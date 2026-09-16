# Dominio · Pedidos

**Fase:** F1 (estados automáticos) → F2 (estados manuales y excepciones)
**Pieza:** Nivel 3, plataforma
**Principio rector:** un solo registro de estado

## Propósito

Modelar el ciclo de vida de un pedido desde que se paga hasta que la colección queda activa. Un solo registro de estado alimenta las dos vistas (producción y comprador). Nadie actualiza nada dos veces y no hay hoja de cálculo paralela.

## Ciclo de estados

```
 PLATAFORMA · AUTO          PRODUCCIÓN · MANUAL              PLATAFORMA · AUTO
┌─────────┐ ┌─────────┐   ┌──────────────┐ ┌──────────┐ ┌────────────┐   ┌───────────┐ ┌──────────────────┐
│01 Pagado│▶│02 En    │──▶│03 En         │▶│04        │▶│05          │──▶│06         │▶│07 Colección      │
│         │ │   cola  │   │   producción │ │ Empacado │ │ Despachado │   │ Entregado │ │   activa         │
└─────────┘ └─────────┘   └──────────────┘ └──────────┘ └────────────┘   └───────────┘ └──────────────────┘
                       handoff                                        handoff
```

| # | Estado | Quién lo marca | Disparador | Efecto |
|---|---|---|---|---|
| 01 | **Pagado** | Plataforma | Webhook `orders/paid` de Shopify | Se crea el pedido, se emiten los códigos (inactivos), correo de confirmación |
| 02 | **En cola** | Plataforma | Automático tras 01 | Listo para producir. Aparece en el panel de producción |
| 03 | **En producción** | Producción | Clic en el panel | El equipo marca inicio |
| 04 | **Empacado** | Producción | Clic en el panel | Listo para despacho |
| 05 | **Despachado** | Producción | Clic en el panel + número de guía + transportadora | Correo con guía y enlace de rastreo |
| 06 | **Entregado** | Plataforma | Automático (rastreo) o manual si no hay integración | Cierra el pedido. Los códigos pasan a activos |
| 07 | **Colección activa** | Plataforma | Automático tras 06 | El código es canjeable en la cuenta |

### Los dos handoffs

- **02 → 03:** la plataforma entrega a producción. Construimos el panel donde producción marca 03, 04 y 05. No ejecutamos ninguno de los tres.
- **05 → 06:** producción devuelve a la plataforma. Desde el despacho, la plataforma vuelve a ser responsable de informar.

## Excepciones

Las excepciones se modelan desde el día uno. Cada una es un estado, no un caso especial.

| Estado | Desde | Quién lo marca | Efecto |
|---|---|---|---|
| **Cancelado** | 01, 02 (antes de producir) | Plataforma vía webhook `orders/cancelled`, o producción desde el panel | Códigos anulados. Reembolso lo gestiona Shopify |
| **Reimpresión** | 03, 04, 05, 06 | Producción | Vuelve a 03 con marca de reimpresión. Los códigos originales se conservan (el póster nuevo lleva el mismo código) o se reemiten (decisión de producción por caso) |
| **Devolución** | 06 | Producción vía Shopify `refunds/create` o manual | Códigos anulados si ya estaban activos. El póster sale de la colección |

Reglas de excepción:

1. Un pedido en excepción conserva su historial. Nunca se borra un estado, se agrega uno.
2. Cancelar un pedido con códigos ya canjeados no es posible desde el panel. Requiere devolución.
3. Reimpresión no cambia el estado visible al comprador salvo que se decida comunicarlo (pregunta abierta).

## Entidades

### Pedido

| Campo | Tipo | Origen | Nota |
|---|---|---|---|
| id | identificador | Plataforma | |
| shopify_order_id | referencia | Shopify | Único. Base de la idempotencia del webhook |
| numero | texto | Shopify | El número visible al comprador (#1001) |
| comprador | referencia a cuenta o correo | Shopify | Depende de la [decisión 0002](../decisions/0002-identidad-del-usuario.md) |
| items | lista | Shopify | Producto, variante, cantidad. Cada unidad genera un código |
| monto | número + moneda | Shopify | Sin conversión |
| estado_actual | enum | Plataforma | Derivado del último evento de estado |
| drop | referencia | Plataforma | Para la cola por drop |
| guia | texto | Producción | Al despachar |
| transportadora | texto o enum | Producción | Al despachar |
| creado_en | fecha | Plataforma | |

### Evento de estado

El estado no se sobreescribe. Se registra un evento por cambio y `estado_actual` es el último.

| Campo | Tipo | Nota |
|---|---|---|
| pedido_id | referencia | |
| estado | enum | Uno de los estados de arriba |
| marcado_por | referencia a usuario o `sistema` | Auditoría |
| nota | texto opcional | Motivo de excepción, número de guía, etc. |
| marcado_en | fecha | |

## Notificaciones

Correo automático en cada cambio de estado visible al comprador.

| Estado | Correo | Contenido mínimo |
|---|---|---|
| 01 Pagado | Confirmación | Número de pedido, ítems, enlace a la cuenta |
| 03 En producción | Opcional | "Tu póster está en producción" |
| 05 Despachado | Sí | Guía, transportadora, enlace de rastreo |
| 06 Entregado | Sí | "Ya puedes reclamar tu código" con enlace a canje |
| Cancelado | Sí | Confirmación de cancelación |

Los estados 02 y 04 son internos y no generan correo.

## Reglas

1. **Idempotencia.** Recibir el mismo webhook dos veces no crea dos pedidos ni dos eventos.
2. **Un evento por cambio.** El historial es la fuente de verdad. `estado_actual` es una proyección.
3. **Transiciones válidas.** El sistema rechaza transiciones que no estén en la tabla (por ejemplo, de 01 a 05).
4. **Los correos se disparan por evento**, no por polling. Si el correo falla, el estado no se revierte; se reintenta el correo.
5. **El panel no ve datos de pago.**

## Criterios de aceptación

F1:
- [ ] Un pago en Shopify crea un pedido en 01 y lo mueve a 02 automáticamente.
- [ ] El comprador recibe correo de confirmación.
- [ ] El mismo webhook recibido dos veces no duplica nada.

F2:
- [ ] Producción mueve un pedido por 03, 04 y 05 desde el panel.
- [ ] Al despachar, el comprador recibe guía y enlace de rastreo.
- [ ] Al entregar, los códigos del pedido pasan a activos.
- [ ] Cancelación, reimpresión y devolución se registran con su nota y su autor.

## Preguntas abiertas

- ¿Cómo se detecta *entregado*? Integración con la transportadora, marcado manual por producción, o confirmación del comprador. Propuesta para F2: manual por producción, con integración después.
- ¿La reimpresión se comunica al comprador? Negocio decide.
- ¿Qué pasa con un pedido que lleva N días en 05 sin llegar a 06? Puede requerir un estado *incidencia* o una alerta en el panel.
