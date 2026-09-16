# Stack técnico · consejos

> Esto es una guía, no un spec. Los specs describen comportamiento y dominio; el stack es el medio y puede cambiar sin que cambie nada en [domains/](../domains/). Si algo de aquí contradice un spec, gana el spec.

## Resumen

| Capa | Recomendación | Por qué |
|---|---|---|
| Framework | Next.js (App Router) con TypeScript | Un solo despliegue para landing, cuenta, panel y álbum. Server Components para la landing, Server Actions para el panel |
| Hosting | Vercel | Cero configuración para Next.js, previews por PR, Fluid Compute para webhooks y cron |
| Estilos | Tailwind CSS v4 + shadcn/ui | Velocidad en el panel (tablas, formularios) sin sacrificar control en la landing |
| Base de datos | Postgres gestionado (Neon vía Vercel Marketplace) + Drizzle ORM | Tres tablas y un log de eventos. Postgres sobra y no estorba |
| Correo transaccional | Resend (vía Marketplace) con plantillas en React Email | Cinco correos por pedido, disparados por evento |
| Tienda | Shopify Basic | Catálogo, checkout, impuestos. Ver [tienda.md](../domains/tienda.md) |
| Auth del comprador | Depende de la [decisión 0002](../decisions/0002-identidad-del-usuario.md) | Shopify Customer Account API, o Better Auth / Clerk con magic link |
| Auth de producción | Separada del comprador. Clerk o Better Auth con roles | Nunca la misma tabla que el comprador |
| Activos del álbum | Vercel Blob | Capas por pieza, servidas con caché largo |
| 3D y shaders | Three.js vía react-three-fiber + drei | Solo en F3. Carga diferida en la ruta del álbum |
| Configuración | `vercel.ts` con `@vercel/config` | Tipado, crons y headers en un solo archivo |

## Principios técnicos

1. **Un despliegue.** Landing, cuenta, panel y álbum viven en el mismo proyecto Next.js. Ver [decisión 0003](../decisions/0003-landing-y-plataforma-en-un-despliegue.md).
2. **Node.js, no Edge.** Webhooks, Server Actions y cron corren en el runtime Node.js por defecto (Fluid Compute). No usar `runtime = 'edge'`; no aporta nada aquí y quita compatibilidad.
3. **Server-first.** La landing y la cuenta son Server Components. El cliente solo para lo interactivo: canje, panel, álbum.
4. **Marketplace antes que infraestructura propia.** Postgres, correo, auth y monitoreo se aprovisionan desde el Vercel Marketplace. No se monta nada a mano hasta que un drop real lo justifique.
5. **Tipos desde la base de datos.** Drizzle genera los tipos; los enums de estado viven en un solo lugar y se importan en todas partes.

## Estructura sugerida del proyecto

```
app/
  (landing)/            # nivel 1 · público
    page.tsx
    drops/[slug]/
  (cuenta)/             # nivel 3 · comprador
    cuenta/
      pedidos/
      coleccion/
      canjear/
  (produccion)/         # nivel 3 · interno, ruta protegida
    panel/
  (album)/              # nivel 3 · F3, carga diferida
    album/[temporada]/
  api/
    webhooks/shopify/   # orders/paid, orders/cancelled, refunds/create
    canje/              # endpoint de canje
db/
  schema.ts             # pedidos, eventos_estado, codigos, drops, piezas
  migrations/
lib/
  shopify/              # cliente Storefront API, verificación HMAC
  estados/              # máquina de transiciones válidas
  correo/               # plantillas y envío
emails/                 # React Email
vercel.ts
```

Los grupos de rutas siguen los tres niveles de [docs/01-sistema.md](../docs/01-sistema.md).

## Integración con Shopify

| Necesidad | Herramienta | Nota |
|---|---|---|
| Recibir `orders/paid` | Route Handler en `app/api/webhooks/shopify` | Verificar HMAC con el secreto del webhook antes de leer el body. Responder 200 rápido, procesar dentro del mismo handler (Fluid Compute lo permite) |
| Idempotencia | Índice único en `shopify_order_id` | Un segundo webhook hace `INSERT ... ON CONFLICT DO NOTHING` |
| Producto del drop en la landing | Storefront API (GraphQL) | Cachear con `use cache` y revalidar por tag al cambiar el drop |
| Identidad (si decisión 0002 = A) | Customer Account API | OAuth con PKCE. La sesión de la plataforma guarda el token |
| Sincronizar guía (opcional) | Admin API, `fulfillmentCreateV2` | Solo como espejo de salida al marcar *despachado* |
| Tema de la tienda | Tema de Shopify con los mismos tokens de diseño | Mismas fuentes, colores y espaciado que la landing |

## Base de datos

Esquema mínimo, uno a uno con los dominios:

| Tabla | Dominio | Nota |
|---|---|---|
| `drops` | Landing, colección | slug, fecha, temporada, posición en cuadrícula |
| `piezas` | Colección, álbum | drop, ilustrador, imagen, capas (URLs en Blob) |
| `pedidos` | Pedidos | `shopify_order_id` único, `estado_actual` proyectado |
| `eventos_estado` | Pedidos | Log. Ver [decisión 0004](../decisions/0004-estados-como-eventos.md) |
| `codigos` | Cuenta y colección | código único, pedido, pieza, estado, cuenta |
| `cuentas` | Cuenta | Solo si decisión 0002 = B. Si A, referencia al customer de Shopify |
| `usuarios_produccion` | Producción | Roles `produccion` y `admin` |
| `suscriptores` | Landing | Correo capturado, o delegar a Resend Audiences |

La máquina de transiciones válidas vive en código (`lib/estados`), no en la base de datos. Un `CHECK` en `eventos_estado.estado` limita los valores al enum.

## Correos

Resend con React Email. Una plantilla por estado que notifica ([pedidos.md](../domains/pedidos.md), sección Notificaciones). El envío se dispara al insertar el evento de estado. Si el envío falla, se reintenta; el estado no se revierte.

Para el remitente, dominio propio verificado en Resend antes del drop uno.

## Canje y protección

- Endpoint de canje como Server Action o Route Handler autenticado.
- Rate limit por cuenta y por IP. Vercel Firewall con regla de rate limit sobre la ruta de canje, o Upstash Redis vía Marketplace si se quiere lógica propia.
- BotID en el formulario de canje si aparece abuso. No antes.
- El código nunca viaja en query string en enlaces de correo; el QR apunta a `/canjear?c=CODIGO` y la página lo lee del lado del servidor y lo pasa al formulario.

## Detección de *entregado*

F2: manual desde el panel.
Después: un cron (`vercel.ts` → `crons`) que consulta la API de la transportadora para pedidos en *despachado* y marca *entregado*. Cada transportadora es un adaptador en `lib/transportadoras`.

## Álbum (F3)

- `react-three-fiber` + `drei` para el canvas. Shader holográfico propio en GLSL.
- Device Orientation API con permiso pedido en contexto (iOS lo exige en gesto de usuario).
- Capas como texturas comprimidas (KTX2 o WebP según soporte) en Vercel Blob.
- La ruta del álbum es un grupo aparte con `dynamic import` para que Three.js no entre en el bundle de la landing ni del panel.
- Degradación: sin WebGL o sin permiso, imagen plana con CSS.

## Entornos y variables

| Variable | Uso |
|---|---|
| `DATABASE_URL` | Postgres. La inyecta el Marketplace |
| `SHOPIFY_STORE_DOMAIN` | Tienda |
| `SHOPIFY_STOREFRONT_TOKEN` | Storefront API |
| `SHOPIFY_WEBHOOK_SECRET` | Verificación HMAC |
| `SHOPIFY_ADMIN_TOKEN` | Solo si se sincroniza fulfillment |
| `RESEND_API_KEY` | Correo. La inyecta el Marketplace |
| `BLOB_READ_WRITE_TOKEN` | Activos del álbum |
| `AUTH_*` | Según proveedor de auth elegido |

Gestión con `vercel env`. Tres entornos: development, preview, production. La tienda de preview apunta a una tienda de desarrollo de Shopify, nunca a la real.

## Observabilidad

- Vercel Logs y Observability para funciones y webhooks.
- Alertas mínimas: webhook que responde distinto de 200, correo que falla tres veces, pedido con más de N días en *despachado*.
- Nada de dashboards de negocio en la plataforma. Para eso está el admin de Shopify.

## Lo que no se usa

| No | Por qué |
|---|---|
| Edge runtime | Sin beneficio aquí y con restricciones de compatibilidad |
| App nativa | Todo es web. Ver [MANIFIESTO.md](../MANIFIESTO.md) |
| Blockchain / NFTs | El código es una fila en una tabla |
| Microservicios | Tres tablas y un log no justifican más de un despliegue |
| CMS externo para la landing | Los drops y piezas viven en la misma base de datos. Si el equipo creativo necesita editar sin código, se evalúa en F2 |
| Redis como base de datos | Solo para rate limit si hace falta |

## Cuándo revisar este documento

- Al tomar la [decisión 0002](../decisions/0002-identidad-del-usuario.md): fija el proveedor de auth.
- Al cerrar F1: confirmar que Shopify Basic y Vercel Hobby/Pro siguen siendo suficientes.
- Antes de F3: revisar formatos de textura y soporte de Device Orientation en los navegadores del momento.
