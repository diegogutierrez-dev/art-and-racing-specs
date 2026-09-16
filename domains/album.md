# Dominio · Álbum

**Fase:** F3
**Pieza:** Nivel 3, plataforma
**Principio rector:** web, no app. Es el diferenciador, pero no bloquea el lanzamiento

## Propósito

Convertir la colección en algo que se quiera abrir. El afiche en el teléfono, con profundidad y efecto holográfico, y la cuadrícula de la temporada con los huecos que empujan al siguiente drop.

## Los tres efectos

| Efecto | Qué es | Cómo se logra |
|---|---|---|
| **Profundidad** | El afiche en capas (fondo, circuito, figura) que se mueven con el giro del teléfono | Cada pieza se entrega en capas separadas. Parallax con la orientación del dispositivo |
| **Holográfico** | Shader que reacciona a la orientación del dispositivo. Es lo que hace sentir cromo | Shader propio sobre la capa superior, alimentado por Device Orientation |
| **Cuadrícula** | La temporada completa con los huecos visibles. El hueco es lo que empuja al siguiente drop | Vista de temporada con posiciones fijas; las no canjeadas se muestran vacías o en silueta |

## Stack

Three.js · shader propio · Device Orientation API · mismo despliegue de la plataforma.

No hay app nativa. Todo corre en el navegador del teléfono. Ver [tech/stack.md](../tech/stack.md) para el detalle.

## Responsabilidades

- Renderizar una pieza canjeada con sus capas y el shader.
- Responder a la orientación del dispositivo (y al mouse en escritorio como degradación).
- Mostrar la cuadrícula de temporada con huecos.
- Enlazar cada hueco al drop correspondiente (si ya salió, a la tienda; si no, a la landing con fecha).

## Lo que el álbum no hace

- No canjea. El canje es de [cuenta y colección](cuenta-y-coleccion.md).
- No vende. Enlaza a la tienda.
- No funciona sin cuenta. Es una vista privada de la colección.

## Dependencias con el arte

El álbum necesita que cada pieza se entregue en capas separadas (fondo, circuito, figura, más una máscara para el holográfico). Esto es un requisito para el equipo creativo y debe estar en el pipeline de producción de cada drop desde el drop uno, aunque el álbum llegue después.

Si una pieza no tiene capas, el álbum la muestra plana. No bloquea nada.

## Reglas

1. **No bloquea el lanzamiento.** Los códigos se emiten desde el drop uno; el álbum puede llegar en el drop seis sin perder nada.
2. **Degradación elegante.** Sin permiso de orientación, sin WebGL o en escritorio, la pieza se ve plana y la cuadrícula funciona igual.
3. **Los activos se sirven optimizados.** Capas en formato comprimido, tamaño acorde al dispositivo. El álbum no puede pesar más que la landing.
4. **El permiso de orientación se pide en contexto.** Solo al abrir una pieza, con explicación, no al cargar la página.

## Criterios de aceptación (F3)

- [ ] Un comprador abre una pieza canjeada en iOS y Android y ve profundidad y holográfico al girar el teléfono.
- [ ] En escritorio, la pieza responde al mouse.
- [ ] La cuadrícula muestra las piezas canjeadas y los huecos, y cada hueco enlaza al drop.
- [ ] Una pieza sin capas se muestra plana sin error.
- [ ] El álbum carga en menos de tres segundos en una conexión móvil promedio.

## Preguntas abiertas

- ¿Cuántas capas por pieza? Tres (fondo, circuito, figura) es la hipótesis. Definir con el equipo creativo antes del drop uno para que el pipeline de arte lo incluya.
- ¿El holográfico es por pieza o global? Puede haber piezas "cromo" y piezas normales como mecánica de rareza.
- ¿Compartir una pieza (imagen o enlace público)? Es viralidad, pero abre una vista pública del álbum. Fuera de F3 salvo que negocio lo priorice.
