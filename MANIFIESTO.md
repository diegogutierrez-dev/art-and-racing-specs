# Manifiesto

Este documento dice qué construimos, para qué, y qué no vamos a hacer. Todo lo demás en este repositorio (specs, decisiones, consejos técnicos) se subordina a lo que está aquí. Si un spec contradice el manifiesto, el spec está mal.

## Qué es esto

Afiches F1 vende pósters impresos por drops. Cada póster trae un código único. Con ese código, el comprador reclama la pieza en su cuenta y la ve dentro de su colección digital de la temporada. La colección tiene huecos visibles: cada hueco es el siguiente drop.

Construimos la capa técnica que hace posible ese ciclo: entrar, comprar, recibir, reclamar, coleccionar. Nada más.

## Principios

### 1. La frontera es la compra

Nuestra responsabilidad empieza cuando alguien llega a la landing y termina cuando el pedido queda pagado. A partir de ahí, producción ejecuta e informa. Nosotros damos las herramientas para que informe, no ejecutamos por ellos.

No respondemos por arte, impresión, empaque, calidad, transportadoras ni servicio al cliente sobre el pedido físico. Esto no es una limitación técnica, es una decisión. Está escrita para que nadie termine respondiendo por un pedido perdido desde el equipo tech.

### 2. Tres piezas, una sola experiencia

El sistema son tres piezas: la landing, la tienda y la plataforma. Cada una tiene un trabajo. Ninguna intenta hacer el trabajo de otra.

El comprador atraviesa las tres en una sola sesión y nunca debería sentir que cambió de producto. Mismo dominio, misma identidad visual, misma sesión.

### 3. Un solo registro de estado

El estado de un pedido vive en un solo lugar. El panel de producción lo escribe. La cuenta del comprador lo lee. Los correos lo reflejan. Nadie actualiza nada dos veces y no hay hoja de cálculo paralela.

Cuando aparece una excepción (cancelación, reimpresión, devolución) es un estado nuevo, no un caso especial. Se modela desde el día uno.

### 4. Primero vender, después operar, al final coleccionar

Las fases están en ese orden por una razón. El álbum digital es el diferenciador, y construirlo antes de vender el primer póster sería el error clásico.

Los códigos se emiten desde el drop uno. El álbum puede llegar en el drop seis sin perder nada, porque el código ya existe y ya está asociado a la cuenta.

### 5. Backend mínimo

Una tabla de códigos, un endpoint de canje, una tabla de estados de pedido. Sin blockchain, sin app nativa, sin infraestructura pesada. Si una pieza de infraestructura no se justifica con un drop real, no se construye.

### 6. Aburrido donde debe ser aburrido

El panel de producción es una tabla, filtros y botones de estado. Nada de dashboards. La emoción va en la landing y en el álbum. La operación va en una tabla.

### 7. Las decisiones bloqueantes se toman antes de escribir código

Hay dos decisiones que definen el alcance de todo lo demás y no se pueden cambiar barato después:

- **A. Pasarela y jurisdicción.** Colombia o entidad en EE. UU. Define comisiones y a quién le podemos vender.
- **B. Identidad del usuario.** Cuentas de Shopify o auth propio. Define el alcance de la plataforma.

Cada una tiene una persona y una fecha asignada. Sin eso, la fase uno no arranca. Están documentadas en [decisions/](decisions/).

### 8. Los consejos técnicos son consejos

El stack recomendado (Next.js, Vercel, Tailwind y su ecosistema) está en [tech/](tech/) como guía, no como spec. Los specs describen comportamiento y dominio. El stack es el medio, y puede cambiar sin que cambie el spec.

## Qué no somos

- No somos un marketplace. Vendemos nuestros drops.
- No somos una app nativa. Todo es web.
- No somos una plataforma de NFTs. El código es una fila en una tabla.
- No somos el equipo de producción. Les damos un panel.
- No somos asesores legales ni contables. Donde el documento toca esos temas, lo dice y remite a quien sí lo es.

## Cómo usar este repositorio

1. Lee este manifiesto.
2. Lee [docs/00-alcance.md](docs/00-alcance.md) y [docs/01-sistema.md](docs/01-sistema.md).
3. Revisa las decisiones pendientes en [decisions/](decisions/).
4. Ve al dominio que vas a construir en [domains/](domains/).
5. Consulta [tech/stack.md](tech/stack.md) solo cuando vayas a escribir código.
