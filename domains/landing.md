# Dominio · Landing

**Fase:** F1
**Pieza:** Nivel 1, el entry point
**Principio rector:** es el entry point, no un folleto

## Propósito

La landing tiene que resolver tres trabajos distintos con un solo diseño. Si solo cuenta la historia, no sirve.

| Trabajo | Qué hace | Hacia dónde lleva |
|---|---|---|
| **Contar** | El drop de la semana, los ilustradores, por qué esto existe | Se queda en la landing |
| **Vender** | Ruta directa al producto sin fricción. El checkout está a un clic | Tienda |
| **Retener** | Entrada a la cuenta: ver mi colección, canjear un código | Plataforma |

## Responsabilidades

- Mostrar el drop actual con ruta directa al producto en la tienda.
- Contar la narrativa: ilustradores, temporada, por qué existe el proyecto.
- Capturar correo para avisar del siguiente drop.
- Dar acceso a la cuenta (entrar, ver colección, canjear código).
- Mostrar drops anteriores (agotados o no) como parte de la narrativa de temporada.

## No es responsabilidad de la landing

- Catálogo completo ni carrito. Eso es la tienda.
- Autenticación. La landing enlaza a la cuenta; la plataforma autentica.
- Contenido editorial extenso. Una landing, no un blog.

## Reglas

1. **Un clic al producto.** Desde cualquier vista de la landing, el producto del drop actual está a un clic. El checkout, a dos.
2. **La captura de correo no bloquea nada.** Es una invitación, no un muro.
3. **El drop actual es dinámico.** No se despliega código para cambiar de drop. El drop actual se define por datos (fecha de publicación, estado del producto en Shopify, o un campo de la plataforma).
4. **Misma sesión.** Si el comprador tiene sesión en la plataforma, la landing lo sabe y muestra su acceso a la cuenta, no el botón de entrar.

## Entidades

| Entidad | Origen | Uso en la landing |
|---|---|---|
| Drop | Plataforma | Cuál es el actual, cuáles pasaron, cuál viene |
| Producto | Shopify | Imagen, precio, disponibilidad, enlace a compra |
| Ilustrador | Plataforma | Nombre, bio corta, piezas en la temporada |
| Suscriptor | Plataforma (o proveedor de correo) | Correo capturado |

## Decisión asociada

[0003 · Landing y plataforma en un mismo despliegue, fuera de Shopify](../decisions/0003-landing-y-plataforma-en-un-despliegue.md). Dentro de Shopify es más rápido y sin costo extra, pero el diseño queda limitado por el tema. Aparte da control total y es donde vive la plataforma, a cambio de mantener dos despliegues.

## Criterios de aceptación (F1)

- [ ] Un visitante nuevo ve el drop actual y llega al producto en la tienda con un clic.
- [ ] Un visitante puede dejar su correo y recibe confirmación.
- [ ] Un comprador con cuenta ve la ruta a su colección desde la landing.
- [ ] Cambiar el drop actual no requiere despliegue.

## Preguntas abiertas

- ¿Los drops pasados se muestran como archivo navegable o solo como cuadrícula de temporada? Afecta cuánto de la narrativa vive en la landing vs. en el álbum.
- ¿Captura de correo con proveedor externo (lista de Shopify, Resend, etc.) o tabla propia? Ver [tech/stack.md](../tech/stack.md).
