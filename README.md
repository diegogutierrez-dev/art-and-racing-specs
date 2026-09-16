# Afiches F1 · Specs

Especificaciones, dominios y manifiesto de la capa técnica de Afiches F1: landing, tienda y plataforma de colección.

Este repositorio no contiene código. Solo Markdown. Es la fuente de verdad sobre **qué** se construye y **por qué**. El **cómo** vive en el repositorio de la aplicación, que se subordina a lo que está aquí.

## Empieza por aquí

| Documento | Qué responde |
|---|---|
| [MANIFIESTO.md](MANIFIESTO.md) | Qué somos, qué no somos, y los principios que ordenan todo lo demás |
| [docs/00-alcance.md](docs/00-alcance.md) | Dónde empieza y dónde termina nuestra responsabilidad |
| [docs/01-sistema.md](docs/01-sistema.md) | Las tres piezas del sistema y cómo se conectan |
| [docs/02-fases.md](docs/02-fases.md) | Plan en tres fases: vender, operar, coleccionar |
| [docs/03-glosario.md](docs/03-glosario.md) | Vocabulario compartido entre negocio y desarrollo |

## Dominios

Cada dominio es un área del sistema con su propio vocabulario, reglas y responsabilidades. Un dominio no debe saber más de otro dominio que lo que su spec declara como interfaz.

| Dominio | Fase | Descripción |
|---|---|---|
| [Landing](domains/landing.md) | F1 | El entry point. Contar, vender, retener |
| [Tienda](domains/tienda.md) | F1 | Catálogo y checkout en Shopify |
| [Pagos](domains/pagos.md) | F1 | Pasarela, monedas y jurisdicción |
| [Pedidos](domains/pedidos.md) | F1 → F2 | Estados del pedido, handoff y excepciones |
| [Producción](domains/produccion.md) | F2 | Panel de producción sobre el mismo registro de estado |
| [Cuenta y colección](domains/cuenta-y-coleccion.md) | F2 | Identidad, códigos, canje y colección |
| [Álbum](domains/album.md) | F3 | El álbum digital con profundidad y holográfico |
| [Marca](domains/marca.md) | Previo a F1 | Registro de marca en Colombia y EE. UU. |
| [Costos](domains/costos.md) | Transversal | Qué cuesta la capa técnica |

## Decisiones

Las decisiones de arquitectura que definen el alcance viven en [decisions/](decisions/) como ADRs (Architecture Decision Records). Dos están **pendientes y bloquean la fase uno**:

- [0001 · Pasarela y jurisdicción](decisions/0001-pasarela-y-jurisdiccion.md)
- [0002 · Identidad del usuario](decisions/0002-identidad-del-usuario.md)

## Stack técnico

[tech/stack.md](tech/stack.md) recoge el ecosistema recomendado (Next.js, Vercel, Tailwind, Shopify) a modo de consejo. No es un spec. Puede cambiar sin que cambie nada de lo anterior.

## Cómo contribuir a este repositorio

- Todo cambio de alcance pasa primero por el [MANIFIESTO.md](MANIFIESTO.md). Si el manifiesto no lo permite, no entra.
- Una decisión nueva se registra como ADR en [decisions/](decisions/) con su estado (`propuesta`, `aceptada`, `rechazada`, `reemplazada`).
- Cada dominio tiene una sección **Preguntas abiertas**. Si algo no está resuelto, va ahí, no en un comentario de código.
- Las cifras (comisiones, tasas, precios) llevan fecha de verificación. Si la fecha tiene más de seis meses, hay que reconfirmar antes de usarla.

## Origen

Este repositorio nace de la presentación *Alcance técnico: landing, tienda y plataforma* (Afiches F1, documento de trabajo, septiembre de 2026). Esa presentación cubre solo la capa técnica; el modelo de negocio, el arte y la estrategia comercial están definidos por el equipo fuera de este repositorio.
