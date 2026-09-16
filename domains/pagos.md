# Dominio · Pagos

**Fase:** F1
**Pieza:** Nivel 2, dentro de la tienda
**Principio rector:** el punto más caro de resolver mal

> **Aviso.** Este documento no es asesoría legal, contable ni financiera. La ruta B implica constituir una entidad en otro país y hay que validarla con contador y abogado antes de ejecutarla. El lado colombiano de recibir ingresos del exterior también.

## El problema

Shopify Payments no está disponible para cuentas colombianas. Encima de la comisión de la pasarela local, Shopify cobra un recargo por usar pasarela externa: 2% en Basic, 1% en Grow, 0.6% en Advanced.

Si el público es global desde el drop uno, la ruta A se queda corta rápido. Conviene decidirlo antes de montar la tienda, no después.

## Ruta A · Pasarela local

| Aspecto | Detalle |
|---|---|
| Proveedores | Wompi, PayU, ePayco o Mercado Pago |
| Comisión | ~2.65% a 3.99% + IVA por transacción |
| Recargo Shopify | Sí, encima de la comisión |
| Fortaleza | Excelente para Colombia: PSE, Nequi, Daviplata |
| Debilidad | Débil para venta internacional |
| Costo total por venta | ~5.5% a 6% |

## Ruta B · Entidad en EE. UU.

| Aspecto | Detalle |
|---|---|
| Habilita | Shopify Payments y Stripe |
| Comisión | ~2.9% + 30¢ |
| Recargo Shopify | No |
| Fortaleza | Cobro nativo en USD y multi-moneda, tarjetas internacionales |
| Debilidad | Implica constitución, banco y contabilidad en EE. UU. Se pierden PSE, Nequi y Daviplata |
| Costo total por venta | ~3.5% a 4.5% |

### Cómo se monta la ruta B

| Paso | Qué | Nota |
|---|---|---|
| 01 | LLC en Delaware | Stripe Atlas, ~USD 500 una vez: filing, EIN, agente año 1 y cuenta bancaria |
| 02 | LLC, no C-corp | La C-corp implica doble tributación y contabilidad más cara |
| 03 | Cuenta bancaria en EE. UU. | Incluida en el paquete, sin viajar |
| 04 | Activar Shopify Payments | Con el EIN. Desaparece el recargo por pasarela externa |
| 05 | Shopify Markets | Multi-moneda y precios por región |
| 06 | Contador en EE. UU. | Declaraciones federales obligatorias aunque no haya utilidad |

### Costo de la estructura

| Concepto | Costo |
|---|---|
| Setup, una sola vez | ~USD 500 |
| Registered agent | USD 100/año |
| Franchise tax Delaware | ~USD 300/año |
| Contador en EE. UU. | USD 500 a 1.000/año |

### Punto de equilibrio

Alrededor de USD 5.000 a 6.000 de ventas al mes. Por debajo, la ruta A cuesta menos. Pero B no se elige por ahorro: se elige por acceso a tarjetas internacionales y cobro en USD.

## La advertencia importante

Con Shopify Payments se pierden PSE, Nequi y Daviplata, que es como paga la mayoría en Colombia. Si se quieren los dos mercados hay que decidir entre:

1. **Métodos de pago adicionales** en la misma tienda (pasarela local como método secundario, con su recargo).
2. **Dos storefronts**, una por mercado, con el costo operativo que eso implica.

Esta es una sub-decisión de la [decisión 0001](../decisions/0001-pasarela-y-jurisdiccion.md).

## Reglas para la plataforma

Independientemente de la ruta:

1. **La plataforma nunca toca datos de pago.** No almacena tarjetas, no procesa cobros, no ve números de cuenta. Todo eso vive en Shopify y la pasarela.
2. **El panel de producción no tiene acceso a datos de pago.** Ve monto total y método como texto, nada más.
3. **El monto se guarda en la moneda de la transacción**, con la moneda explícita. No se convierte.

## Criterios de aceptación (F1)

- [ ] La decisión 0001 está tomada, con persona y fecha.
- [ ] La pasarela procesa un pago real de prueba de punta a punta.
- [ ] Las comisiones reales están cotizadas por escrito con el proveedor elegido.

## Preguntas abiertas

- ¿Cuál es la proporción esperada de ventas Colombia vs. exterior en los primeros tres drops? Es el dato que decide la ruta.
- ¿Hay contador y abogado identificados para validar la ruta B?

## Verificación de cifras

Precios y comisiones verificados en septiembre de 2026. Los porcentajes de pasarela varían por negociación y volumen; hay que pedir cotización directa antes de fijar el precio de venta.
