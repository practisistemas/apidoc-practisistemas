# SOAP

{% embed url="https://recargas.practisistemas.com/pruebaws/practiWs.php?wsdl=" %}
URL PRUEBAS 🧪
{% endembed %}

## Métodos

{% tabs %}
{% tab title="pracRec" %}


Método para realizar venta de recarga y paquetes

![Diagrama de secuencia pracRec](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2F4q6ePgZ3my2vQPwfpcBY%2Fimage.png?alt=media\&token=73192690-c34f-4dbb-9548-496b19b042f5)



<table><thead><tr><th width="150">Parámetro</th><th width="150">Detalle</th><th width="150">Tipo</th><th width="150">Ejemplo</th><th>Nulo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio dada por plataforma</td><td>int</td><td>01234</td><td>No</td></tr><tr><td>claveventa</td><td>Clave venta dada por plataforma</td><td>int</td><td>1234</td><td>No</td></tr><tr><td>idtrans</td><td>Id transacción del cliente para futuras consultas</td><td>Bigint(20 caracteres)</td><td>123456</td><td>No</td></tr><tr><td>Celular</td><td>Número del celular o tarjeta</td><td>char</td><td>3108390075</td><td>No</td></tr><tr><td>operador</td><td>Código del operador(ver tabla códigos de operador)</td><td>char</td><td>cl</td><td>No</td></tr><tr><td>valor</td><td>Valor de la recarga o código de paquete(aplicable para los distintos tipos de paquetes prepago)</td><td>int</td><td>1000 (recarga) 67(paquete)</td><td>No</td></tr><tr><td>jsonAdicional</td><td>Json adicional para algunos productos(ver tabla descripción datos adicionales productos), Este campo no es Obligatorio</td><td>JSON</td><td>{"circulo":"002","matricula":"1"}</td><td>No (enviar JSON vacío si es el caso)</td></tr></tbody></table>



**Respuesta**



<table><thead><tr><th width="150">Parámetros</th><th>Detalle</th><th>Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idtrans</td><td>Id Transacción de practisistemas(para futuras referencias)</td><td>int</td><td>01234</td></tr><tr><td>estado</td><td>Estado de la transacción ( <mark style="background-color:green;">00 - correcta</mark>; <mark style="background-color:red;">05 - incorrecta</mark>; <mark style="background-color:yellow;">99 - sin respuesta</mark> )</td><td>char</td><td>00</td></tr><tr><td>respuesta</td><td>Descripción textual transacción</td><td>char</td><td>El número no pertenece al operador</td></tr><tr><td>codigoauth</td><td>Código Autorización(solo si estado 00)</td><td>char</td><td>123456</td></tr><tr><td>saldo</td><td>Saldo del cliente después de trx</td><td>Int</td><td>900000</td></tr><tr><td>codop</td><td>Código de error del operador(si aplica)</td><td>int</td><td>123</td></tr><tr><td>json</td><td>Adicional Json adicional respuesta para algunos productos(ver tabla descripción datos adicionales productos), Este campo no es Obligatorio</td><td>JSON</td><td>{"circulo":"002","matricula":"1"}</td></tr></tbody></table>



**Prueba**

```php

//Ejemplo metodo pracRec
$params = array(
        'idcomercio' => 01234,
        'claveventa' => 1234,
        'idtrans' => 123456,
        'celular' => '3108390075',
        'operador' => 'cl',
        'valor' => 3000000000,
        'jsonAdicional' => '{}',
);

$client = clientSoap();
$response = $client->call('pracRec', $params);
  
```

```php
//Response exitoso
	Array ( 
		[idtrans] => 123456 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => 
	)
//Response Erroneos
	//  No saldo
	Array ( 
		[idtrans] => 123456 
		[estado] => 05 
		[respuesta] => nosaldo 
		[codigoauth] => 
		[saldo] => 1000 
		[codop] => 001 
	)
	// Numero no corresponde al operador
	Array ( 
		[idtrans] => 123456 
		[estado] => 05 
		[respuesta] => Transaccion no exitosa 
		[codigoauth] => 
		[saldo] => 1000 
		[codop] => 001 
	)
```
{% endtab %}

{% tab title="consRec" %}


Este método solo deberá ser usado en caso de no recibir respuesta del método de recarga. <mark style="color:red;">TimeOut 60 seg</mark>

![Diagrama de secuencia consRec](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2FXSCu40TEmW1uGKzn356y%2Fimage.png?alt=media\&token=b6c18277-d570-46ca-b06d-47f08a99cf3f)



<table><thead><tr><th width="150">Parámetro</th><th width="150">Detalle</th><th width="150">Tipo</th><th width="150">Ejemplo</th><th>Nulo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio dada por plataforma</td><td>int</td><td>01234</td><td>No</td></tr><tr><td>claveventa</td><td>Clave venta dada por plataforma</td><td>int</td><td>1234</td><td>No</td></tr><tr><td>fecha</td><td>Fecha de la transacción (Y-m-d)</td><td>char</td><td>2018-01-24</td><td>No</td></tr><tr><td>idtrans</td><td>Id interno enviado en la recarga por parte del cliente</td><td>int</td><td>1234567</td><td>No</td></tr></tbody></table>



**Respuesta**



<table><thead><tr><th width="150">Parámetros</th><th width="150">Detalle</th><th width="150">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idtrans</td><td>Id interno enviado en la recarga por parte del cliente</td><td>int</td><td>1234567</td></tr><tr><td>estado</td><td>Estado de la transacción ( <mark style="background-color:green;">00 - correcta</mark>; <mark style="background-color:red;">05 - incorrecta</mark>; <mark style="background-color:yellow;">99 - sin respuesta</mark> )</td><td>char</td><td>00</td></tr><tr><td>respuesta</td><td>Descripción textual transacción</td><td>char</td><td>El número no pertenece al operador</td></tr><tr><td>codigoauth</td><td>Código Autorización(solo si estado 00)</td><td>char</td><td>123456</td></tr><tr><td>saldo</td><td>Saldo del cliente actual</td><td>decimal</td><td>900000</td></tr><tr><td>codop</td><td>Código de error del operador(si aplica)</td><td>int</td><td>123</td></tr></tbody></table>



**Prueba**

```php

$param = array(
        'idcomercio' => 01234,
        'claveventa' => 1234,
        'fecha' => '2018-01-24',
        'idtrans' => 12345,
);

$client = clientSoap();
$response = $client->call('consRec', $param);
  
```

```php

//Response exitoso
	Array ( 
		[idtrans] => 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 
		[saldo] => 1000 
		[codop] => 001 
	)
	
```


{% endtab %}

{% tab title="cSaldo" %}


Método usado por parte del cliente para consultar el saldo disponible en el comercio

![Diagrama de secuencia cSaldo](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2FsDNsVFvB2qL80eIcwZ8r%2Fimage.png?alt=media\&token=501fc392-1f47-4922-8c12-b4020ac9ec23)



<table><thead><tr><th width="150">Parámetro</th><th width="150">Detalle</th><th width="150">Tipo</th><th width="150">Ejemplo</th><th>Nulo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio dada por plataforma</td><td>int</td><td>01234</td><td>No</td></tr><tr><td>claveventa</td><td>Clave venta dada por plataforma</td><td>int</td><td>1234</td><td>No</td></tr></tbody></table>



**Respuesta**



| Parámetros | Detalle                                                                                                                                                | Tipo    | Ejemplo |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ------- | ------- |
| estado     | Estado de la consulta ( <mark style="background-color:green;">00 - correcta</mark>;  <mark style="background-color:red;">05 - error de datos</mark>; ) | char    | 00      |
| saldo      | Saldo del cliente                                                                                                                                      | Int     | 10000   |
| saldomp    | Saldo multiproducto cliente                                                                                                                            | decimal | 10000   |



**Prueba**



```php

$param = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
);

$client = clientSoap();
$response = $client->call('cSaldo', $param);

```



```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[saldo] => 55000 
	)
```
{% endtab %}

{% tab title="preConsulta" %}


Método usado por parte del cliente para hacer preConsulta para algunos productos.

![Diagrama de secuencia preConsulta](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2FkEFpi4kZEvfYid2Y4QGu%2Fimage.png?alt=media\&token=6b62c10d-4c5e-42ad-a039-817233dd26b1)

<table><thead><tr><th width="150">Parámetro</th><th width="150">Detalle</th><th width="150">Tipo</th><th width="150">Ejemplo</th><th>Nulo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio dada por plataforma</td><td>int</td><td>01234</td><td>No</td></tr><tr><td>claveventa</td><td>Clave venta dada por plataforma</td><td>int</td><td>1234</td><td>No</td></tr><tr><td>tipoConsulta</td><td>El tipo de consulta disponible(ver tablas descripción tipos pre consulta)</td><td>string</td><td>productos_consulta</td><td>No</td></tr><tr><td>data</td><td>Json conteniendo data a consultar</td><td>JSON</td><td>{"tipo":"1"}</td><td>No (enviar JSON vacío si es el caso)</td></tr><tr><td>idTrx</td><td>Id transacción cliente</td><td>int</td><td>1</td><td>No</td></tr></tbody></table>



{% hint style="warning" %}
#### Para consultar productos:

* Tipo: 1(Recargas)
* Tipo: 2(Pines)
* Tipo: 0(Multiproducto)


{% endhint %}

```json

{"tipo":"1"} //productos de recargas

```



**Respuesta**



<table data-header-hidden><thead><tr><th width="150">Parámetros</th><th width="150">Detalle</th><th width="150">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>estado</td><td>Estado de la consulta ( 00 - correcta; 05 - error de datos; )</td><td>char</td><td>00</td></tr><tr><td>respuesta</td><td>Respuesta textual consulta</td><td>string</td><td>Consulta Correcta</td></tr><tr><td>data</td><td>Data consulta respuesta ( ver tablas descripción tipos preConsulta )</td><td>json</td><td>Ver siguiente <strong>ejemplo</strong> de data a recibir.</td></tr></tbody></table>



> **Ejemplo:**
>
> ```json
>
> {
> 	//**reply =** Respuesta validación documento, ok si se encuentra productos, error si no se encuentran.
> 	"reply":"ok",
> 	//**estado** = Estado consulta productos (00)correcto (05)error.
> 	"estado":"00",
> 	//**productos =** Array con los productos de plataforma, identificados de la siguiente forma:
> 	"productos":
> 	"{ 
> 		//**producto =** código del producto, tiene un array con la descripcion de la siguiente forma.
> 		\\"cl\\":{
> 			//**op =** Código del producto.
> 			\\"op\\":\\"cl\\",
> 			//**desc =** Descripcion del producto, nombre del operador.
> 			\\"desc\\":\\"Claro\\",
> 			//**packs =** Códigos de producto describiendo los paquetes que pertenecen al operador, 
> 			// si llega en blanco si no tiene paquetes.
> 			\\"packs\\":\\"pc\\"
> 		},
> 		\\"mo\\":{
> 			\\"op\\":\\"mo\\",
> 			\\"desc\\":\\"Movistar\\",
> 			\\"packs\\":\\"pm\\"
> 		}
> 	}"
> }
>
> ```
>
>



**Prueba**

```php
// 
$data = json_encode(array("tipo" => "1"));

$param = array(
  'idcomercio' => 01234,
  'claveventa' => 1234,
  'tipoConsulta' => 'productos_consulta',
  'data' => $data,
  'idTrx' => 1,
);

$client = clientSoap();
$response = $client->call('preConsulta', $param);
```



```php
//**Response exitoso**
Array ( 
	[estado] => 00 
	[respuesta] => Consulta Correcta 
	[data] => {
		"reply":"ok",
		"estado":"00",
		"productos":"{
			\\"cl\\":{
				\\"op\\":\\"cl\\",
				\\"isPack\\":\\"0\\",
				\\"desc\\":\\"Claro\\",
				\\"packs\\":\\"pc\\"
			},
			\\"mo\\":{
				\\"op\\":\\"mo\\",
				\\"isPack\\":\\"0\\",
				\\"desc\\":\\"Movistar\\",
				\\"packs\\":\\"pm\\"
			},
			\\"ti\\":{
				\\"op\\":\\"ti\\",
				\\"isPack\\":\\"0\\",
				\\"desc\\":\\"Tigo\\",
				\\"packs\\":\\"pi,pv,pb\\"
			},
			\\"pe\\":{
				\\"op\\":\\"pe\\",
				\\"isPack\\":\\"1\\",
				\\"desc\\":\\"Paquetes Exito\\",
				\\"packs\\":null
			}
		}"
	}
)

//**Response Erroneos**
	// Error en idcomercio o claveventa
	Array ( 
		[estado] => 05 
		[respuesta] => IP NO AUTORIZADA 
		[data] => 
	)
	//  Error en campo **data** 
	Array ( 
		[estado] => 05 
		[respuesta] => Consulta con error 
		[data] =>
	)
	//claveventa o idcomercio no autorizado
	Array ( 
		[estado] => 05 
		[respuesta] => IP NO AUTORIZADA 
		[data] => 
	)
```
{% endtab %}

{% tab title="consultaProducto" %}


Método usado por parte del cliente para hacer consulta de producto de paquetes, o producto pines, para obtener en tiempo real id, descripción y valor de cada uno.

![Diagrama de secuencia consultaProducto](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2FHgwwKoeDBGi1uIkBRd0a%2Fimage.png?alt=media\&token=bd9d0c1d-e478-425b-bcde-ea35ef8146c5)



<table><thead><tr><th width="150">Parámetro</th><th width="150">Detalle</th><th width="150">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>idcomercio</td><td>Id comercio dada por plataforma</td><td>int</td><td>01234</td></tr><tr><td>claveventa</td><td>Clave venta dada por plataforma</td><td>int</td><td>1234</td></tr><tr><td>producto</td><td>Producto a consultar</td><td>string</td><td>pc</td></tr></tbody></table>



**Respuesta**



<table><thead><tr><th width="150">Parámetros</th><th width="199.0769230769231">Detalle</th><th width="150">Tipo</th><th>Ejemplo</th></tr></thead><tbody><tr><td>estado</td><td>Estado de la consulta ( <mark style="background-color:green;">00 - correcta</mark>; <mark style="background-color:red;">05 - incorrecta</mark>; )</td><td>char</td><td>00</td></tr><tr><td>data</td><td>Json conteniendo información del producto</td><td>Json</td><td>Ver siguiente <strong>ejemplo</strong> de data a recibir.</td></tr></tbody></table>

> **Ejemplo:**
>
> ```json
>
> {
> //**estado** = Estado consulta producto (00)correcto (05)error.
> "estado":"00",
> "data":"[
> 	{
> 		\\"68\\":{
> 			//**categoryId =** Código del producto
> 			\\"categoryId\\":\\"pc\\",
> 			//**internalCod** = Código del operador
> 			\\"internalCod\\":\\"50012\\",
> 			//**practiCode =** Código que se debe enviar hacia la plataforma génesis para consumir el paquete o producto
> 			\\"practiCode\\":\\"68\\",
> 			//**productDesc =** Descripción del producto
> 			\\"productDesc\\":\\"60 minutos 2 dias\\",
> 			//**cost** = Costo del producto al mayorista
> 			\\"cost\\":\\"3000\\",
> 			//**sell** = Precio de venta al cliente final
> 			\\"sell\\":\\"3000\\",
> 			//**validity =** (decimal) vigencia del producto en días (si aplica)
> 			\\"validity\\":\\"2.0000\\"
> 		}
> 	},
> 	{
> 		\\"69\\":{
> 			\\"categoryId\\":\\"pc\\",
> 			\\"internalCod\\":\\"50140\\",
> 			\\"practiCode\\":\\"69\\",
> 			\\"productDesc\\":\\"50 minutos 1 dia\\",
> 			\\"cost\\":\\"1000\\",
> 			\\"sell\\":\\"1000\\",
> 			\\"validity\\":\\"1.0000\\"
> 		}
> 	},
> 	...
> ]
> }
>
> ```
>
> \
> **Prueba**
>
> ```php
> $param = array(
>     'idcomercio' => 1425,
>     'claveventa' => 1234,
>     'producto' => 'pc', //Código del producto
>   );
>
>   $client = clientSoap2();
>   $response = $client->call('consultaProducto', $param);
> ```
>
> ```php
> //**Response exitoso**
> Array ( 
> 	[estado] => 00 
> 	[data] => [
> 		{"68":{
> 			//**categoryId** = Código del producto
> 			"categoryId":"pc",
> 			//**internalCod** = Código del operador
> 			"internalCod":"50012",
> 			//**practiCode =** Código que se debe enviar hacia la plataforma génesis para consumir el paquete o producto
> 			"practiCode":"68",
> 			//**productDesc =**  Descripción del producto
> 			"productDesc":"60 minutos 2 dias",
> 			// **cost** Costo del producto al mayorista
> 			"cost":"3000",
> 			// **sell =** Precio de venta al cliente final
> 			"sell":"3000",
> 			//**validity =** (decimal) vigencia del producto en días (si aplica)
> 			"validity":"2.0000"
> 		},
> 		{"69":{
> 			"categoryId":"pc",
> 			"internalCod":"50140",
> 			"practiCode":"69",
> 			"productDesc":"50 minutos 1 dia",
> 			"cost":"1000",
> 			"sell":"1000",
> 			"validity":"1.0000"
> 		}},
> 		...
> 	]
> )
>
> //**Response Erroneos**
> 	//  Error en campo $param
> 	Array ( 
> 		[estado] => 05 
> 		[data] => Datos erroneos143425 
> 	)
>  
> 	//Código del producto no existe
> 	Array ( 
> 		[estado] => 00 
> 		[data] => [] 
> 	)
> ```
{% endtab %}
{% endtabs %}

<details>

<summary>Paquetes</summary>

<mark style="background-color:red;">**Nota:**</mark> Para poder vender este producto se debe agregar en `'valor'` el código del paquete



**Prueba**

```php
//Ejemplo metodo **pracRec**
$params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3214598028',
    'operador' => 'pc',
    'valor' => 61,
    'jsonAdicional' => '{}',
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
		Array ( 
			[idtrans] => 690658960 
			[estado] => 00 
			[respuesta] => Transaccion exitosa 
			[codigoauth] => 123456 
			[saldo] => 1000 
			[codop] => 123456 
			[jsonAdicional] => 
		)
		
//**Response Erroneos**
		//  No saldo
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => nosaldo 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
		// Numero no corresponde al operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Certificado de tradición y libertad</summary>

<mark style="background-color:red;">**Nota:**</mark> Para poder vender este producto debe realizar **Preconsulta** antes.



**Prueba**

Preconsulta de **certTyl**

```php
$data = json_encode(array(
	//**Circulo** = XXX String conteniendo circulo matricula a consultar
	"circulo" =>"002",
	//**Matricula =** XXX String conteniendo numero matricula a consultar
	"matricula"=>"1"
));
  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'certTyl',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			"estado":"00",
			"reply":"ok",
			"respuesta":"CALLE SANTA BARBARA (48) 54-46",
			//dir = XXX String conteniendo direccion
			"dir":"CALLE SANTA BARBARA (48) 54-46",
			//municipio = XXX String conteniendo nombre municipio
			"municipio":"ABEJORRAL",
			"valorVenta":"20500"
		} 
	)
//**Response Erroneos**
// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		//  Error en campo data 
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {"reply":"Error en el codigo de municipio"} 
		)
		// Error en campo data 
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"estado":"05",
				"respuesta":"Error consultando Matricula, Posiblemente no existe",
				"reply":"Error consultando Matricula, Posiblemente no existe"} 
		)
		// Error en campo tipoConsulta
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => 
		)
```



**Prueba**

pracRec **cb**&#x20;

```php
$data = json_encode(array(
	//**circulo =** XXX String conteniendo circulo matricula a consultar	
	"circulo" =>"002",
	//**Matricula =** XXX String conteniendo numero matricula a consultar
	"matricula"=>"1"
));

$params = array(
  'idcomercio' => 01234,
  'claveventa' => 1234,
  'idtrans' => 123456,
  'celular' => '3108390075',
  'operador' => 'cb',
  'valor' => '20500',
  'jsonAdicional' => $data,
);

$client = clientSoap();
$response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
	Array ( 
		[idtrans] => 123456 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			// **Circulo** = XXX String conteniendo circulo
			"Circulo":"002",
			// **Matricula =** XXX String conteniendo matricula
			"Matricula":"1",
			// **Url** = XXX String conteniendo url del certificado generado(este también será enviado al numero celular proporcionado en la venta)
			"Url":"http:\\/\\/dw.praw.biz\\/web\\/dw.php?h=dd42e571d74ca4d8"
		} 
	)
//**Response Erroneos**
	//  Error en campo **data** 
	Array ( 
		[idtrans] => 
		[estado] => 
		[respuesta] => :Datos erroneos para esta venta 
		[codigoauth] => 
		[saldo] => 
		[codop] => 0 
	)
```

</details>

<details>

<summary>Certificado de tradición vehicular RUNT</summary>



**Prueba**

Preconsulta de **hv**

```php

$data = json_encode(array(
	// **placaVh** = XXXXXX String conteniendo placa de vehiculo a expedir certificado e histórico runt
	"placaVh" =>"AAA113"
));
  $params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3214598028',
    'operador' => 'hv',
    'valor' => '1000',
    'jsonAdicional' => $data,
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
  
```

```php

//**Response exitoso**
	Array ( 
		[idtrans] => 123456 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			// **Url =** XXX String conteniendo histórico vehicular
			"Url":"<http://dw.praw.biz/web/dw.php?h=0185e5719305e322>",
			// **Url2 =** XXX String conteniendo histórico propietarios vehiculo
			"Url2":"<http://dw.praw.biz/web/dw.php?h=8f784feb563830a5>"
		} 
	)

//**Response Erroneos**
		//  Error en campo **data** 
		Array ( 
			[idtrans] => 
			[estado] => 
			[respuesta] => :Datos erroneos para esta venta 
			[codigoauth] => 
			[saldo] => 
			[codop] => 0 
		)
		//  Error en campo **jsonAdicional**
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
		
```

</details>

<details>

<summary>SOAT</summary>

Para vender este producto, se debe primero consultar la placa ( Metodo **preConsulta chkPlacaSoat** ), y validar la clase deseada para la venta ( Método **preConsulta valPlacaSoat** ), en ese orden, antes de proceder a ejecutar este método



**Prueba**

Preconsulta de **chkPlacaSoat**



<mark style="background-color:red;">**Nota:**</mark> Para el campo **chkTipoDoc** :

* 1 (cédula)
* 3 (NIT)

```php
$data = json_encode(array(
	//**placa =** XXXXXX String conteniendo matricula vehiculo a consultar
	"placa" =>"kwl59d",
	//**chkPLacaSoat =** numero documento del propietario 
	"chkDocumentoSoat":"6763449",
	//**chkTipoDoc** **=** tipo documento del propietario ( 1 cédula ó 3 NIT )
	"chkTipoDoc":"1"));
  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'chkPlacaSoat',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap2();
  $response = $client->call('preConsulta', $param);
```

<mark style="background-color:red;">**Nota:**</mark> **tx\_id** deberá usarse para método **validación tarifa** y **método venta**

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			// **marca =** XXX String conteniendo marca vehículo
			"marca":"KIA",
			// **linea =** XXX String conteniendo línea vehículo
			"linea":"CERATO PRO SX",
			// **modelo** = XXX String conteniendo modelo vehículo
			"modelo":"2017",
			// **cc** = XXX String conteniendo cilindraje vehículo
			"cc":"1591",
			// **carroceria =** XXX String conteniendo tipo carroceria vehículo
			"tipo_carroceria":"SEDAN",
			// **no_motor** = XXX String conteniendo numero motor vehículo
			"no_motor":"AAAAA",
			// **no_chasis =** XXX String conteniendo numero chasis vehículo
			"no_chasis":"BBBBB",
			// **no_vin =** XXX String conteniendo numero vin vehículo
			"no_vin":"CCCCCCC",
			// **cnt_toneladas =** XXX String conteniendo cantidad toneladas vehículo
			"cnt_toneladas":"0.00",
			// **cnt_ocupantes =** XXX String conteniendo cantidad ocupantes vehículo
			"cnt_ocupantes":"5",
			
			"claseVehiculo":"MOTOCICLETA",
	
			"idClaseVehiculo":"10",
	
			"idTipoServicio":"1",
			// **inicio_vigencia =** xxxx String conteniendo inicio vigencia soat nuevo, si retorna vacio, se obtendrá al validar costo soat
			"inicio_vigencia":"",
			// **fin_vigencia =** xxxx string conteniendo fin vigencia soat nuevo, si retorna vacio se obtendrá valor al validar costo soat
			"fin_vigencia":"",
			// **valor =** xxx string valor soat, si retorna vacío se obtendrá al validar costo soat
			"valor":"",
			// **estado =** estado consulta soat, si 00 correcto, si no código de error
			"estado":"00",
			// **reply** = estado proceso consulta, ok para correcto otro para error// reply = estado proceso consulta, ok para correcto otro para error
			"reply":"ok",
			// **respuesta =** descripción textual estado consulta
			"respuesta":"Consulta Correcta",
			// **clase_soat =** clase sugerida para la venta, corresponde a una de las disponibles en la lista clase_soat_list, puede usar otra en caso de ser necesario
			"clase_soat":"5",
			// **tx_id =** id de consulta en sistema génesis, deberá usarse para método validación tarifa y método venta
			"tx_id":89174,
			// **clase_soat_list =** clases de soat disponibles para venta soat este vehiculo, separadas por “,”
			"clase_soat_list":"5,7"
		} 
	)

//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		//  Error en campo **data** 
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Error en la placa vehiculo"
			} 
		)
		//  Error en campo **tipoConsulta**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Tiempo de espera agotado, Puede intentar realizar venta"
			} 
		)
		
```



**Prueba**

preConsulta **valPlacaSoat**&#x20;

```php
$data = json_encode(array(
	//**placa** -> XXXXXX String conteniendo matricula vehículo a validar tarifa
	"placa" =>"JGV151", 
	//**tx_id**-> xxxx String conteniendo tx_id devuelto en consultar placa soat
	"tx_id" =>"89174", 
	//**claseSoat** -> xxx Clase soat a validar, debe ser una de las devueltas en el campo clase_soat_list devuelto en el método CONSULTA PLACA SOAT
	"claseSoat" =>"7" 
));

$param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'chkPlacaSoat',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			//**valor**-> valor venta final soat para la clase seleccionada
			"valor":"489550",
			//**inicio_vigencia**->Inicio de vigencia del soat a vender
			"inicio_vigencia":"06\\/05\\/2020",
			//**fin_vigencia**-> fin vigencia del soat a vender
			"fin_vigencia":"05\\/05\\/2021",
			//**estado**-> estado consulta validación soat (00)correcto (05)error
			"estado":"00",
			//**reply**-> respuesta validación soat, ok correcto otro para error
			"reply":"ok",
			//**respuesta**-> respuesta textual validación valor soat
			"respuesta":"Consulta Correcta"
		} 
	)
//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		****// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Error (nodata)"
			} 
		)
```



**Prueba**

preConsulta **chkDataDoc&#x20;**<mark style="background-color:red;">**(OPCIONAL)**</mark>&#x20;

```php
$data = json_encode(array( 
	// **nDocumento =** xxxx String Numero Documento
	"nDocumento" =>"12345678", 
	// **tipoDocumento =** xxxx int tipo Documento ( 1 cédula ó 3 NIT )
	"tipoDocumento" =>"1" 
));
$param = array(
  'idcomercio' => 1425,
  'claveventa' => 1234,
  'tipoConsulta' => 'chkDataDoc',
  'data' => $data,
  'idTrx' => 1,
);

$client = clientSoap();
$response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			// **reply =** respuesta validación documento, ok si se encuentra documento, error si no se encuentra
			"reply":"ok",
			// estado = estado consulta documento (00)correcto (05)error
			"estado":"00",
			// **nombre =** Nombre encontrado cliente
			"nombre":"ASTEBIA",
			// **apellido =** Apellido encontrado cliente
			"apellido":"RAMOS",
			// **sexo =** Sexo del cliente
			"sexo":"F",
			
			"ciudadtxt":"CARTAGENA,BOLIVAR",
			// **dptoNum =** Departamento cliente
			"dptoNum":"13",
			// **ciuNum** = Ciudad cliente
			"ciuNum":"13001"
		} 
	)

//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		****// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"error"
			} 
		)
```



**Prueba**

<mark style="background-color:red;">**Nota:**</mark> Para la venta exitosa de SOAT, hay que tener en cuenta que la cédula con la que se consulto la placa ( <mark style="color:red;">`preConsulta chkPlacaSoat`</mark> ), debe ser la misma con la que se realice la venta ( <mark style="color:red;">`nDocumentoSoat`</mark> ).&#x20;

pracRec **so**&#x20;

```php
$data = json_encode(array(
    // nombreSoat =  Nombre del cliente final
    "nombreSoat" =>"ASTEBIA",
    // apellidoSoat =  Apellido del cliente final
    "apellidoSoat" =>"RAMOS",
    // sexoSoat:  =  Sexo del cliente final, M para masculino, F para femenino
    "sexoSoat" =>"F",
    // documentoSoat = tipo de documento, ( 1 cédula ó 3 NIT )
    "documentoSoat" =>"1",
    // nDocumentoSoat =  Numero de documento del cliente final
    "nDocumentoSoat" =>"12345678",
    // ciudadSoat =  Codigo de Ciudad Soat (documento adicional mundial)
    "ciudadSoat" =>"13001",
    // departamentoSoat = Código de Departamento Soat (documento adicional mundial)
    "departamentoSoat" =>"13",
    // direccionSoat =  dirección del cliente final
    "direccionSoat" =>"",
    // emailSoat = correo del cliente final(aquí se envía info de mundial)
    "emailSoat" =>"",
    // modeloCarro =  modelo del carro string obtenido en anterior consulta
    "modeloCarro" =>"2017",
    // placaSoat =  placa del soat enviado en anteriores consultas
    "placaSoat" =>"JGV151",
    // valorSoat = valor final de venta de soat
    "valorSoat" =>"489550",
    // Soattx_id = id transacción obtenido de la primera consulta
    "Soattx_id" =>"89174",
    // tipoSoat =  clase de soat elegido para venta final
    "tipoSoat" =>"5",
  ));
  $params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3214598028',
    'operador' => 'so',
    'valor' => '1000',
    'jsonAdicional' => $data,
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
Array (  
	[idtrans] => 123456 
	[estado] => 00 
	[respuesta] => Transaccion exitosa 
	[codigoauth] => 123456 
	[saldo] => 1000 
	[codop] => 123456 
	[jsonAdicional] => {
		"Placa":"JGV151",
		"Cedula":"12345678",
		"Nombre":"ASTEBIA",
		"Apellido":"RAMOS",
		"Telefono":"3214598028",
		"Numero Soat":"78490935",
		"Valor Prima Soat":487850,
		"Valor RUNT":1700,
		"Terminos y Condiciones":"http:\\/\\/praw.biz\\/a\\/terminosSoat.pdf",
		"Url":"http:\\/\\/dw.praw.biz\\/web\\/dw.php?h=1164f900b8d35653",
		"Emisor":"Mundial"
	} 
)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Facturas</summary>

(El sistema tomara automáticamente la comisió&#x6E;**,** sea factura de energía o gas, o el resto de los convenios) Para vender este producto, se debe primero consultar convenio manual o automático con código de barras&#x20;

Datos adicionales:

**Prueba**

preConsulta **convenios\_consulta**&#x20;

```php
$data = json_encode(array(
	//**key** = Palabras clave busqueda de convenio, en blanco para listar todos disponibles
	"key" =>"CLARO - COMCEL"
));
$param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'convenios_consulta',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {	
			//**reply** = respuesta validación documento, ok si se encuentra convenios, error si no se encuentran
			"reply":"ok",
			"estado":"00",
			//**convenios =** array con los conveios de facturas disponibles, identificados de la siguiente forma:
			"convenios":"{
				//**código del convenio =** código del convenio, contiene array con los siguientes datos
				\\"8313\\":{
			//**id =** código del convenio
					\\"id\\":\\"8313\\",
			//**codigoInterno** = este código debe validarse a diario, si código no corresponde a una consulta previa debe actualizarse todo el producto convenio
					\\"codigoInterno\\":null,
			//**nombre =** nombre del convenio
					\\"nombre\\":\\"CLARO - COMCEL\\",
			//**campoReferencia =** ayuda de referencia para el usuario, se puede mostrar como guía
					\\"campoReferencia\\":\\"CUENTA O REF. DE PAGO.\\",
			//**ciudad =** ciudad perteneciente al recuado
					\\"ciudad\\":\\"\\",
			//**pagoParcial** = indica si el cliente que esta pagando puede modificar el valor a pagar devuelto por la consulta de referencia
					\\"pagoParcial\\":\\"0\\",
			//**soloAutomatico** = indica si se debe pagar únicamente leyendo el código de barras, o si se puede usar búsqueda de convenio manual y consulta de referencia manual (1) true (0) false
					\\"soloAutomatico\\":\\"0\\",
					\\"tipo\\":\\"0\\"
				},
				\\"8517\\":{
					\\"id\\":\\"8517\\",
					\\"codigoInterno\\":null,
					\\"nombre\\":\\"CLARO - COMCEL TIEMPO AL AIRE\\",
					\\"campoReferencia\\":\\"DIGITE REFERENCIA\\",
					\\"ciudad\\":\\"\\",
					\\"pagoParcial\\":\\"0\\",
					\\"soloAutomatico\\":\\"0\\",
					\\"tipo\\":\\"0\\"
				}
			}"
		} 
	)

//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		****// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => )
		)
		// Error en **timeout**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Por favor digite una palabra clave para busqueda"
			} 
		)
```



**Prueba**

preConsulta **consultaValorConvRef**&#x20;

```php
$data = json_encode(array( 
		//idConv -> xxxx string id del convenio a recaudar
    "idConv"=>"134467",
		//extConvenio -> xxxx string numero de referencia solicitada por convenio de pago
    "extConvenio"=>"1234"
  ));
  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'consultaValorConvRef',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			//**reply** = respuesta validación documento, ok si se encuentra documento, error si no se encuentra			
			"reply":"ok",
			//**estado** = estado consulta documento (00)correcto (05)error
			"estado":"00",
			//**referencia** = Referencia a pagar
			"referencia":"1234",
			//**nconvenio** = Nombre del convenio a pagar
			"nconvenio":"MOVISTAR SERVICIOS MOVILES",
			//**valorPago** = valor de la factura a cancelar, se puede modificar si pago parcial lo permite
			"valorPago":"103990",
			//**idPre** = Numero de transacción, debe enviarse al momento de realizar el pago
			"idPre":"99694264",
			//**pagoParcial** = Si es 1, se podrá pagar un valor parcial del valor total de pago, si es 0, deberá pagarse el total indicado en valorMonto
			"pagoParcial":1
		} 
	)
//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		****// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Error en datos Codigo Interno 2"
			} 
		)
		//Error
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"estado":"05",
				"nombre":"Advertencia consultando factura en ATH: P1 null"
			} 
		)
		// Covenio no validoválido 
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Convenio no valido"
			} 
		)
```



**Prueba**

preConsulta **verifyBillEan**&#x20;

```php
$data = json_encode(array( 
    "eanbill"=>"41577071769601788020060055370041390000000000103990962020063060055370041",
  ));
  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'verifyBillEan',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			//**reply** = respuesta validación documento, ok si se encuentra documento, error si no se encuentra			
			"reply":"ok",
			//**estado** = estado consulta documento (00)correcto (05)error
			"estado":"00",
			//**referencia** = Referencia a pagar
			"referencia":"1234",
			//**nconvenio** = Nombre del convenio a pagar
			"nconvenio":"MOVISTAR SERVICIOS MOVILES",
			//**valorPago** = valor de la factura a cancelar, se puede modificar si pago parcial lo permite
			"valorPago":"103990",
			//**idPre** = Numero de transacción, debe enviarse al momento de realizar el pago
			"idPre":"99694264",
			//**pagoParcial** = Si es 1, se podrá pagar un valor parcial del valor total de pago, si es 0, deberá pagarse el total indicado en valorMonto
			"pagoParcial":1
		} 
	)
//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		****// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Error en datos Codigo Interno 2"
			} 
		)
		// Covenio no validoválido 
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Convenio no valido"
			} 
		)
```



**Prueba**&#x20;

pracRec **fc**&#x20;

```php
$data = json_encode(array(
    //idPre = id recibido previamente en consulta de factura
    "idPre" =>"99694264",
  ));
  $params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3214598028',
    'operador' => 'fc',
    'valor' => '103990',
    'jsonAdicional' => $data,
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params
```

```php
//**Response exitoso**
Array ( 
	[idtrans] => 123456 
	[estado] => 00 
	[respuesta] => Transaccion exitosa 
	[codigoauth] => 123456 
	[saldo] => 1000 
	[codop] => 123456 
	[jsonAdicional] => {
		// **Codigo Convenio** -> Codigo del convenio entregado por el banco
		"Codigo Convenio":"18931",
		// **Nombre Convenio** -> Nombre del convenio que se pago
		"Nombre Convenio":"MERCADOPAGO",
		// **Referencia** -> Referencia del convenio que se pago
		"Referencia":"1234"
	} 
)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
		//error en **idPre**
		Array ( 
			[idtrans] => 
			[estado] => 
			[respuesta] => :Datos erroneos para esta venta 
			[codigoauth] => 
			[saldo] => 
			[codop] => 0 
		)
		// Error en operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Pin EPM</summary>

**Prueba**

preConsulta **validarPinEpm**&#x20;

```php
$data = json_encode(array( 
    // **numeroContador** -> Numero contador de energía
    "numeroContador"=>"01111111111",
    // **cantidadVenta**-> Valor de venta del pin de energía a expedir
    "cantidadVenta"=>"5000",
  ));

  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'validarPinEpm',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**
	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			//**estado**-> estado consulta documento (00)correcto (05)error
			"estado":"00",
			//**reply**-> respuesta validación documento, ok si se encuentra documento, error si no se encuentra
			"reply":"ok",
			//**respuesta**-> respuesta textual validación consulta factuar manual
			"respuesta":"",
			//**nombreClienteEpm** -> Nombre Cliente
			"nombreClienteEpm":"JUAN PEREZ",
			//**dniClienteEpm** ->Documento cliente
			"dniClienteEpm":"123456",
			//**direccionClienteEpm** -> Dirección Contador
			"direccionClienteEpm":"CRA 10 6 90",
			//**localidadEpm** -> Localidad ubicación Contador
			"localidadEpm":"MEDELLIN",
			//**departamentoEpm** -> Departamento Ubicación Contador
			"departamentoEpm":"ANTIOQUIA"
		} 
	)
//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		****// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => )
		)
		// Error en **numeroContador**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"ok",
				"estado":"05",
				"nombre":"Usuario no existe"
			} 
		)
	
```



**Prueba**

pracRec **em**&#x20;

<mark style="background-color:red;">**Nota:**</mark> En el campo `‘celular’` debe enviarse el campo `‘numeroContador’` de la preconsulta anterior y en el campo `‘valor’` el campo `‘cantidadVenta’`, ver ejemplo.&#x20;

Se debe enviar en el campo jsonAdicional ( **telEnvio** ) en caso de querer enviar el sms por parte de GENESIS, en caso de que no, se puede enviar un numero de teléfono genérico ( 3100000000 )

```php
$data = json_encode(array(
    // telEnvio -> Numero telefónico para envio pin, en caso de host se envía en respuesta del host
    "telEnvio" =>"3100000000",
  ));
  $params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '**01111111111**',
    'operador' => 'em',
    'valor' => '**5000**',
    'jsonAdicional' => $data,
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
	Array ( 
		[idtrans] => 123456 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			//**Numero Pin** -> Numero del pin solicitado
			"Numero Pin":"6754 4382 1411 6542 0312",
			//**Cupon** -> Cupón informativo
			"Cupon":"",
			//**Cantidad Kwh** -> Cantidad kwh entregados por el pin
			"Cantidad Kwh":"35.006",
			//**Estrato** -> Estrato Ubicación Contador
			"Estrato":"2",
			//**Subsidio** -> Subsidio Entregado
			"Subsidio":"9860.9",
			//**Contrubucion** -> Info Contribución
			"Contribucion":"0.0",
			//**Otros Cobros** -> Otros cobros información
			"Otros Cobros":"0.0",
			//**Valor Aplicar Deuda** -> Valor Aplicar a deuda Información
			"Valor Aplicar Deuda":"0",
			//**Saldo Pendiente** -> Saldo Pendiente información
			"Saldo Pendiente":"83892",
			//**Valor Tasa Aseo** -> Valor tasa aseo información
			"Valor Tasa Aseo":"0.0",
			//**Saldo Tasa Aseo** -> Saldo tasa aseo información
			"Saldo Tasa Aseo":"0.0"
		} 
	)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
		//error en **telEnvio**
		Array ( 
			[idtrans] => 
			[estado] => 
			[respuesta] => :Datos erroneos para esta venta 
			[codigoauth] => 
			[saldo] => 
			[codop] => 0 
		)
		// Error en operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Pin ESSA</summary>

**Prueba**

preConsulta **validarPinEssa**&#x20;

```php
$data = json_encode(array( 
    // numeroContador -> Numero contador de energía
    "numeroContador"=>"01111111111",
    // cantidadVenta-> Valor de venta del pin de energía a expedir
    "cantidadVenta"=>"5000",
    //tipoAlumbrado -> posibles valores "AP" con alumbrado publico "SP" Sin alumbrado publico
    "tipoAlumbrado"=>"AP",
  ));

  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'validarPinEssa',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
  
```

```php
//**Response exitoso**

	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			"estado":"00",
			"reply":"ok",
			"respuesta":"",
			"nombreClienteEssa":"JUAN PEREZ",
			"dniClienteEssa":"123456",
			"direccionClienteEssa":"CRA 10 6 90",
			"localidadEssa":"MEDELLIN",
			"departamentoEssa":"ANTIOQUIA"
		}
	)
	
//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => )
		)
		//
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => {
				"reply":"Cantidad no puede ser menor de 5000"
			}
				
		)

```



pracRec **es**

<mark style="background-color:red;">**Nota:**</mark> En el campo `‘celular’` debe enviarse el campo `‘numeroContador’` de la preconsulta anterior y en el campo `‘valor’` el campo `‘cantidadVenta’`, ver ejemplo.&#x20;

Se debe enviar en el campo jsonAdicional ( **telEnvio** ) en caso de querer enviar el sms por parte de GENESIS, en caso de que no, se puede enviar un numero de teléfono genérico ( 3100000000 )

```php
$data = json_encode(array(
    // telEnvio -> Numero telefónico para envio pin, en caso de host se envía en respuesta del host
    "telEnvio" =>"3100000000",
    "tipoAlumbrado" =>"AP",
  ));
  $params = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'idtrans' => 1,
    'celular' => '01111111111',
    'operador' => 'es',
    'valor' => '6000',
    'jsonAdicional' => $data,
  );
  
  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**

	Array ( 
		[idtrans] => 1 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			"Numero Pin":"6580 2091 1576 7134 6777",
			"Nombre Cliente":"YEIMI PATRICIA JAIMES MENDOZA",
			"Barrio":"408-URB VILLA DE DIOS",
			"Municipio":"20011-AGUACHICA",
			"Departamento":"20-CESAR",
			"Estrato":"1",
			"Valor Pagado":"10550",
			"Deuda Aseo":"5920",
			"Deuda Energia":"93000"
		} 
	)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
		// Error en operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Pin CENS</summary>



**Prueba**

preConsulta **validarPinEssa**&#x20;

```php
  $data = json_encode(array( 
    // numeroContador -> Numero contador de energía
    "numeroContador"=>"01111111111",
    // cantidadVenta-> Valor de venta del pin de energía a expedir
    "cantidadVenta"=>"5000",
    // cantidadAseo -> valor de aseo que se va a pagar con el pin
    "cantidadAseo"=>"2000",
    // cantidadEnergia -> valor de energia que se va a pagar con el pin
    "cantidadEnergia"=>"2000",
  ));

  $param = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'tipoConsulta' => 'validarPinCens',
    'data' => $data,
    'idTrx' => 1,
  );

  $client = clientSoap();
  $response = $client->call('preConsulta', $param);
```

```php
//**Response exitoso**

	Array ( 
		[estado] => 00 
		[respuesta] => Consulta Correcta 
		[data] => {
			"estado":"00",
			"reply":"ok",
			"respuesta":"",
			"nombreClienteCens":"JUAN PEREZ",
			"direccionClienteCens":"CRA 10 6 90"
		} 
	)
	
//**Response Erroneos**
		// Error en idcomercio o claveventa
		Array ( 
			[estado] => 05 
			[respuesta] => IP NO AUTORIZADA 
			[data] => 
		)
		// Error en **data**
		Array ( 
			[estado] => 05 
			[respuesta] => Consulta con error 
			[data] => )
		)
```



pracRec **cs**

<mark style="background-color:red;">**Nota:**</mark> En el campo `‘celular’` debe enviarse el campo `‘numeroContador’` de la preconsulta anterior y en el campo `‘valor’` el campo `‘cantidadVenta’`, ver ejemplo.&#x20;

Se debe enviar en el campo jsonAdicional ( **telEnvio** ) en caso de querer enviar el sms por parte de GENESIS, en caso de que no, se puede enviar un numero de teléfono genérico ( 3100000000 )

```php
$data = json_encode(array(
    // telEnvio -> Numero telefónico para envio pin, en caso de host se envía en respuesta del host
    "telEnvio" =>"3100000000",
    "cantidadAseo" =>"3000",
    "cantidadEnergia" =>"3000",
  ));
  $params = array(
    'idcomercio' => 1425,
    'claveventa' => 1234,
    'idtrans' => 1,
    'celular' => '01111111111',
    'operador' => 'cs',
    'valor' => '6000',
    'jsonAdicional' => $data,
  );
  
  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**

	Array ( 
		[idtrans] => 1 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			"Numero Pin":"3110 7694 7742 9591 5417",
			"Cupon":"0",
			"Cantidad Kwh":"11.35",
			"Estrato":"1",
			"Plan":"SP",
			"Subsidio":"-5398.0"
		} 
	)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
		// Error en operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Pines multimedia</summary>

Se envía en el campo `valor`, el id del pin a vender, si desea se puede enviar un numero genérico(3100000000) en el campo teléfono, ya que GENESIS no enviara el sms



**Prueba**

pracRec **Código Operador:** Al final de la pagina [](<> "mention") &#x20;

```php
$params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3100000000',
    'operador' => 'nx',
    'valor' => '177',
    'jsonAdicional' => '',
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
	Array ( 
		[idtrans] => 123456 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			//**Pin Cliente** -> Pin a enviar al cliente
			"info":"aa-bb-cc-dd"
		} 
	)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
	
		// Error en operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```



</details>

<details>

<summary>Wallets</summary>

Para **cashout** se debe enviar en `valor` un 0, ya que al ser cashout no se asigna valor de venta.



**Prueba**

pracRec **Código Operador:** Al final de la pagina [](<> "mention") &#x20;

```php
$data = json_encode(array(
    // **tokenr** -> Token generado por la aplicación para realizar el retiro
    "tokenr" =>"123456",
    // **valorr** -> Valor del retiro
    "valorr" =>"10000",
  ));

  $params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3214598028',
    'operador' => 'nr',
    'valor' => '0',
    'jsonAdicional' => $data,
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
	Array ( 
		[idtrans] => 123456 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => 
	)

//**Response Erroneos**
		// Error
		Array ( 
			[idtrans] => 123456 
			[estado] => 
			[respuesta] => error 
			[codigoauth] => 
			[saldo] => 
			[codop] => 001 
		)
		// error **data**
		Array ( 
			[idtrans] => 
			[estado] => 
			[respuesta] => :Datos erroneos para esta venta 
			[codigoauth] => 
			[saldo] => 
			[codop] => 0 
		)
		// Error **tokenr**
		Array ( 
			[idtrans] => 
			[estado] => 
			[respuesta] => :token no encontrado 
			[codigoauth] => 
			[saldo] => 
			[codop] => 0 
		)
		// Error en operador
		Array ( 
			[idtrans] => 123456 
			[estado] => 05 
			[respuesta] => Transaccion no exitosa 
			[codigoauth] => 
			[saldo] => 1000 
			[codop] => 001 
		)
```

</details>

<details>

<summary>Retiros Bancos ATH</summary>

Para **retiros** se debe enviar en `valor` un 0, ya que al ser retiro no se asigna valor de venta.

**Prueba**

pracRec&#x20;

**Código Operador: br**

**athBanco:** 1 <mark style="background-color:blue;">**Banco de Bogotá**</mark>, 2 <mark style="background-color:blue;">**Banco popular**</mark>, 3 <mark style="background-color:blue;">**Occidente**</mark>, 4 <mark style="background-color:blue;">**Av villas**</mark>

```php
$data = json_encode(array(
    // posibles valores, 1 banco de bogota 2 banco popular 3 occidente 4 av villas
    "athBanco" =>"4",
    // 1 ahorros - 2 corriente
    "athCuenta" =>"1",
    // identificacion titular
    "athIdentificacion" => "12345",
    // celular titular
    "athCelular" => "3214598028",
    // numero de cuenta ( en este caso que es retiro va la misma cedula del propietario)
    "athNuCuenta" => "12345",
    // valor del retiro
    "valorr" => 10000,
    // token retiro
    "tokenr" => "123456"
  ));

  $params = array(
    'idcomercio' => 01234,
    'claveventa' => 1234,
    'idtrans' => 123456,
    'celular' => '3214598028',
    'operador' => 'br',
    'valor' => '0',
    'jsonAdicional' => $data,
  );

  $client = clientSoap();
  $response = $client->call('pracRec', $params);
```

```php
//**Response exitoso**
	Array ( 
		[idtrans] => 2 
		[estado] => 00 
		[respuesta] => Transaccion exitosa 
		[codigoauth] => 123456 
		[saldo] => 1000 
		[codop] => 123456 
		[jsonAdicional] => {
			"Nombre Convenio":"Retiro",
			"Referencia":"****1234",
			"Id Aprobacion":"12345",
			"Codigo Convenio":"RETIRO",
			"No Identificacion":"****1234",
			"Celular":"3214598028",
			"Banco":"BOGOTA",
			"Cuenta":"****1234",
			"Valor Retiro":"10000",
			"LINEA DE ATENCION AL CLIENTE":"BOGOTA:7432626 \/NACIONAL: 018000512825"
		} 
	)
//**Response Erroneos**
	// Error	
	Array ( 
		[idtrans] => 
		[estado] => 
		[respuesta] => :token no encontrado 
		[codigoauth] => 
		[saldo] => 
		[codop] => 0 
	)
```

</details>

<details>

<summary>Depositos Banco Caja Social</summary>

## Pre-Consulta

Para **Depositos** se debe enviar una pre-consulta con:

| Action       | Content                                                                                                               |
| ------------ | --------------------------------------------------------------------------------------------------------------------- |
| Method       | POST                                                                                                                  |
| URL          | [https://recargas.practisistemas.com/api\_test/preConsulta](https://recargas.practisistemas.com/api_test/preConsulta) |
| SOAP Version | 1.1                                                                                                                   |

```
<?php

$jsonAdicional = json_encode(array(
    "valorSd"  => "10000",
    "sdCuenta" => "24083018583",
    "operador" => "sd"
));

$params = array(
    "idcomercio"     => "1425",
    "claveventa"     => "1234",
    "tipoConsulta"   => "consultaSd",
    "idTrx"          => "1",
    "jsonAdicional"  => $jsonAdicional
);

$client = clientSoap();
$response = $client->call('preConsulta', $params);

var_dump($response);
?>

```

**Glosario**&#x20;

| Variable     | Value        | Description                                                                                   |
| ------------ | ------------ | --------------------------------------------------------------------------------------------- |
| operador     | sd           | Social Deposito                                                                               |
| tipoConsulta | consultaSd   | Consulta Social Deposito                                                                      |
| idTrx        | Valor String | Este ID lo proporciona cliente quien esta integrando para trasabilidad entre los dos sistemas |
| valorSd      | Valor String | Valor deposito                                                                                |
| sdCuenta     | Valor String | Cuenta destino                                                                                |

**Response**

</details>

<details>

<summary>Retiros Banco Caja Social</summary>

## Venta

Para **Retiros** se debe enviar una venta  con:

| Action | Content                                                                                                       |
| ------ | ------------------------------------------------------------------------------------------------------------- |
| Method | POST                                                                                                          |
| URL    | [https://recargas.practisistemas.com/api\_test/pracRec](https://recargas.practisistemas.com/api_test/pracRec) |

```
<?php

$jsonAdicional = json_encode(array(
    "valorSd"  => "10000",
    "sdCuenta" => "24083018583",
    "operador" => "sd"
));

$params = array(
    "idcomercio"     => "1425",
    "claveventa"     => "1234",
    "tipoConsulta"   => "consultaSd",
    "idTrx"          => "1",
    "jsonAdicional"  => $jsonAdicional
);

$client = clientSoap();
$response = $client->call('preConsulta', $params);

var_dump($response);
?>

```

**Glosario**

| Variable  | Value          | Description                                                                                   |
| --------- | -------------- | --------------------------------------------------------------------------------------------- |
| operador  | sd             | Social Deposito                                                                               |
| celular   | Valor String   | Numero de documento  de quien va a realizar el retiro                                         |
| valor     | Valor String   | En este caso es **"0"** el valor que va por defecto                                           |
| idtrans   | Valor String   | Este ID lo proporciona cliente quien esta integrando para trasabilidad entre los dos sistemas |
| sdCelular | Valor Interger | Numero celular de quien retira                                                                |
| tokenr    | Valor Interger | Token de retiro Banco o Codigo OTP                                                            |
| valorr    | Valor Interger | Valor retirado por el cliente                                                                 |
|           |                |                                                                                               |

**Response**

```
{
    "idtrans": "1234",
    "estado": "00",
    "respuesta": "Transaccion exitosa",
    "codigoauth": "123456",
    "saldo": "1000",
    "codop": "123456",
    "jsonAdicional": {
        "Banco": "Caja-Social",
        "terminal": "1838",
        "comercio": "15034",
        "ciudad": "chia",
        "direccion": "km 32 cajica chia",
        "Tipo de Cuenta": "AHORROS",
        "Cuenta Deposito": "*******7364",
        "Costo Transaccion": "0",
        "Valor Transaccion": 100000,
        "Codigo Auth": "523792",
        "Url": "https://recargas.practisistemas.com/web/ticket.php?h=409776c526ba2542"
    }
}
```

</details>
