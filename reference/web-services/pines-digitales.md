# Pines Digitales

Los pines digitales son productos de valor variable (gaming, cine, streaming, seguridad, facturación). Todos siguen el **mismo flujo de dos pasos**:

1. **`consultaProducto`** con el código del producto → devuelve la lista de pines disponibles con `id`, `valor` y `descripción`.
2. **`pracRec`** con `operador = código del producto` y `valor = id del pin seleccionado`. El campo raíz `celular` acepta el número destino para SMS, o el genérico `3100000000` si el comercio entrega el pin impreso.

{% hint style="info" %}
El código `tipo` en `consultaProducto` debe ser `2` para todos los pines de este catálogo.
{% endhint %}

***

## Catálogo de productos

| Código | Nombre comercial                 | Requiere email cliente |
| ------ | -------------------------------- | ---------------------- |
| `ro`   | Roblox Pin                       | —                      |
| `mt`   | Minecraft Pin                    | —                      |
| `mf`   | McAfee Pin                       | —                      |
| `ff`   | FreeFire Pin                     | —                      |
| `dg`   | DirecTV GO Pin                   | —                      |
| `ck`   | Cinemark Pin Cinecombo           | —                      |
| `c2`   | Cinemark Pin Confitería          | —                      |
| `c3`   | Procinal Pin Cinecombo           | —                      |
| `c4`   | Procinal Pin Confitería          | —                      |
| `c5`   | Cinecolombia Pin Cinecombo       | —                      |
| `c6`   | Cinecolombia Pin Confitería      | —                      |
| `fe`   | Pin Factura Electrónica          | ✅                      |

***

## Flujo estándar

Aplica a: `ro, mt, mf, ff, dg, ck, c2, c3, c4, c5, c6`

### 1. Consultar pines disponibles

```php
$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'producto'   => 'ro',
];
$response = $client->call('consultaProducto', $params);
```

```json
// Response
{
  "estado":"00",
  "respuesta":"ok",
  "productos":[
    { "categoryId":"ro", "practiCode":"ROBLOX_10", "productDesc":"Roblox 10 USD", "cost":45000, "validity":0 },
    { "categoryId":"ro", "practiCode":"ROBLOX_25", "productDesc":"Roblox 25 USD", "cost":105000, "validity":0 }
  ]
}
```

### 2. Vender el pin

```php
$data = json_encode(["telEnvio" => "3100000000"]);

$params = [
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans'    => 123456,
    'celular'    => '3100000000',
    'operador'   => 'ro',
    'valor'      => 'ROBLOX_10',   // practiCode devuelto por consultaProducto
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```

```json
// Response exitoso
{
  "idtrans":123456, "estado":"00", "respuesta":"Transaccion exitosa", "codigoauth":"123456",
  "saldo":1000, "codop":"123456",
  "jsonAdicional":{ "info":"ROBLOX-PIN: XXXX-YYYY-ZZZZ" }
}
```

{% hint style="warning" %}
Si el comercio desea que Practisistemas envíe el pin por SMS, debe poner en `celular` el número real del cliente (10 dígitos). Si lo entrega impreso/manualmente, usar `3100000000` como genérico y poner ese mismo valor en `jsonAdicional.telEnvio`.
{% endhint %}

***

## Caso especial — Pin Factura Electrónica (fe)

{% tabs %}
{% tab title="fe" %}

Además del celular destino, requiere el **correo electrónico** del cliente donde se facturará el producto.

**Código operador:** `fe`

<table><thead><tr><th width="170">Campo jsonAdicional</th><th width="260">Detalle</th><th width="110">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>telEnvio</td><td>Celular para SMS del pin (o genérico <code>3100000000</code>)</td><td>string</td><td>"3100000000"</td></tr><tr><td>email</td><td>Correo electrónico del cliente</td><td>string</td><td>"cliente@correo.com"</td></tr></tbody></table>

```php
$data = json_encode([
    "telEnvio" => "3100000000",
    "email"    => "cliente@correo.com"
]);

$params = [
    'idcomercio' => 01234, 'claveventa' => 1234, 'idtrans' => 123456,
    'celular' => '3100000000', 'operador' => 'fe',
    'valor' => 'FE_PLAN_100',
    'jsonAdicional' => $data,
];
$response = $client->call('pracRec', $params);
```
{% endtab %}

{% endtabs %}

***

## Estados específicos

| estado | Significado                                              |
| ------ | -------------------------------------------------------- |
| 00     | Venta exitosa, pin devuelto en `jsonAdicional.info`      |
| 05     | Error (pin agotado, id inválido, datos KYC incorrectos)  |
| 99     | Sin respuesta del proveedor del pin                      |
