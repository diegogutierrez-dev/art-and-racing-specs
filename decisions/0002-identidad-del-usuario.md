# 0002 · Identidad del usuario

**Estado:** `pendiente`
**Responsable:** _por asignar_
**Fecha límite:** _por asignar_
**Bloquea:** F1

## Contexto

El comprador necesita una cuenta para ver sus pedidos, canjear códigos y ver su colección. Esa identidad puede venir de Shopify (cuentas de cliente) o ser propia de la plataforma. Define el alcance de todo lo demás y no se puede cambiar barato después. Detalle en [domains/cuenta-y-coleccion.md](../domains/cuenta-y-coleccion.md).

## Opciones

### A · Cuentas de cliente de Shopify

- Shopify es la única fuente de identidad. La plataforma usa la Customer Account API para autenticar y leer el cliente.
- El pedido ya viene asociado al cliente desde el webhook. No hay que reconciliar correos.
- La fase uno se acorta semanas.
- Dependencia fuerte de Shopify: si un día se cambia de tienda, se migra la identidad.
- Menos control sobre el flujo de entrada (login de Shopify, con su UX).

### B · Auth propio

- La plataforma tiene su propia identidad (correo + magic link, o proveedor de auth).
- Control total del flujo, del diseño y de los datos.
- Hay que sincronizar dos fuentes de identidad: el cliente de Shopify (del webhook) y la cuenta de la plataforma. La reconciliación es por correo, con sus casos borde (correo distinto en checkout, mayúsculas, alias).
- Más semanas en F1 y F2.

## Criterio para decidir

- Si la prioridad es salir rápido con F1 y F2, A.
- Si la prioridad es que la plataforma sea independiente de Shopify a largo plazo, B.
- Un camino intermedio (A ahora, B después) es posible pero cuesta una migración de identidades. Hay que aceptar ese costo conscientemente.

## Decisión

_Pendiente._

## Consecuencias

_Se completan al decidir._ Afectan: [landing.md](../domains/landing.md) (acceso a cuenta), [tienda.md](../domains/tienda.md) (sesión compartida), [cuenta-y-coleccion.md](../domains/cuenta-y-coleccion.md), [tech/stack.md](../tech/stack.md) (proveedor de auth).
