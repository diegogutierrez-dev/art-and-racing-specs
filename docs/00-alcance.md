# 00 · Alcance

Dónde empieza y dónde termina la responsabilidad del equipo técnico.

## Dentro del alcance

| Pieza | Qué incluye |
|---|---|
| Landing page | Entrada a tienda y plataforma. Narrativa, drops, captura de correo |
| Storefront y checkout | Catálogo, carrito y pago en Shopify |
| Pasarela de pago e internacionalización | Integración con pasarela, monedas, precios por región |
| Cuentas, códigos y colección | Identidad del comprador, emisión y canje de códigos, colección por temporada |
| Panel de estados para producción | Interfaz donde el equipo de producción actualiza el estado de cada pedido |

## Fuera del alcance

| Área | Responsable |
|---|---|
| Creación y curaduría del arte | Equipo creativo |
| Impresión, empaque y logística física | Producción |
| Calidad del producto y reimpresiones | Producción |
| Relación con imprentas y transportadoras | Producción |
| Servicio al cliente sobre el pedido físico | Producción / operación |

## La frontera

**La frontera es la compra.** Cuando el pedido entra pagado, hacemos handoff. De ahí en adelante, producción ejecuta y actualiza estados en la plataforma.

Esto significa:

- Construimos el panel donde producción marca *en producción*, *empacado* y *despachado*. No ejecutamos ninguno de los tres.
- Si un pedido se pierde en la transportadora, el sistema debe permitir registrarlo (ver [pedidos.md](../domains/pedidos.md), excepciones), pero resolverlo no es tarea técnica.
- Si una impresión sale mal, el sistema debe permitir marcar una reimpresión. Decidir si se reimprime no es tarea técnica.

## Por qué está escrito

Esta frontera existe para evitar que el equipo tech termine respondiendo por pedidos perdidos o impresiones malas. Debe quedar escrita y aceptada por ambas partes antes de la fase uno.

## Preguntas abiertas

- ¿Quién es la persona de contacto en producción que valida el panel? Sin esa persona, el panel se diseña a ciegas.
- ¿El servicio al cliente sobre el pedido físico tendrá acceso al panel de producción, o solo lectura desde otra vista? Ver [produccion.md](../domains/produccion.md).
