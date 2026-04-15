# Retiros Corresponsalía

Los retiros por corresponsalía permiten al cliente sacar efectivo en el comercio usando un **token OTP** generado previamente desde su app bancaria. El flujo estándar es:

1. El cliente genera el token OTP en su banco/wallet.
2. El comercio ejecuta **`preConsulta`** con el token y el monto → el banco valida que el token esté vivo y el saldo disponible.
3. Si la pre-consulta devuelve `estado = 00`, el comercio ejecuta **`pracRec`** con el mismo token para confirmar la entrega del efectivo.
4. El comercio entrega el efectivo al cliente.

{% hint style="warning" %}
En todos los retiros se envía `valor = 0` en el `pracRec` porque el monto real viaja dentro de `jsonAdicional.valorr`. No se cobra "valor de venta" al comercio.
{% endhint %}

## Estados específicos de retiros

| estado | Significado                                          |
| ------ | ---------------------------------------------------- |
| 00     | Retiro exitoso, dinero descontado al cliente         |
| 05     | Error de operador / token inválido / saldo insuficiente |
| 99     | Sin respuesta del banco corresponsal                 |

***

## Redes disponibles

{% tabs %}
{% tab title="ATH / Grupo Aval (br)" %}

Cubre **Banco de Bogotá, Popular, Occidente, AV Villas y Dale** — el cliente indica al comercio cuál banco.

**Código operador:** `br`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token OTP generado en app del banco</td><td>string</td><td>"12345678"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"50000"</td></tr><tr><td>athBanco</td><td>1 = Bogotá · 2 = Popular · 3 = Occidente · 4 = AV Villas · 5 = Dale</td><td>string</td><td>"1"</td></tr><tr><td>athCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>athNuCuenta</td><td>Número de cuenta (si no se envía <code>athIdentificacion</code>, se usa como identificación)</td><td>string</td><td>"1234567890"</td></tr><tr><td>athIdentificacion</td><td>Cédula del titular (opcional; si vacío, se toma <code>athNuCuenta</code>)</td><td>string</td><td>"1020304050"</td></tr><tr><td>athCelular</td><td>Celular del titular</td><td>string</td><td>"3100000000"</td></tr></tbody></table>

```php
// SOAP - pracRec
$data = json_encode([
    "tokenr" => "12345678",
    "valorr" => "50000",
    "athBanco" => "1",
    "athCuenta" => "1",
    "athNuCuenta" => "1234567890",
    "athIdentificacion" => "1020304050",
    "athCelular" => "3100000000"
]);

$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'br',
    'valor' => '0',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

```json
// Response exitoso
{ "idtrans":123456, "estado":"00", "respuesta":"Transaccion exitosa", "codigoauth":"123456", "saldo":1000, "codop":"123456" }

// Token inválido
{ "idtrans":0, "estado":"05", "respuesta":"token no encontrado", "codop":"0" }
```
{% endtab %}

{% tab title="Nequi (nr)" %}

**Código operador:** `nr`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Nequi generado desde la app</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"20000"</td></tr></tbody></table>

```php
$data = json_encode(["tokenr" => "123456", "valorr" => "20000"]);
$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000', 'operador' => 'nr', 'valor' => '0', 'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Caja Social (sr)" %}

Retiro Caja Social requiere **consulta previa** usando `sdCelular` para obtener `id_trx`.

**Código operador:** `sr`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Caja Social</td><td>string</td><td>"654321"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"30000"</td></tr><tr><td>sdCelular</td><td>Celular del titular (usado en consulta previa)</td><td>string</td><td>"3100000000"</td></tr><tr><td>id_trx</td><td>ID devuelto por la consulta previa</td><td>string</td><td>"TX-987"</td></tr></tbody></table>
{% endtab %}

{% tab title="Colpatria (rc)" %}

**Código operador:** `rc`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Colpatria</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"40000"</td></tr><tr><td>colpIdentificacionTx</td><td>Cédula del titular (mínimo 5 dígitos)</td><td>string</td><td>"1020304050"</td></tr><tr><td>colpatriaIdenEx</td><td>Fecha de expedición del documento (<code>AAAA-MM-DD</code>)</td><td>string</td><td>"2010-05-14"</td></tr><tr><td>colpTipoRetiro</td><td>1 = Pin Pago · 2 = Pin Giro</td><td>string</td><td>"1"</td></tr></tbody></table>
{% endtab %}

{% tab title="Davivienda (dd)" %}

Davivienda requiere **consulta previa** (`consultaDaviCuenta`) para obtener nombre + `davitx_id` antes del retiro.

**Código operador:** `dd`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Davivienda</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"100000"</td></tr><tr><td>daviDocumento</td><td>Número de documento del titular (mínimo 4 dígitos)</td><td>string</td><td>"1020304050"</td></tr><tr><td>daviTipoDoc</td><td>01 = CC · 02 = CE · 03 = NIT · 04 = TI · 12 = NIT Persona Natural · 13 = RC · 14 = Celular</td><td>string</td><td>"01"</td></tr><tr><td>daviTipoCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>daviNombre</td><td>Nombre completo devuelto por la consulta previa</td><td>string</td><td>"JUAN PEREZ"</td></tr><tr><td>daviTelefono</td><td>Celular del titular</td><td>string</td><td>"3100000000"</td></tr><tr><td>davitx_id</td><td>ID de transacción devuelto por la consulta previa</td><td>string</td><td>"DAVI-555"</td></tr></tbody></table>
{% endtab %}

{% tab title="Coopcentral (rr)" %}

**Código operador:** `rr`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Coopcentral</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"25000"</td></tr></tbody></table>
{% endtab %}

{% tab title="Top-up (tr)" %}

**Código operador:** `tr`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Top-up</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"15000"</td></tr></tbody></table>
{% endtab %}

{% tab title="Banco Agrario (ar)" %}

**Código operador:** `ar`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tokenr</td><td>Token Banco Agrario</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Monto del retiro</td><td>string</td><td>"35000"</td></tr><tr><td>caTipoCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>caCelular</td><td>Celular del titular (exactamente 10 dígitos)</td><td>string</td><td>"3100000000"</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

***

## Resumen rápido de parámetros por red

| Red              | Código | Token OTP | Cédula | Tipo cuenta | Celular | id\_trx previo | Obs.                        |
| ---------------- | ------ | --------- | ------ | ----------- | ------- | -------------- | --------------------------- |
| ATH / Aval       | `br`   | ✅         | ✅      | ✅           | ✅       | —              | 4 bancos via `athBanco`     |
| Nequi            | `nr`   | ✅         | —      | —           | —       | —              | —                           |
| Caja Social      | `sr`   | ✅         | —      | —           | ✅       | ✅              | `consultaSd` previa         |
| Colpatria        | `rc`   | ✅ (8)     | ✅      | —           | —       | —              | token fijo 8 dígitos        |
| Davivienda       | `dd`   | ✅         | ✅      | ✅           | ✅       | ✅              | `consultaDaviCuenta` previa |
| Coopcentral      | `rr`   | ✅         | —      | —           | —       | —              | —                           |
| Top-up           | `tr`   | ✅         | —      | —           | —       | —              | —                           |
| Banco Agrario    | `ar`   | ✅         | —      | ✅           | ✅       | —              | —                           |
