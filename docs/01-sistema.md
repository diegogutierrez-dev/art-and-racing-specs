# 01 · Sistema

Las tres piezas y cómo se conectan.

## Los tres niveles

El sistema está compuesto por tres piezas que el comprador atraviesa en orden. Cada una es un nivel de compromiso mayor con la marca.

```
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│   LANDING    │ ──▶ │    TIENDA    │ ──▶ │    PLATAFORMA    │
│ entry point  │     │   Shopify    │     │    lo nuestro    │
└──────────────┘     └──────────────┘     └──────────────────┘
   Contar               Comprar              Coleccionar
   Vender               Pagar                Operar
   Retener              Ordenar
```

### Nivel 1 · Landing

El entry point. No es un folleto.

- Narrativa y drops
- Captura de correo
- Rutas a tienda y a cuenta

Spec: [domains/landing.md](../domains/landing.md)

### Nivel 2 · Tienda (Shopify)

Donde ocurre la transacción. Usamos Shopify como catálogo y checkout, no como plataforma.

- Catálogo y checkout
- Pasarela y monedas
- Webhook de orden

Specs: [domains/tienda.md](../domains/tienda.md), [domains/pagos.md](../domains/pagos.md)

### Nivel 3 · Plataforma

Lo nuestro. Donde vive todo lo que Shopify no hace.

- Cuenta y colección
- Estados del pedido
- Panel de producción
- Álbum (fase 3)

Specs: [domains/pedidos.md](../domains/pedidos.md), [domains/produccion.md](../domains/produccion.md), [domains/cuenta-y-coleccion.md](../domains/cuenta-y-coleccion.md), [domains/album.md](../domains/album.md)

## Cómo se conectan

### Una sola sesión

El comprador atraviesa las tres piezas en una sola sesión y nunca debería sentir que cambió de producto. Esto impone:

- Mismo dominio raíz (la tienda puede vivir en un subdominio, pero con el mismo tema visual).
- Identidad consistente: si el comprador inició sesión en la plataforma, la tienda debe reconocerlo, y viceversa. Cómo se logra depende de la [decisión 0002](../decisions/0002-identidad-del-usuario.md).
- Navegación cruzada: desde cualquier pieza se llega a las otras dos en un clic.

### El único punto de integración obligatorio

**El webhook de orden de Shopify es el único punto de integración obligatorio de la fase uno.** Todo lo demás puede venir después.

```
Shopify ──(orders/paid)──▶ Plataforma
                               │
                               ├─ crea el pedido en estado "pagado"
                               ├─ genera los códigos asociados
                               └─ envía correo de confirmación
```

Ver [domains/pedidos.md](../domains/pedidos.md) para el detalle del estado inicial y [domains/cuenta-y-coleccion.md](../domains/cuenta-y-coleccion.md) para la emisión de códigos.

### Despliegues

Decisión propuesta: landing y plataforma en el mismo despliegue, fuera de Shopify. Shopify solo como catálogo y checkout. Ver [decisión 0003](../decisions/0003-landing-y-plataforma-en-un-despliegue.md).

Esto da dos despliegues en total:

1. **Shopify** (tienda): tema, catálogo, checkout.
2. **Plataforma** (nuestro código): landing, cuenta, colección, panel de producción, álbum.

## Flujo completo del comprador

1. Llega a la landing (orgánico, redes, correo).
2. Ve el drop de la semana. Hace clic en el producto.
3. Cae en la tienda. Agrega al carrito. Paga.
4. Shopify dispara el webhook. La plataforma crea el pedido y los códigos.
5. Recibe correo de confirmación con enlace a su cuenta.
6. Producción avanza el pedido por sus estados. Cada cambio le llega por correo.
7. Recibe el póster con el código impreso.
8. Escanea o entra el código en su cuenta. El póster aparece en su colección.
9. Ve la cuadrícula de la temporada, con los huecos que faltan.
10. Vuelve a la landing para el siguiente drop.

## Preguntas abiertas

- Los "tres niveles" de la plataforma pueden describirse también desde el negocio (por ejemplo, niveles de acceso o de membresía del comprador). Esa descripción está pendiente de recibirse y puede requerir una sección propia. Hasta entonces, este documento usa las tres piezas del sistema como los tres niveles.
