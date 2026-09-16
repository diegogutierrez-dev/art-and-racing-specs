# 0005 · El álbum es F3 y no bloquea el lanzamiento

**Estado:** `aceptada`
**Responsable:** equipo técnico y negocio
**Fecha límite:** —
**Bloquea:** —

## Contexto

El álbum digital (profundidad, holográfico, cuadrícula) es el diferenciador del producto. La tentación es construirlo primero. Detalle en [domains/album.md](../domains/album.md).

## Decisión

El álbum es fase 3. Los códigos se emiten desde el drop uno y la colección básica llega en F2. El álbum puede llegar en el drop seis sin perder nada, porque cada póster ya tiene su código y cada código ya está en su cuenta.

Construirlo antes de vender el primer póster sería el error clásico.

## Consecuencias

- F1 y F2 no dependen de Three.js, shaders ni Device Orientation.
- El pipeline de arte debe entregar capas desde el drop uno, aunque no se usen hasta F3. Si no las entrega, el álbum muestra la pieza plana.
- La colección en F2 es una cuadrícula simple. El álbum la reemplaza sin cambiar el modelo de datos.
