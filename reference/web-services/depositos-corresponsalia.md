# Depósitos Corresponsalía

Los depósitos por corresponsalía permiten al cliente consignar efectivo en el comercio hacia una **cuenta bancaria** de cualquier banco aliado. El flujo varía según la red:

* **Bancos sin pre-consulta** (`ca`, `ci`, `co`, `bo`) → el comercio captura el número de cuenta + datos del titular y ejecuta directamente `pracRec`.
* **Bancos con pre-consulta** (`dc`, `sd`) → el comercio primero consulta la cuenta (`preConsulta`) para obtener nombre del titular + `tx_id` de corta vida; luego ejecuta `pracRec` referenciando ese `tx_id`.

{% hint style="info" %}
En `pracRec` para depósitos, el campo raíz `valor` lleva el **monto real** a consignar (a diferencia de los retiros donde es `0`). El campo `celular` se reutiliza como número de cuenta destino.
{% endhint %}

## Estados específicos de depósitos

| estado | Significado                                           |
| ------ | ----------------------------------------------------- |
| 00     | Depósito exitoso, fondos acreditados al titular       |
| 05     | Cuenta inválida / titular no coincide / banco rechaza |
| 99     | Sin respuesta del banco corresponsal                  |

***

## Redes disponibles

{% tabs %}
{% tab title="Banco Agrario (ca)" %}

**Código operador:** `ca`
**PreConsulta:** no requiere

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>celular (root)</td><td>Número de cuenta destino</td><td>string</td><td>"12345678901"</td></tr><tr><td>valor (root)</td><td>Monto del depósito</td><td>string</td><td>"50000"</td></tr><tr><td>caTipoCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>caCelular</td><td>Celular del titular (exactamente 10 dígitos)</td><td>string</td><td>"3100000000"</td></tr></tbody></table>

```php
// SOAP - pracRec
$data = json_encode([
    "caTipoCuenta" => "1",
    "caCelular" => "3100000000"
]);

$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '12345678901',   // número de cuenta destino
    'operador' => 'ca',
    'valor' => '50000',           // monto del depósito
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

```json
// Response exitoso
{ "idtrans":123456, "estado":"00", "respuesta":"Transaccion exitosa", "codigoauth":"123456", "saldo":1000, "codop":"123456" }

// Cuenta inválida
{ "idtrans":123456, "estado":"05", "respuesta":"Transaccion no exitosa", "codop":"001" }
```
{% endtab %}

{% tab title="Coopcentral (ci)" %}

**Código operador:** `ci`
**PreConsulta:** no requiere

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>celular (root)</td><td>Número de cuenta Coopcentral (5 – 15 dígitos)</td><td>string</td><td>"12345678"</td></tr><tr><td>valor (root)</td><td>Monto del depósito</td><td>string</td><td>"25000"</td></tr></tbody></table>

```php
$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '12345678',
    'operador' => 'ci',
    'valor' => '25000',
    'jsonAdicional' => '',
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Colpatria (co)" %}

**Código operador:** `co`
**PreConsulta:** no requiere

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>celular (root)</td><td>Número de cuenta Colpatria</td><td>string</td><td>"1234567890"</td></tr><tr><td>valor (root)</td><td>Monto del depósito</td><td>string</td><td>"40000"</td></tr><tr><td>colpCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>colpIdentificacionTx</td><td>Cédula del titular (mínimo 5 dígitos)</td><td>string</td><td>"1020304050"</td></tr></tbody></table>

```php
$data = json_encode([
    "colpCuenta" => "1",
    "colpIdentificacionTx" => "1020304050"
]);

$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '1234567890',
    'operador' => 'co',
    'valor' => '40000',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Davivienda (dc)" %}

**Código operador:** `dc`
**PreConsulta:** **obligatoria** — primero debe ejecutarse una consulta Davivienda (proceso interno `consultaDaviCuenta`) que devuelve el nombre del titular y un `davitx_id` de corta vida. Ese `davitx_id` debe enviarse en el `pracRec`.

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>celular (root)</td><td>Número de cuenta Davivienda</td><td>string</td><td>"1234567890"</td></tr><tr><td>valor (root)</td><td>Monto del depósito</td><td>string</td><td>"100000"</td></tr><tr><td>daviDocumento</td><td>Cédula del titular (mínimo 4 dígitos)</td><td>string</td><td>"1020304050"</td></tr><tr><td>daviTipoDoc</td><td>01 = CC · 02 = CE · 03 = NIT · 04 = TI · 12 = NIT Persona Natural · 13 = RC · 14 = Celular</td><td>string</td><td>"01"</td></tr><tr><td>daviTipoCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>daviNombre</td><td>Nombre devuelto por la consulta previa</td><td>string</td><td>"JUAN PEREZ"</td></tr><tr><td>daviTelefono</td><td>Celular del titular</td><td>string</td><td>"3100000000"</td></tr><tr><td>davitx_id</td><td>ID de transacción devuelto por la consulta previa</td><td>string</td><td>"DAVI-555"</td></tr></tbody></table>

```php
$data = json_encode([
    "daviDocumento" => "1020304050",
    "daviTipoDoc" => "01",
    "daviTipoCuenta" => "1",
    "daviNombre" => "JUAN PEREZ",
    "daviTelefono" => "3100000000",
    "davitx_id" => "DAVI-555"
]);

$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '1234567890',
    'operador' => 'dc',
    'valor' => '100000',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Caja Social (sd)" %}

**Código operador:** `sd`
**PreConsulta:** **obligatoria** — primero debe ejecutarse una consulta Caja Social (proceso interno `consultaSd`) para validar el celular y obtener un `id_trx` de corta vida. Ese `id_trx` debe enviarse en el `pracRec`.

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>celular (root)</td><td>Celular destino del depósito</td><td>string</td><td>"3100000000"</td></tr><tr><td>valor (root)</td><td>Monto del depósito</td><td>string</td><td>"30000"</td></tr><tr><td>sdCelular</td><td>Celular del titular (exactamente 10 dígitos — mismo usado en la consulta previa)</td><td>string</td><td>"3100000000"</td></tr><tr><td>id_trx</td><td>ID devuelto por la consulta previa</td><td>string</td><td>"SD-777"</td></tr></tbody></table>

```php
$data = json_encode([
    "sdCelular" => "3100000000",
    "id_trx" => "SD-777"
]);

$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'sd',
    'valor' => '30000',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Banco de Bogotá (bo)" %}

Depósito a cuenta de **Banco de Bogotá** vía red ATH. Reutiliza la misma estructura de parámetros que el retiro ATH (`br`).

**Código operador:** `bo`
**PreConsulta:** no requiere

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>celular (root)</td><td>Número de cuenta destino</td><td>string</td><td>"1234567890"</td></tr><tr><td>valor (root)</td><td>Monto del depósito</td><td>string</td><td>"80000"</td></tr><tr><td>athBanco</td><td>1 = Bogotá · 2 = Popular · 3 = Occidente · 4 = AV Villas · 5 = Dale</td><td>string</td><td>"1"</td></tr><tr><td>athCuenta</td><td>1 = Ahorros · 2 = Corriente</td><td>string</td><td>"1"</td></tr><tr><td>athNuCuenta</td><td>Número de cuenta (si no se envía <code>athIdentificacion</code>, se usa como identificación)</td><td>string</td><td>"1234567890"</td></tr><tr><td>athIdentificacion</td><td>Cédula del titular (opcional)</td><td>string</td><td>"1020304050"</td></tr><tr><td>athCelular</td><td>Celular del titular</td><td>string</td><td>"3100000000"</td></tr></tbody></table>

```php
$data = json_encode([
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
    'celular' => '1234567890',
    'operador' => 'bo',
    'valor' => '80000',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}
{% endtabs %}

***

## Resumen rápido de parámetros por red

| Red             | Código | PreConsulta | Tipo cuenta | Cédula titular | `tx_id` previo | Obs.                              |
| --------------- | ------ | ----------- | ----------- | -------------- | -------------- | --------------------------------- |
| Banco Agrario   | `ca`   | —           | ✅           | —              | —              | celular del titular               |
| Coopcentral     | `ci`   | —           | —           | —              | —              | solo número de cuenta             |
| Colpatria       | `co`   | —           | ✅           | ✅              | —              | —                                 |
| Davivienda      | `dc`   | ✅           | ✅           | ✅              | ✅              | `consultaDaviCuenta` previa       |
| Caja Social     | `sd`   | ✅           | —           | —              | ✅              | `consultaSd` previa               |
| Banco de Bogotá | `bo`   | —           | ✅           | ✅              | —              | usa red ATH (mismos campos que `br`) |
