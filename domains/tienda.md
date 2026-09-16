# Dominio · Tienda

**Fase:** F1
**Pieza:** Nivel 2, Shopify
**Principio rector:** Shopify es catálogo y checkout, no la plataforma

## Propósito

Donde ocurre la transacción. Shopify resuelve catálogo, carrito, checkout, impuestos y el admin de pedidos. No intentamos replicar nada de eso.

## Responsabilidades

- Catálogo de productos (un producto por póster, variantes si hay tamaños o ediciones).
- Carrito y checkout.
- Cobro a través de la pasarela definida en la [decisión 0001](../decisions/0001-pasarela-y-jurisdiccion.md).
- Multi-moneda y precios por región (Shopify Markets) si se toma la ruta B.
- Disparar el webhook `orders/paid` hacia la plataforma.
- Fuente de verdad del inventario y del precio.

## No es responsabilidad de la tienda

- Estados de producción. Shopify tiene su propio concepto de *fulfillment*, pero el ciclo de estados vive en la plataforma. Ver [pedidos.md](pedidos.md).
- Códigos ni colección. Shopify no sabe que existen.
- Narrativa. La tienda vende; la landing cuenta.

## Reglas

1. **El webhook de orden es obligatorio.** Es el único punto de integración de F1. Sin él, la plataforma no sabe que hubo una venta.
2. **Shopify es fuente de verdad del pedido comercial.** Monto, ítems, dirección, cliente. La plataforma replica lo que necesita y guarda el ID de Shopify como referencia.
3. **La plataforma es fuente de verdad del estado operativo.** Shopify no se actualiza con los estados de producción salvo que se decida sincronizar el *fulfillment* al despachar (pregunta abierta).
4. **El tema visual de la tienda es el mismo de la landing.** El comprador no debe sentir que cambió de producto.

## Integraciones

| Integración | Dirección | Uso | Fase |
|---|---|---|---|
| Webhook `orders/paid` | Shopify → Plataforma | Crear pedido y emitir códigos | F1 |
| Storefront API | Plataforma → Shopify | Mostrar producto del drop en la landing | F1 |
| Webhook `orders/cancelled` | Shopify → Plataforma | Marcar pedido como cancelado | F2 |
| Webhook `refunds/create` | Shopify → Plataforma | Registrar devolución | F2 |
| Admin API (fulfillment) | Plataforma → Shopify | Sincronizar guía al despachar | F2, opcional |
| Customer Account API | Plataforma ↔ Shopify | Identidad si se elige cuentas de Shopify | Depende de 0002 |

## Plan de Shopify

Basic (39 USD/mes, 29 con pago anual) es suficiente para F1. El recargo por pasarela externa depende del plan: 2% Basic, 1% Grow, 0.6% Advanced. Ver [pagos.md](pagos.md) y [costos.md](costos.md).

## Criterios de aceptación (F1)

- [ ] Un comprador completa un pago de prueba y la plataforma recibe el webhook con los datos del pedido.
- [ ] El webhook es idempotente: recibir el mismo evento dos veces no crea dos pedidos.
- [ ] La firma HMAC del webhook se verifica antes de procesar.
- [ ] El producto del drop actual se muestra en la landing con precio y disponibilidad reales.

## Preguntas abiertas

- ¿Sincronizar el *fulfillment* de Shopify cuando producción marca *despachado*? Da consistencia en el admin de Shopify y correos nativos, pero duplica una fuente de estado. Propuesta: sí, pero solo como espejo de salida, nunca como entrada.
- ¿Una tienda o dos? Si se quiere cobrar en Colombia con PSE/Nequi y en el exterior con tarjeta, puede requerir dos storefronts. Ver [pagos.md](pagos.md).
- ¿Apps de Shopify necesarias? Presupuesto de 0 a 100 USD/mes según lo que falte.
