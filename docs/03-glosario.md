# 03 · Glosario

Vocabulario compartido entre negocio y desarrollo. Si un término aparece en un spec, su significado es el de aquí.

| Término | Definición |
|---|---|
| **Drop** | Lanzamiento de uno o varios pósters en una fecha. La unidad de tiempo del negocio. "El drop de la semana". |
| **Temporada** | Conjunto de drops que forman una colección completa. La cuadrícula del álbum es una temporada. |
| **Tiraje** | Cantidad de unidades impresas de un póster en un drop. Producción organiza la cola por drop y por tiraje. |
| **Póster / afiche** | El producto físico. Cada unidad trae un código único impreso. |
| **Código** | Identificador único impreso en cada póster. Se emite al pagar, se activa al entregar, se canjea en la cuenta. Es una fila en una tabla, no un token. |
| **Canje** | Acción del comprador de asociar un código a su cuenta. Requiere que el código esté activo. |
| **Colección** | Conjunto de piezas canjeadas por una cuenta, organizadas por temporada. |
| **Hueco** | Posición de la cuadrícula de temporada que la cuenta todavía no tiene. El hueco es lo que empuja al siguiente drop. |
| **Álbum** | Vista de fase 3 de la colección: pieza con profundidad y holográfico, más la cuadrícula de temporada. |
| **Pedido** | Una orden pagada en Shopify, replicada en la plataforma con su estado. Un pedido puede tener varios pósters y por tanto varios códigos. |
| **Estado** | Posición del pedido en su ciclo de vida. Ver [pedidos.md](../domains/pedidos.md). |
| **Handoff** | Momento en que la responsabilidad pasa de un equipo a otro. Hay dos: plataforma → producción (al entrar en cola) y producción → plataforma (al despachar). |
| **Panel de producción** | Vista interna donde producción cambia estados. Una tabla con filtros y botones. |
| **Vista del comprador** | Vista dentro de la cuenta donde el comprador ve el estado de su pedido. |
| **Landing** | El entry point del sistema. Página pública con narrativa, drop actual y rutas a tienda y cuenta. |
| **Tienda / storefront** | Shopify: catálogo, carrito y checkout. |
| **Plataforma** | Nuestro código: landing, cuenta, colección, panel de producción, álbum. |
| **Pasarela** | Proveedor que procesa el pago. Local (Wompi, PayU, ePayco, Mercado Pago) o Shopify Payments / Stripe vía entidad en EE. UU. |
| **Ruta A / Ruta B** | Las dos opciones de pasarela y jurisdicción. Ver [pagos.md](../domains/pagos.md) y [decisión 0001](../decisions/0001-pasarela-y-jurisdiccion.md). |
| **Webhook de orden** | Evento `orders/paid` que Shopify envía a la plataforma. El único punto de integración obligatorio de F1. |
| **Guía** | Número de rastreo de la transportadora. Producción lo captura al despachar. |
| **ADR** | Architecture Decision Record. Documento que registra una decisión, su contexto y sus consecuencias. Viven en [decisions/](../decisions/). |
