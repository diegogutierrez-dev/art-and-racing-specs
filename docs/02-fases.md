# 02 · Fases

Tres fases, dos decisiones.

## Resumen

| Fase | Nombre | Objetivo | Entregables |
|---|---|---|---|
| F1 | Vender | Que alguien pueda comprar un póster | Landing, tienda, pasarela, webhook de orden, correos de estado |
| F2 | Operar | Que producción trabaje sin hoja de cálculo | Panel de producción, estados completos, cuenta y códigos |
| F3 | Coleccionar | Que el comprador quiera el siguiente drop | Álbum digital y cuadrícula de temporada |

## Antes de F1: dos decisiones bloqueantes

Lo que hay que decidir antes de escribir la primera línea de código.

| ID | Decisión | Qué define | Estado |
|---|---|---|---|
| [0001](../decisions/0001-pasarela-y-jurisdiccion.md) | Pasarela y jurisdicción: Colombia o entidad en EE. UU. | Comisiones y alcance de venta | Pendiente |
| [0002](../decisions/0002-identidad-del-usuario.md) | Identidad del usuario: cuentas de Shopify o auth propio | Alcance de la plataforma | Pendiente |

Cada decisión necesita una persona responsable y una fecha. Sin eso, la fase uno no arranca.

También antes de F1, aunque no bloquea el código: [registro de marca](../domains/marca.md).

## F1 · Vender

**Criterio de salida:** un comprador real paga un póster y recibe un correo de confirmación con su pedido en estado *pagado*.

Incluye:

- Landing con narrativa, drop actual, captura de correo y ruta a tienda.
- Tienda en Shopify con catálogo y checkout.
- Pasarela configurada según la decisión 0001.
- Webhook `orders/paid` recibido por la plataforma y creando el pedido.
- Correo de confirmación y correos de cambio de estado (aunque los cambios sean manuales en esta fase).

No incluye:

- Panel de producción (en F1, producción puede recibir los pedidos por correo o desde el admin de Shopify).
- Canje de códigos (los códigos se **emiten** en F1, pero el canje llega en F2).
- Álbum.

Dominios: [landing](../domains/landing.md), [tienda](../domains/tienda.md), [pagos](../domains/pagos.md), [pedidos](../domains/pedidos.md) (estados automáticos).

## F2 · Operar

**Criterio de salida:** producción actualiza estados desde el panel, el comprador ve su pedido en su cuenta y canjea el código de su primer póster.

Incluye:

- Panel de producción: cola, cambio de estado, guía y transportadora, acciones en lote.
- Estados completos, incluyendo excepciones (cancelación, reimpresión, devolución).
- Cuenta del comprador con estado de pedido, historial y rastreo.
- Endpoint de canje y colección básica (lista de piezas reclamadas).

Dominios: [producción](../domains/produccion.md), [pedidos](../domains/pedidos.md) (estados manuales y excepciones), [cuenta y colección](../domains/cuenta-y-coleccion.md).

## F3 · Coleccionar

**Criterio de salida:** el comprador abre su álbum en el teléfono, ve su póster con profundidad y efecto holográfico, y ve la cuadrícula de la temporada con los huecos.

Incluye:

- Álbum digital: capas, shader holográfico, orientación del dispositivo.
- Cuadrícula de temporada con huecos visibles.

No bloquea el lanzamiento. Los códigos se emiten desde el drop uno; el álbum puede llegar en el drop seis sin perder nada.

Dominio: [álbum](../domains/album.md).

## Dependencias entre fases

```
Decisión 0001 ──┐
                ├──▶ F1 Vender ──▶ F2 Operar ──▶ F3 Coleccionar
Decisión 0002 ──┘         │
                          └── códigos emitidos desde aquí
```

- F2 depende de F1 porque el panel opera sobre pedidos reales.
- F3 depende de F2 porque el álbum muestra piezas canjeadas.
- La decisión 0002 afecta F1 (identidad en checkout) y F2 (cuenta), por eso es bloqueante aunque la cuenta llegue en F2.
