# Bre-B

Bre-B es el sistema interoperable de pagos inmediatos. Practisistemas expone un conjunto de **8 métodos SOAP** dedicados, servidos desde un endpoint separado:

* **WSDL pruebas**: `https://recargas.practisistemas.com/pruebaws/paymentsApi_v2.php?wsdl`
* **Namespace**: `urn:PractiWs`

{% hint style="warning" %}
Antes de consumir cualquier método, el comercio debe tener activado `breb` en su perfil y estar registrado y activo en la plataforma Bre-B de Practisistemas. De lo contrario se devuelve `status=02` o `status=03`.
{% endhint %}

## Estados comunes

| status | Significado                                 |
| ------ | ------------------------------------------- |
| 00     | Operación exitosa                           |
| 01     | Error de autenticación (idcomercio / clave) |
| 02     | Comercio no tiene acceso a Bre-B            |
| 03     | Comercio no registrado en Bre-B             |
| 04     | Datos de entrada inválidos                  |
| 05     | Registro no encontrado                      |
| 99     | Error genérico / sin respuesta upstream     |

***

## Métodos

{% tabs %}
{% tab title="brebGenerateQrDynamic" %}

Genera un **QR dinámico** con monto fijo para un cobro puntual.

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio dado por plataforma</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta dada por plataforma</td><td>int</td><td>1234</td></tr><tr><td>monto</td><td>Valor a cobrar (1 – 50.000.000)</td><td>string</td><td>"25000"</td></tr><tr><td>transactionId</td><td>Id único del comercio para rastreo</td><td>string</td><td>"TX-001"</td></tr></tbody></table>

```php
$params = array(
    'idcomercio'    => 01234,
    'claveventa'    => '1234',
    'monto'         => '25000',
    'transactionId' => 'TX-001',
);
$client   = clientSoap();
$response = $client->call('brebGenerateQrDynamic', $params);
```

```json
// Response exitoso (jsonData decodificado)
{
  "status": "00",
  "desc": "QR generado",
  "qrImage": "data:image/png;base64,iVBORw0KGgo...",
  "transactionId": "TX-001",
  "keyValue": "brebmicomercio"
}

// Response error (monto fuera de rango)
{ "status": "04", "desc": "Monto maximo 50.000.000 pesos" }
```
{% endtab %}

{% tab title="brebGenerateQrStatic" %}

Devuelve el **QR estático** del comercio (reutilizable, sin monto fijo).

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr></tbody></table>

```php
$params   = array('idcomercio' => 01234, 'claveventa' => '1234');
$response = $client->call('brebGenerateQrStatic', $params);
```

```json
{
  "status": "00",
  "desc": "QR estatico",
  "qrImage": "data:image/png;base64,iVBORw0KGgo...",
  "keyValue": "brebmicomercio",
  "commerceName": "COMERCIO DEMO"
}
```
{% endtab %}

{% tab title="brebCreateKey" %}

Crea una **llave alfanumérica Bre-B** asociada al comercio.

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr><tr><td>amount</td><td>Monto de referencia asociado a la llave (1 – 50.000.000)</td><td>string</td><td>"10000"</td></tr></tbody></table>

```json
// Response exitoso
{ "status":"00", "desc":"Llave creada", "keyValue":"brebmicomercio", "keyType":"ALIAS", "amount":10000 }
```
{% endtab %}

{% tab title="brebGetKey" %}

Obtiene **todas las llaves** del comercio, más la llave principal activa.

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr></tbody></table>

```json
{
  "status":"00",
  "desc":"Llaves obtenidas",
  "keyValue":"brebmicomercio",
  "keys":[ { "keyValue":"brebmicomercio", "keyType":"ALIAS", "status":"ACTIVE" } ]
}
```
{% endtab %}

{% tab title="brebGetTransactions" %}

Lista transacciones Bre-B del comercio en un rango de fechas (máx. **1 día**, 200 registros).

<table><thead><tr><th width="170">Parámetro</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr><tr><td>fechaInicio</td><td>Formato Y-m-d</td><td>string</td><td>"2026-04-15"</td></tr><tr><td>fechaFin</td><td>Formato Y-m-d (mismo día)</td><td>string</td><td>"2026-04-15"</td></tr><tr><td>filtroTipo</td><td>Opcional: <code>qr_id</code> o <code>llave</code></td><td>string</td><td>"qr_id"</td></tr><tr><td>filtroValor</td><td>Valor asociado al filtro</td><td>string</td><td>"TX-001"</td></tr></tbody></table>

```json
{
  "status":"00", "desc":"ok", "total":1,
  "transacciones":[ {
      "id": 123,
      "qrType":"DIN", "amount":25000,
      "creditorKey":"brebmicomercio", "qrRefId":"...",
      "description":"Venta QR", "transactionId":"TX-001"
  } ]
}
```
{% endtab %}

{% tab title="brebGetTransaction" %}

Consulta una **transacción puntual** por `idTransaccion` interno.

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr><tr><td>idTransaccion</td><td>ID interno de la transacción</td><td>string</td><td>"123"</td></tr></tbody></table>

```json
{ "status":"00", "desc":"ok", "transaccion": { /* misma forma que brebGetTransactions */ } }
```
{% endtab %}

{% tab title="brebResolveKey" %}

Resuelve una llave (alias, cédula o celular) y devuelve un `resolveId` de corta vida usado después por `brebSendToKey`.

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr><tr><td>keyValue</td><td>Llave a consultar (mín. 3 caracteres)</td><td>string</td><td>"brebjuanp"</td></tr></tbody></table>

```json
{
  "status":"00", "desc":"Llave encontrada",
  "resolveId":"res_abc123",
  "keyData": { "keyValue":"brebjuanp", "accountHolderName":"J***N P***Z", "bankName":"BANCO EJEMPLO" }
}
```
{% endtab %}

{% tab title="brebSendToKey" %}

Envía dinero a una llave previamente resuelta. **Flujo obligatorio**: primero `brebResolveKey` → guardar `resolveId` → llamar a este método.

<table><thead><tr><th width="170">Parámetro</th><th width="240">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta</td><td>int</td><td>1234</td></tr><tr><td>resolveId</td><td>Devuelto por <code>brebResolveKey</code></td><td>string</td><td>"res_abc123"</td></tr><tr><td>amount</td><td>Monto a enviar (100 – 5.000.000)</td><td>string</td><td>"15000"</td></tr><tr><td>note</td><td>Descripción; si vacío → "Transferencia Bre-B"</td><td>string</td><td>"Pago servicio"</td></tr></tbody></table>

```json
// Response exitoso
{
  "status":"00", "desc":"Envio exitoso",
  "transactionId":"MVI-789",
  "amount":15000
}
```
{% endtab %}
{% endtabs %}
