# 0003 · Landing y plataforma en un mismo despliegue, fuera de Shopify

**Estado:** `propuesta`
**Responsable:** equipo técnico
**Fecha límite:** antes de F1
**Bloquea:** F1

## Contexto

La landing puede vivir dentro de Shopify (como página del tema) o aparte, en el mismo despliegue que la plataforma. Detalle en [domains/landing.md](../domains/landing.md).

## Opciones

### Dentro de Shopify

- Más rápido y sin costo extra.
- El diseño queda limitado por el tema.
- La plataforma vive aparte de todos modos, así que hay dos despliegues igual: Shopify (tienda + landing) y plataforma.

### Aparte, con la plataforma

- Control total del diseño.
- Es donde vive la plataforma: misma sesión, mismo código, misma identidad visual.
- Hay que mantener dos despliegues: Shopify (solo tienda) y plataforma (landing + cuenta + panel + álbum).

## Decisión propuesta

Landing y plataforma en el mismo despliegue, fuera de Shopify. Shopify solo como catálogo y checkout.

Es discutible según qué tan rápido se quiera salir. Si F1 tiene una fecha muy corta, la landing dentro de Shopify es aceptable como puente, con la condición de migrarla en F2.

## Consecuencias

- Dos despliegues: Shopify y plataforma. Ver [docs/01-sistema.md](../docs/01-sistema.md).
- El producto del drop se muestra en la landing vía Storefront API.
- La landing puede saber si el comprador tiene sesión en la plataforma.
- El stack de la plataforma (Next.js en Vercel) sirve también la landing. Ver [tech/stack.md](../tech/stack.md).
