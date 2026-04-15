# Payments API

Servicio SOAP dedicado a flujos de **recaudo y consignaciones**: QR Movii, Código de Barras, PSE y consulta de Depósitos. Vive en un endpoint independiente de la API de recargas.

* **WSDL pruebas:** `https://recargas.practisistemas.com/pruebaws/paymentsApi_v2.php?wsdl`
* **Namespace:** `urn:PractiWs`
* **Autenticación:** `idcomercio` + `claveventa` + IP origen (misma de la API de recargas).

{% hint style="info" %}
Los métodos de **Bre-B** viven en este mismo endpoint pero están documentados en su [página dedicada](bre-b.md).
{% endhint %}

***

## Familias de métodos

| Familia        | Métodos                                                                            |
| -------------- | ---------------------------------------------------------------------------------- |
| QR Movii       | `generarQr`, `obtenerPagosQr`, `obtenerPagosDiaQr`                                 |
| Código Barras  | `generarBarcode`, `obtenerPagosBarcode`                                            |
| PSE            | `generarLinkPse`, `obtenerPagosPse`                                                |
| Depósitos      | `obtenerDepositos`, `obtenerLinkDepositos`                                         |
| [Bre-B](bre-b.md) | 8 métodos — ver página dedicada                                                 |

***

## QR Movii

{% tabs %}
{% tab title="generarQr" %}

Genera la imagen del QR Movii asignado al comercio/subcomercio para recibir pagos.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>subComercio</td><td>Id del sub-punto que recibirá los pagos</td><td>string</td><td>"1"</td></tr></tbody></table>

```php
$params = [
    'idcomercio'  => 01234,
    'claveventa'  => '1234',
    'subComercio' => '1',
];
$response = $client->call('generarQr', $params);
```

```json
// jsonData
{
  "code":"00",
  "reply":"ok",
  "qrImage":"data:image/png;base64,iVBORw0KGgo...",
  "sessionQr":"987654"
}
```

Guardar el `sessionQr` para consultar después los pagos recibidos en esa sesión.
{% endtab %}

{% tab title="obtenerPagosQr" %}

Lista los pagos recibidos en una sesión QR específica.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>idSesion</td><td>sessionQr devuelto por <code>generarQr</code></td><td>string</td><td>"987654"</td></tr></tbody></table>

```json
// jsonData
{
  "reply":"ok",
  "statusQr":"1",
  "statusConsulta":"00",
  "replyConsulta":"ok",
  "pagos":{
    "555":{ "fecha":"2026-04-15 10:30:00", "idUsuario":1234, "subPunto":"1", "valorPago":15000 }
  }
}

// Sesión no válida
{ "reply":"ok", "statusQr":"05", "replyConsulta":"No se encuentra sesion relacionada al comercio" }
```
{% endtab %}

{% tab title="obtenerPagosDiaQr" %}

Lista todos los pagos QR de un día completo (independiente de sesión).

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>fecha</td><td>Fecha a consultar (formato libre; se normaliza a Y-m-d)</td><td>string</td><td>"2026-04-15"</td></tr></tbody></table>

```json
{
  "pagos":{
    "555":{ "fecha":"2026-04-15 10:30:00", "idUsuario":1234, "subPunto":"1", "valorPago":15000, "sesion":"987654" },
    "556":{ "fecha":"2026-04-15 14:22:00", "idUsuario":1234, "subPunto":"2", "valorPago":8000,  "sesion":"987655" }
  }
}
```
{% endtab %}
{% endtabs %}

***

## Código de Barras

{% tabs %}
{% tab title="generarBarcode" %}

Emite un código de barras único para que el cliente consigne en bancos vía convenio.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr></tbody></table>

```json
// jsonData
{
  "reply":"ok",
  "id":1234567,
  "barCode":"41577099982390678020000012345679"
}
```

El `barCode` se usa directamente en el convenio bancario del cliente. El `id` es la referencia interna para consultar pagos después.
{% endtab %}

{% tab title="obtenerPagosBarcode" %}

Lista los pagos recibidos por código de barras en un día.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>fecha</td><td>Fecha a consultar</td><td>string</td><td>"2026-04-15"</td></tr></tbody></table>

```json
{
  "reply":"ok",
  "pagos":{
    "777":{ "fecha":"2026-04-15 11:00:00", "idUsuario":1234, "valorPago":120000, "idBarcode":1234567, "idBanco":"0001" }
  }
}
```
{% endtab %}
{% endtabs %}

***

## PSE

{% tabs %}
{% tab title="generarLinkPse" %}

Genera un link de pago PSE con los datos del cliente pre-llenados.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>valorPago</td><td>Valor a pagar (mínimo 100)</td><td>string</td><td>"50000"</td></tr><tr><td>descripcion</td><td>Descripción corta (se sanitiza a alfanumérico)</td><td>string</td><td>"Pago servicio"</td></tr><tr><td>document</td><td>Documento del cliente</td><td>string</td><td>"1020304050"</td></tr><tr><td>mail</td><td>Correo del cliente</td><td>string</td><td>"cliente@correo.com"</td></tr><tr><td>name</td><td>Primer nombre</td><td>string</td><td>"Juan"</td></tr><tr><td>secondName</td><td>Apellido</td><td>string</td><td>"Perez"</td></tr><tr><td>phone</td><td>Celular</td><td>string</td><td>"3100000000"</td></tr><tr><td>description</td><td>Descripción extendida (mostrada al pagador)</td><td>string</td><td>"Pago factura 100"</td></tr></tbody></table>

```json
// Response exitoso
{
  "status":"00",
  "id":"1234",
  "hash":"a1b2c3d4e5f6...",
  "linkPago":"https://pay.praw.biz/pagos/a1b2c3d4e5f6..."
}

// Valor insuficiente
{ "status":"02", "desc":"el valor minimo es de 10.000 pesos" }
```
{% endtab %}

{% tab title="obtenerPagosPse" %}

Consulta el estado de un pago PSE previamente generado.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>idPago</td><td>ID devuelto por <code>generarLinkPse</code></td><td>string</td><td>"1234"</td></tr></tbody></table>

```json
// Response
{
  "status":"00",
  "desc":"pago encontrado",
  "id":"1234",
  "hash":"a1b2c3d4e5f6...",
  "statusPayment":"OK",
  "idPse":"PSE-555",
  "value":"50000"
}
```

Valores típicos de `statusPayment`: `PENDING`, `OK`, `FAILED`, `REJECTED`.
{% endtab %}
{% endtabs %}

***

## Depósitos

{% tabs %}
{% tab title="obtenerDepositos" %}

Lista los depósitos (consignaciones) del comercio en una ventana de tiempo.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>fecha</td><td>Día a consultar</td><td>string</td><td>"2026-04-15"</td></tr><tr><td>hora</td><td>Hora base HH:MM:SS (24h)</td><td>string</td><td>"08:00:00"</td></tr><tr><td>filtrarApi</td><td>1 = solo depósitos con referencia API</td><td>string</td><td>"1"</td></tr><tr><td>filtrarAuth</td><td>1 = solo autorizados</td><td>string</td><td>"1"</td></tr></tbody></table>

```json
{
  "status":"ok",
  "desc":"consulta correcta",
  "jsonData":"{ \"pagos\":{ \"999\":{ \"id\":999, \"fechaReporte\":\"2026-04-15 09:15:00\", \"cantidad\":500000, \"fechaConsignacion\":\"2026-04-15\", \"fechaAutorizacion\":\"2026-04-15 09:30:00\", \"idbanco\":\"0001\", \"estado\":1, \"info\":\"...\", \"respuesta\":\"OK\", \"referenciaApi\":\"REF-123\" } } }"
}

// Hora mal formada
{ "status":"03", "desc":"Hora no valida" }
// Parámetros vacíos
{ "status":"-1", "desc":"datos incompletos" }
```

Campo `estado`: `0` pendiente · `1` autorizada · `2` rechazada.
{% endtab %}

{% tab title="obtenerLinkDepositos" %}

Obtiene el link de la página pública donde el cliente puede subir su comprobante de consignación, referenciado al comercio.

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>string</td><td>"1234"</td></tr><tr><td>referencia</td><td>Referencia externa del comercio</td><td>string</td><td>"REF-ABC-001"</td></tr></tbody></table>

```json
// Response exitoso
{
  "status":"00",
  "desc":"",
  "url":"https://consig.praw.biz/consignaciones/<hash>/REF-ABC-001"
}

// Comercio sin hash configurado
{ "status":"02", "desc":"No existe hash para este comercio" }
```
{% endtab %}
{% endtabs %}

***

## Códigos de estado comunes

| status | Significado                                                     |
| ------ | --------------------------------------------------------------- |
| 00     | Operación exitosa                                               |
| 01     | Error de autenticación (idcomercio / clave / IP origen)          |
| 02     | Error de configuración o parámetro específico del método         |
| 03     | Formato de dato inválido (fecha, hora)                           |
| -1     | Datos incompletos                                                |
