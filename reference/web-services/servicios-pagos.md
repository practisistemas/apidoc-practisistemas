# Servicios y Pagos

Productos que no encajan en recargas, paquetes, retiros ni pines: juegos de suerte y azar, cobro de premios, aportes a seguridad social, pagos con referencia y seguros. Cada uno tiene su estructura propia de `jsonAdicional`.

***

## Catálogo

| Código | Nombre comercial             | Requiere PreConsulta | Notas                                              |
| ------ | ---------------------------- | -------------------- | -------------------------------------------------- |
| `ba`   | **Baloto**                   | ✅ (valida apuesta)   | Hasta 9 sorteos, tablas manuales o automáticas     |
| `bc`   | **Baloto Premios**           | ✅ (resuelve premio)  | Cobro con `idPre` + token                          |
| `ss`   | **Pago Seguridad Social**    | ✅                    | Aportes Colpensiones (voluntario / obligatorio)    |
| `dp`   | **Depósito tpaga**           | ✅ (obtiene `tx_id`)  | Pago de referencia                                 |
| `ga`   | **Seguro GEA**               | —                    | KYC completo del adquirente                        |
| `bw`   | **Bwin Apuestas**            | ✅ (valida cédula)    | Flujo con `idPre`; valor lo fija el servidor       |

***

{% tabs %}
{% tab title="Baloto (ba)" %}

Venta de apuestas Baloto / Baloto Revancha. Se juegan **tablas** (manuales o automáticas), 1 a 9 sorteos consecutivos.

### Pre-consulta — estructura de `boarDataInfo`

`boarDataInfo` es una cadena con tablas separadas por `;`. Cada tabla tiene campos separados por `,`:

**Tabla manual** (7 campos):
```
b1,b2,b3,b4,b5,superBalota,revancha
```
* `b1..b5` — 5 balotas normales (1-43), no repetidas
* `superBalota` — super balota (1-16)
* `revancha` — `0` o `1`

**Tabla automática** (2 campos):
```
99,revancha
```
El `99` indica quick-pick; el servidor genera las balotas.

**Parámetros**

<table><thead><tr><th width="170">Campo</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>sorteosBaloto</td><td>Cantidad de sorteos a jugar (1 – 9)</td><td>string</td><td>"3"</td></tr><tr><td>boarDataInfo</td><td>Tablas separadas por <code>;</code></td><td>string</td><td>"5,12,18,27,33,9,1;99,0"</td></tr></tbody></table>

```php
// PreConsulta
$params = [
    'idcomercio'   => 01234, 'claveventa' => 1234,
    'tipoConsulta' => 'valDatosPreRec',
    'data'         => json_encode([
        "operador"      => "ba",
        "sorteosBaloto" => "3",
        "boarDataInfo"  => "5,12,18,27,33,9,1;99,0"
    ])
];
$response = $client->call('preConsulta', $params);
```

```json
// Response exitoso (servidor calcula el precio total)
{ "estado":"1", "setValor":"1", "valorVenta":23400 }

// Errores frecuentes
{ "reply":"errord", "descerr":"Maximo de sorteos superado" }
{ "reply":"errord", "descerr":"Error, balota supera valor permitido en tabla 0" }
{ "reply":"errord", "descerr":"Error, SuperBalota supera valor permitido en tabla 0" }
{ "reply":"errord", "descerr":"Error, balotas repetidas en tabla 0" }
```

### Venta (pracRec)

```php
$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'ba',
    'valor' => '23400',                 // valorVenta devuelto por preConsulta
    'jsonAdicional' => '',
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Baloto Premios (bc)" %}

Cobro de premios de Baloto. El comercio consulta el premio (proceso interno que devuelve un `idPre`) y luego ejecuta la venta con ese ID y el token del ganador.

**Código operador:** `bc`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idPre</td><td>ID devuelto por la consulta de premio (solo números)</td><td>string</td><td>"1234567"</td></tr><tr><td>tokenr</td><td>Token OTP del ganador</td><td>string</td><td>"123456"</td></tr><tr><td>valorr</td><td>Valor del premio</td><td>string</td><td>"500000"</td></tr><tr><td>opcionAnulacion</td><td>1 = flujo de anulación (omite rango de validación)</td><td>string</td><td>"0"</td></tr></tbody></table>

```php
$data = json_encode([
    "idPre"           => "1234567",
    "tokenr"          => "123456",
    "valorr"          => "500000",
    "opcionAnulacion" => "0"
]);

$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'bc',
    'valor' => '0',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% tab title="Pago Seguridad Social (ss)" %}

Aportes a Colpensiones — voluntario u obligatorio. Si es obligatorio, requiere número de planilla.

**Código operador:** `ss`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="280">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>sgTipoAporte</td><td>1 = Voluntario · 2 = Obligatorio</td><td>string</td><td>"1"</td></tr><tr><td>sgOpPlanilla</td><td>Operador de planilla: 83 = Mi Planilla · 86 = Asopagos · 88 = Simple · 89 = Arus · 94 = Aporte en Línea · SOI = Otro</td><td>string</td><td>"83"</td></tr><tr><td>sgTipoDoc</td><td>Tipo de documento (numérico)</td><td>string</td><td>"01"</td></tr><tr><td>sgDocUsuario</td><td>Documento del aportante</td><td>string</td><td>"1020304050"</td></tr><tr><td>sgNuPlanilla</td><td>Número de planilla (obligatorio si <code>sgTipoAporte = 2</code>)</td><td>string</td><td>"PLA-9876"</td></tr></tbody></table>

```php
$data = json_encode([
    "sgTipoAporte"  => "2",
    "sgOpPlanilla"  => "83",
    "sgTipoDoc"     => "01",
    "sgDocUsuario"  => "1020304050",
    "sgNuPlanilla"  => "9876543"
]);

$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'ss',
    'valor' => '150000',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

```json
// Error por planilla faltante en aporte obligatorio
{ "reply":"errord", "descerr":"Debes enviar un numero de planilla valido por el tipo de aporte" }
```
{% endtab %}

{% tab title="Depósito tpaga (dp)" %}

Pago de una **referencia tpaga** previamente consultada. La consulta retorna el monto, la referencia, el order ID y el correo; el `pracRec` solo necesita el `tx_id`.

**Código operador:** `dp`
**PreConsulta:** obligatoria — el proceso interno guarda la información en `switchData` y devuelve el `tx_id`.

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>tx_id</td><td>ID de la consulta previa (tabla <code>switchData</code>)</td><td>string</td><td>"1234567"</td></tr></tbody></table>

```php
$data = json_encode(["tx_id" => "1234567"]);

$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'dp',
    'valor' => '80000',                 // monto devuelto por la consulta
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

{% hint style="info" %}
Si la consulta previa indica `pagoParcial = 1`, el comercio puede enviar un `valor` menor al total; en caso contrario se debe pagar el monto exacto devuelto.
{% endhint %}

```json
// Errores
{ "reply":"errord", "descerr":"Error consultando transaccion" }   // tx_id no existe
{ "reply":"errord", "descerr":"Faltan Datos" }                     // tx_id ausente
```
{% endtab %}

{% tab title="Seguro GEA (ga)" %}

Producto de seguro con **KYC completo** del adquirente. El sistema valida:

* `documentoGa` — entero con mínimo 4 dígitos.
* `nombreGa` — alfanumérico mínimo 4 caracteres (solo letras, números y espacios).
* `emailGa` — mínimo 4 caracteres; se sanitiza a `[A-Za-z0-9._@-]`.

**Código operador:** `ga`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>documentoGa</td><td>Documento del adquirente (mínimo 4 dígitos)</td><td>string</td><td>"1020304050"</td></tr><tr><td>nombreGa</td><td>Nombre y apellidos (mínimo 4 caracteres)</td><td>string</td><td>"JUAN PEREZ"</td></tr><tr><td>emailGa</td><td>Correo electrónico del adquirente</td><td>string</td><td>"juan@correo.com"</td></tr></tbody></table>

```php
$data = json_encode([
    "documentoGa" => "1020304050",
    "nombreGa"    => "JUAN PEREZ",
    "emailGa"     => "juan@correo.com"
]);

$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'ga',
    'valor' => 'GEA_BASICO',            // id del plan a vender
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

```json
// Errores de KYC
{ "reply":"errord", "descerr":"El Nombre digitado no es valido" }   // < 4 caracteres
{ "reply":"errord", "descerr":"El Email digitado no es valido" }    // < 4 caracteres
{ "reply":"errord", "descerr":"El Documento digitado no es valido" }// < 4 dígitos
```
{% endtab %}

{% tab title="Bwin Apuestas (bw)" %}

Recarga de cuenta Bwin para apuestas online. Flujo en dos pasos: **validar documento + monto** para obtener un `idPre`, luego **vender** referenciando ese `idPre`. El servidor fija el valor real de venta a partir de lo autorizado por Bwin (no del `valor` enviado en `pracRec`).

**Código operador:** `bw`
**PreConsulta:** obligatoria — proceso interno `bwinCheckDoc` (ruta `conexredRest.consultaApuestas`).

### 1. Validar documento y obtener `idPre`

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>tipoConsulta</td><td>Nombre del proceso interno</td><td>string</td><td>"bwinCheckDoc"</td></tr><tr><td>cedula</td><td>Cédula del apostador (mínimo 5 dígitos)</td><td>string</td><td>"1020304050"</td></tr><tr><td>valorCompra</td><td>Valor solicitado por el cliente (mínimo 10.000)</td><td>string</td><td>"20000"</td></tr></tbody></table>

```php
$params = [
    'idcomercio'   => 01234,
    'claveventa'   => 1234,
    'tipoConsulta' => 'bwinCheckDoc',
    'data'         => json_encode([
        "cedula"      => "1020304050",
        "valorCompra" => "20000"
    ])
];
$response = $client->call('preConsulta', $params);
```

```json
// Response exitoso
{ "reply":"ok", "idPre":"1234567" }

// Errores
{ "reply":"Error en el documento cliente" }               // cédula < 5 dígitos
{ "reply":"El valor minimo de compra es de 10.000 pesos" } // valorCompra < 10000
```

### 2. Ejecutar venta (pracRec)

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idPre</td><td>ID devuelto por la validación previa (solo números)</td><td>string</td><td>"1234567"</td></tr></tbody></table>

```php
$data = json_encode(["idPre" => "1234567"]);

$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'bw',
    'valor' => '0',                     // el servidor fija el valor real de venta
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

```json
// Error de idPre vencido o inexistente
{ "reply":"errord", "descerr":"Datos erroneos para esta venta (-2)" }
```

{% hint style="info" %}
El `idPre` tiene una vida corta (se almacena en <code>switchData</code>). Si pasa mucho tiempo entre el paso 1 y el 2, vuelva a ejecutar la validación.
{% endhint %}
{% endtab %}
{% endtabs %}

***

## Estados específicos

| estado | Significado                                                  |
| ------ | ------------------------------------------------------------ |
| 00     | Operación exitosa                                            |
| 05     | Validación rechazada / referencia inválida / premio no pagable|
| 99     | Sin respuesta del proveedor (Baloto, Colpensiones, tpaga, GEA)|
