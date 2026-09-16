# Decisiones

Registro de decisiones de arquitectura (ADR). Cada decisión es un archivo numerado. Una decisión no se edita una vez aceptada: si cambia, se crea una nueva que la reemplaza.

## Estados

| Estado | Significado |
|---|---|
| `pendiente` | Bloquea trabajo. Tiene responsable y fecha límite |
| `propuesta` | Hay una recomendación, falta aceptarla |
| `aceptada` | Vigente. Los specs la asumen |
| `rechazada` | Se evaluó y no se tomó. Se conserva el porqué |
| `reemplazada` | Otra decisión la sustituye. Se enlaza la nueva |

## Índice

| ID | Decisión | Estado | Bloquea |
|---|---|---|---|
| [0001](0001-pasarela-y-jurisdiccion.md) | Pasarela y jurisdicción | `pendiente` | F1 |
| [0002](0002-identidad-del-usuario.md) | Identidad del usuario | `pendiente` | F1 |
| [0003](0003-landing-y-plataforma-en-un-despliegue.md) | Landing y plataforma en un mismo despliegue, fuera de Shopify | `propuesta` | F1 |
| [0004](0004-estados-como-eventos.md) | El estado del pedido es un log de eventos, no un campo | `propuesta` | F1 |
| [0005](0005-album-no-bloquea-lanzamiento.md) | El álbum es F3 y no bloquea el lanzamiento | `aceptada` | — |

## Plantilla

```markdown
# NNNN · Título

**Estado:** pendiente | propuesta | aceptada | rechazada | reemplazada
**Responsable:** nombre
**Fecha límite:** AAAA-MM-DD
**Bloquea:** F1 | F2 | F3 | —

## Contexto
## Opciones
## Decisión
## Consecuencias
```
