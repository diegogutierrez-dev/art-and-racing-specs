# Dominio · Producción (panel)

**Fase:** F2
**Pieza:** Nivel 3, plataforma
**Principio rector:** deliberadamente aburrido

## Propósito

La vista interna donde el equipo de producción marca los estados 03, 04 y 05 del [ciclo de pedidos](pedidos.md). Una tabla, filtros y botones de estado. Nada de dashboards.

## Quién lo usa

El equipo de producción. No el comprador, no el equipo tech en el día a día. Es la única interfaz que producción necesita; si necesitan algo más, se agrega aquí, no en otra herramienta.

## Responsabilidades

| Función | Detalle |
|---|---|
| Cola de pedidos | Filtrable por drop y por tiraje. Ordenada por antigüedad en el estado actual |
| Cambio de estado | Un clic por transición válida. El botón solo muestra las transiciones permitidas desde el estado actual |
| Guía y transportadora | Campos obligatorios al marcar *despachado* |
| Acciones en lote | Seleccionar varios pedidos y aplicar la misma transición. Para despachos grandes |
| Excepciones | Marcar cancelación, reimpresión o devolución con nota obligatoria |
| Historial | Ver los eventos de estado de un pedido, con autor y fecha |

## Lo que el panel NO tiene

- **Datos de pago.** Ni tarjeta, ni cuenta, ni método detallado. Monto total como texto y nada más.
- **Dashboards ni gráficas.** Si producción necesita un reporte, se exporta la tabla.
- **Edición del pedido comercial.** Ítems, dirección y monto se editan en Shopify, no aquí.
- **Gestión de códigos.** Los códigos se ven asociados al pedido, pero no se editan desde el panel.

## Vista principal

Una tabla. Columnas mínimas:

| Columna | Nota |
|---|---|
| Número de pedido | Enlace al detalle |
| Drop | Filtro |
| Ítems | Resumen: "2× Póster A, 1× Póster B" |
| Estado actual | Con tiempo en ese estado |
| Comprador | Nombre y ciudad. Sin correo completo si no hace falta |
| Acciones | Botones de transición válida |

Filtros: drop, tiraje, estado, rango de fechas. Búsqueda por número de pedido o nombre.

## Reglas

1. **Solo transiciones válidas.** El panel no permite saltar estados. Ver la tabla en [pedidos.md](pedidos.md).
2. **Guía obligatoria al despachar.** Sin guía y transportadora no se puede marcar 05.
3. **Nota obligatoria en excepciones.** Cancelar, reimprimir o devolver exige un motivo escrito.
4. **Todo cambio queda con autor.** Cada usuario de producción tiene su propia cuenta. No hay usuario compartido.
5. **Las acciones en lote se confirman.** Muestra cuántos pedidos se van a mover y a qué estado antes de aplicar.
6. **Un solo registro.** El panel escribe sobre el mismo registro que lee la [vista del comprador](cuenta-y-coleccion.md). No hay copia.

## Acceso

- Autenticación separada de la del comprador. Un usuario de producción no es una cuenta de cliente.
- Roles mínimos: `produccion` (cambia estados) y `admin` (además gestiona usuarios de producción y ve costos).
- Sin acceso público. Ruta protegida.

## Criterios de aceptación (F2)

- [ ] Un usuario de producción entra al panel y ve la cola filtrada por el drop actual.
- [ ] Mueve un pedido de 02 a 03 con un clic y el comprador ve el cambio en su cuenta.
- [ ] Intenta marcar 05 sin guía y el sistema lo impide.
- [ ] Selecciona diez pedidos y los marca como despachados en lote, cada uno con su guía.
- [ ] Registra una reimpresión con nota y aparece en el historial del pedido.
- [ ] No ve ningún dato de pago en ninguna vista.

## Preguntas abiertas

- ¿Producción necesita imprimir etiquetas o listas de empaque desde el panel? Si sí, es una función de F2 que hay que dimensionar.
- ¿Carga masiva de guías desde CSV de la transportadora? Útil en despachos grandes. Propuesta: no en F2, evaluar en F3.
- ¿Quién en producción valida el diseño del panel antes de construirlo? Ver [00-alcance.md](../docs/00-alcance.md).
