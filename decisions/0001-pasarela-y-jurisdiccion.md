# 0001 · Pasarela y jurisdicción

**Estado:** `pendiente`
**Responsable:** _por asignar_
**Fecha límite:** _por asignar_
**Bloquea:** F1

> No es asesoría legal ni financiera. La ruta B hay que validarla con contador y abogado.

## Contexto

Shopify Payments no está disponible para cuentas colombianas. Usar una pasarela local implica su comisión más un recargo de Shopify. Constituir una entidad en EE. UU. habilita Shopify Payments y Stripe, sin recargo, con cobro en USD, pero pierde PSE, Nequi y Daviplata y trae costos fijos anuales.

Esta decisión define las comisiones por venta y a quién le podemos vender. Conviene tomarla antes de montar la tienda, no después. Detalle completo en [domains/pagos.md](../domains/pagos.md).

## Opciones

### A · Pasarela local (Colombia)

- Wompi, PayU, ePayco o Mercado Pago.
- ~5.5% a 6% por venta (pasarela + IVA + recargo Shopify).
- PSE, Nequi, Daviplata. Débil para venta internacional.
- Sin costos fijos adicionales.

### B · Entidad en EE. UU.

- LLC en Delaware vía Stripe Atlas (~USD 500) + registered agent + franchise tax + contador (~USD 900 a 1.400/año).
- ~3.5% a 4.5% por venta.
- Tarjetas internacionales, USD, multi-moneda con Shopify Markets.
- Se pierden PSE, Nequi y Daviplata salvo que se agregue pasarela local como método secundario o se abran dos storefronts.

### Sub-decisión si B

Si se quieren los dos mercados: métodos de pago adicionales en la misma tienda, o dos storefronts.

## Criterio para decidir

- Si el público es global desde el drop uno, A se queda corta rápido.
- Punto de equilibrio por costo: USD 5.000 a 6.000 de ventas al mes. Por debajo, A cuesta menos.
- B no se elige por ahorro; se elige por acceso.

Dato necesario: proporción esperada de ventas Colombia vs. exterior en los primeros tres drops.

## Decisión

_Pendiente._

## Consecuencias

_Se completan al decidir._ Afectan: [tienda.md](../domains/tienda.md), [pagos.md](../domains/pagos.md), [costos.md](../domains/costos.md), [marca.md](../domains/marca.md) (titular del registro en EE. UU.).
