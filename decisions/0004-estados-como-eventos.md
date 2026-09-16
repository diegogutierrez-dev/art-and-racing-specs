# 0004 · El estado del pedido es un log de eventos, no un campo

**Estado:** `propuesta`
**Responsable:** equipo técnico
**Fecha límite:** antes de F1
**Bloquea:** F1

## Contexto

El pedido pasa por siete estados y tres excepciones. Producción los marca, el comprador los ve, los correos los reflejan. Hay que decidir si el estado es un campo que se sobreescribe o un historial de eventos. Detalle en [domains/pedidos.md](../domains/pedidos.md).

## Opciones

### Un campo `estado` en el pedido

- Simple. Una columna.
- Se pierde el historial: quién marcó qué y cuándo. Para auditar una reimpresión o una queja no hay rastro.

### Un log de eventos de estado

- Una tabla `evento_estado` con pedido, estado, autor, nota y fecha. El estado actual es el último evento (o una columna proyectada).
- Historial completo. Auditoría gratis. Las excepciones son eventos como cualquier otro.
- Un poco más de código para proyectar el estado actual.

## Decisión propuesta

Log de eventos. El pedido tiene `estado_actual` como proyección del último evento, para consultas rápidas, pero la verdad está en el log.

## Consecuencias

- Nunca se borra un estado; se agrega uno.
- El panel de producción muestra el historial por pedido.
- Las excepciones (cancelación, reimpresión, devolución) son estados del mismo log, no tablas aparte.
- Los correos se disparan al insertar un evento, no al cambiar un campo.
