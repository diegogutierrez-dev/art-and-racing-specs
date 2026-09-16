# Dominio · Cuenta y colección

**Fase:** F2 (los códigos se emiten desde F1)
**Pieza:** Nivel 3, plataforma
**Principio rector:** backend mínimo

## Propósito

De la entrega a la colección. El comprador recibe un póster con un código único impreso, lo reclama en su cuenta y la pieza aparece en su colección de temporada, con los huecos que faltan.

```
┌────────────┐   ┌────────────┐   ┌────────────┐   ┌────────────┐
│   Póster   │──▶│   Canje    │──▶│   Cuenta   │──▶│ Colección  │
│ código     │   │ escanea y  │   │ identidad  │   │ temporada, │
│ impreso    │   │ reclama    │   │ e historial│   │ con huecos │
└────────────┘   └────────────┘   └────────────┘   └────────────┘
```

## Backend mínimo

Tres piezas y nada más:

1. **Tabla de códigos.** Un código por unidad vendida.
2. **Endpoint de canje.** Recibe un código y una cuenta, valida y asocia.
3. **Tabla de estados de pedido.** Ya definida en [pedidos.md](pedidos.md).

Sin blockchain, sin app nativa, sin infraestructura pesada.

## Sub-dominios

### Cuenta

Identidad e historial del comprador.

| Función | Detalle |
|---|---|
| Entrar | Según la [decisión 0002](../decisions/0002-identidad-del-usuario.md): cuentas de Shopify o auth propio |
| Estado del pedido | Cada pedido con su estado actual, guía y enlace de rastreo |
| Historial | Todos sus drops, pedidos y piezas |
| Canjear código | Formulario o escaneo (QR) |
| Colección | Vista de temporada |

La cuenta es la **vista del comprador** sobre el mismo registro de estado que usa producción. Ver [pedidos.md](pedidos.md).

### Código

| Campo | Tipo | Nota |
|---|---|---|
| codigo | texto único | Impreso en el póster. Formato legible y no adivinable |
| pedido_id | referencia | El pedido que lo generó |
| item | referencia | Qué póster, qué variante |
| drop | referencia | Para ubicarlo en la temporada |
| estado | enum | `emitido`, `activo`, `canjeado`, `anulado` |
| cuenta_id | referencia opcional | Se llena al canjear |
| emitido_en / activado_en / canjeado_en | fechas | |

Ciclo del código:

```
emitido ──(pedido entregado)──▶ activo ──(canje)──▶ canjeado
   │                               │
   └──────(cancelación)────────────┴──(devolución)──▶ anulado
```

- **Emitido:** se crea al recibir el webhook de pago (F1). Aún no se puede canjear.
- **Activo:** el pedido llegó a *entregado*. Canjeable.
- **Canjeado:** asociado a una cuenta. Aparece en la colección.
- **Anulado:** cancelación o devolución. No canjeable; si estaba canjeado, sale de la colección.

### Canje

Endpoint único. Entrada: código + cuenta autenticada. Salida: pieza en la colección o error.

Reglas:

1. Solo códigos en estado `activo`.
2. Un código se canjea una sola vez. Un segundo intento con el mismo código devuelve error claro ("ya reclamado").
3. Un código se puede canjear en una cuenta distinta a la del comprador original (regalo, reventa). El código es del que tiene el póster.
4. Límite de intentos por cuenta y por IP para evitar fuerza bruta.
5. El canje registra fecha y cuenta. No se puede deshacer desde la cuenta; solo por devolución.

### Colección

La temporada, con huecos.

| Función | Detalle |
|---|---|
| Cuadrícula de temporada | Todas las posiciones de la temporada. Las canjeadas se ven; las demás son huecos |
| Detalle de pieza | Imagen, ilustrador, drop, fecha de canje |
| Varias temporadas | Una cuadrícula por temporada, la actual por defecto |

En F2 la colección es una lista o cuadrícula simple. En F3 se convierte en el [álbum](album.md).

## Reglas del dominio

1. **Los códigos se emiten desde el drop uno.** Aunque el canje llegue en F2, cada póster vendido en F1 ya tiene su código en la tabla y ese código va impreso.
2. **El código no es un token ni un activo.** Es una fila en una tabla. No se transfiere, no se vende, no tiene valor fuera de la colección.
3. **La colección no se edita a mano.** Solo se llena por canje y se vacía por devolución.
4. **Sin datos de pago en la cuenta.** El comprador ve monto y método como texto. Para facturas, va a Shopify.

## Impresión del código

El código va impreso en el póster. Esto es responsabilidad de producción, pero la plataforma debe:

- Exponer los códigos de un pedido (o de un tiraje) en formato exportable para la imprenta.
- Generar el QR correspondiente si se decide escaneo (el QR apunta a la URL de canje con el código).

## Decisión bloqueante

[0002 · Identidad del usuario](../decisions/0002-identidad-del-usuario.md). ¿Cuentas de cliente de Shopify como identidad, o auth propio? Define el alcance de todo lo demás y no se puede cambiar barato después. Si se usan cuentas de Shopify, la fase uno se acorta semanas. Si se quiere auth propio, hay que sincronizar dos fuentes de identidad.

## Criterios de aceptación (F2)

- [ ] Al pagar, cada unidad del pedido tiene un código `emitido` en la tabla.
- [ ] Al marcar *entregado*, los códigos del pedido pasan a `activo`.
- [ ] El comprador entra a su cuenta, canjea el código y ve la pieza en su colección.
- [ ] Un segundo canje del mismo código falla con mensaje claro.
- [ ] Una devolución anula el código y lo saca de la colección.
- [ ] Producción puede exportar los códigos de un tiraje para la imprenta.

## Preguntas abiertas

- ¿Formato del código? Propuesta: 8 a 10 caracteres alfanuméricos sin ambigüedad (sin 0/O, 1/l/I), con prefijo de temporada.
- ¿QR además del código legible? Sube la conversión de canje, pero requiere URL estable desde el primer tiraje.
- ¿Qué pasa si alguien compra dos veces el mismo póster? Dos códigos, una posición en la cuadrícula. ¿Se muestra "×2"?
