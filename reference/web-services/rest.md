# REST

{% embed url="https://recargas.practisistemas.com/api_test" %}
URL PRUEBAS 🧪
{% endembed %}

## Métodos

## Método para realizar venta de recarga y paquetes

<mark style="color:green;">`POST`</mark> `https://recargas.practisistemas.com/api_test/pracRec`

![](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2Fk5uNxytDwTawLQBqSGn3%2Fimage.png?alt=media\&token=74799857-51a5-4197-a101-60d7d7e23d34)

#### Request Body

| Name                                            | Type                   | Description                                                                                     |
| ----------------------------------------------- | ---------------------- | ----------------------------------------------------------------------------------------------- |
| idcomercio<mark style="color:red;">\*</mark>    | Int                    | Id comercio dada por plataforma                                                                 |
| claveventa<mark style="color:red;">\*</mark>    | Int                    | Clave venta dada por plataforma                                                                 |
| idtrans<mark style="color:red;">\*</mark>       | Bigint(20 caracteres)  | Id transacción del cliente para futuras consultas                                               |
| celular<mark style="color:red;">\*</mark>       | Char                   | Id transacción del cliente para futuras consultas                                               |
| operador<mark style="color:red;">\*</mark>      | Char                   | Código del operador(ver tabla códigos de operador)                                              |
| valor<mark style="color:red;">\*</mark>         | Int                    | Valor de la recarga o código de paquete(aplicable para los distintos tipos de paquetes prepago) |
| jsonAdicional<mark style="color:red;">\*</mark> | Json                   | Json adicional para algunos productos, enviar JSON vacío si es el caso                          |

{% tabs %}
{% tab title="200: OK Ejemplo método pracRec" %}
```json
{
	//Id Transacción de practisistemas(para futuras referencias)
	"idtrans": "1",
	//Estado de la transacción ( 00 - correcta; 05 - incorrecta; 99 - sin respuesta )
	"estado": "00",
	//Descripción textual transacción
	"respuesta": "Transaccion exitosa",
	//Código Autorización(solo si estado 00)
	"codigoauth": "123456",
	//Saldo del cliente después de trx
	"saldo": "1000",
	//Código de error del operador(si aplica
	"codop": "123456",
	//Json adicional para algunos productos
	"jsonAdicional": ""
}
```
{% endtab %}

{% tab title="400: Bad Request Response Erróneos" %}
```javascript
{
	//Id Transacción de practisistemas(para futuras referencias)
	"idtrans": "1",
	//Estado de la transacción ( 00 - correcta; 05 - incorrecta; 99 - sin respuesta )
	"estado": "05",
	//Descripción textual transacción
	"respuesta": "Transaccion no exitosa",
	//Código Autorización(solo si estado 00)
	"codigoauth": "",
	//Saldo del cliente después de trx
	"saldo": "1000",
	//Código de error del operador(si aplica)
	"codop": "001"
}
// parametros incompletos
{
	"error": "parametros incompletos"
}
```
{% endtab %}
{% endtabs %}

## Usar en caso de no recibir respuesta del método de recarga.

<mark style="color:green;">`POST`</mark> `https://recargas.practisistemas.com/api_test/consRec`

![](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2F7Zu4YXw7RZ1enkCUSbCN%2Fimage.png?alt=media\&token=04893972-1cdb-4a2e-9fc3-53bfc3e2fdce)

TimeOut 60 seg&#x20;

#### Request Body

| Name                                          | Type | Description                                                                                                    |
| --------------------------------------------- | ---- | -------------------------------------------------------------------------------------------------------------- |
| idcomercio <mark style="color:red;">\*</mark> | Int  | Id comercio dada por plataforma                                                                                |
| claveventa <mark style="color:red;">\*</mark> | Int  | Clave venta dada por plataforma                                                                                |
| fecha<mark style="color:red;">\*</mark>       | Char | Fecha de la transacción (Y-m-d)                                                                                |
| idtrans <mark style="color:red;">\*</mark>    | Int  | Id interno enviado en la recarga por parte del `clienteId` interno enviado en la recarga por parte del cliente |

{% tabs %}
{% tab title="200: OK Ejemplo método consRec" %}
```json
//Response exitoso
{
    //Id interno enviado en la recarga por parte del cliente
    "idtrans": "1",
    //Estado de la transacción ( 00 - correcta; 05 - incorrecta; 99 - sin respuesta )
    "estado": "00",
    //Descripción textual transacción
    "respuesta": "Transaccion exitosa",
    //Descripción textual transacción
    "codigoauth": "",
    //Saldo del cliente actual
    "saldo": "1000",
    //Código de error del operador( si aplica )
    "codop": "001"
}
```
{% endtab %}

{% tab title="400: Bad Request Response Erróneos" %}
```javascript
{
    "error": "parametros incompletos"
}
```
{% endtab %}
{% endtabs %}

## Método usado por parte del cliente para hacer preConsulta para algunos productos.

<mark style="color:green;">`POST`</mark> `https://recargas.practisistemas.com/api_test/preConsulta`

![](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2Fsj11arsJfEHUD5Y9qXpO%2Fimage.png?alt=media\&token=607a61f3-4b43-4619-84f7-6e2ade17c6c2)

<mark style="background-color:red;">**Nota:**</mark> En `data` para consultar productos:

Tipo: 1( Recargas )

Tipo: 2( Pines )

Tipo: 0( Multiproducto )

**Ejemplo:** `"data":{ "tipo":"1" }`

#### Request Body

| Name                                           | Type   | Description                                                               |
| ---------------------------------------------- | ------ | ------------------------------------------------------------------------- |
| idcomercio<mark style="color:red;">\*</mark>   | Int    | Id comercio dada por plataforma                                           |
| claveventa<mark style="color:red;">\*</mark>   | Int    | Clave venta dada por plataforma                                           |
| tipoConsulta<mark style="color:red;">\*</mark> | String | El tipo de consulta disponible(ver tablas descripción tipos pre consulta) |
| data<mark style="color:red;">\*</mark>         | Json   | El tipo de consulta disponible(ver tablas descripción tipos pre consulta) |
| idTrx<mark style="color:red;">\*</mark>        | Int    | Id transacción cliente                                                    |

{% tabs %}
{% tab title="200: OK Ejemplo método preConsulta" %}
```json
{
	//Estado de la consulta ( 00 - correcta; 05 - error de datos; )  
	"estado": "00",
	//Respuesta textual consulta 
	"respuesta": "Consulta Correcta",
	//Data consulta respuesta
	"data": {
		//reply = Respuesta validación documento, 
		//ok si se encuentra productos, error si no se encuentran.
		"reply": "ok",
		//estado = Estado consulta productos (00)correcto (05)error.
	    	"estado": "00",
		//productos = Array con los productos de plataforma
	    	"productos": "{
				//producto = código del producto
				\"cl\":{
					//op = Código del producto.
					\"op\":\"cl\",
					//isPack = Es paquete(1), no es paquete(0)
					\"isPack\":\"0\",
					//desc = Descripcion del producto, nombre del operador.
					\"desc\":\"Claro\",
					//packs = Códigos de producto describiendo los paquetes que pertenecen al operador, 
					// si llega en blanco, no tiene paquetes.
					\"packs\":\"pc\"
				},
				\"mo\":{
					\"op\":\"mo\",
					\"isPack\":\"0\",
					\"desc\":\"Movistar\",
					\"packs\":\"pm\"
				},
				\"ti\":{
					\"op\":\"ti\",
					\"isPack\":\"0\",
					\"desc\":\"Tigo\",
					\"packs\":\"pi,pv,pb\"
				},
				\"uf\":{
					\"op\":\"uf\",
					\"isPack\":\"0\",
					\"desc\":\"Uff\",
					\"packs\":null
				}, 
				...
			}"
	  }
}
```
{% endtab %}

{% tab title="400: Bad Request Response Erróneos" %}
```json
//  Error en campo data 
{
    "estado": "05",
    "respuesta": "Consulta con error",
    "data": null
}

// parametros incompletos
{
	  "error": "parametros incompletos"
}
```
{% endtab %}
{% endtabs %}

## Consulta de productos de paquetes, pines, recargas, etc.

<mark style="color:green;">`POST`</mark> `https://recargas.practisistemas.com/api_test/consultaProducto`

![](https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2F597XDIGUesCkcMWSWYvh%2Fimage.png?alt=media\&token=5261b459-14a0-4330-b6f9-4b32e0f9d5bf)

Se obtendrá en tiempo real id, descripción y valor de cada uno.

#### Request Body

| Name                                         | Type   | Description                     |
| -------------------------------------------- | ------ | ------------------------------- |
| idcomercio<mark style="color:red;">\*</mark> | Int    | Id comercio dada por plataforma |
| claveventa<mark style="color:red;">\*</mark> | Int    | Producto a consultar            |
| producto<mark style="color:red;">\*</mark>   | String | Producto a consultar            |

{% tabs %}
{% tab title="200: OK Ejemplo método consultaProducto" %}
```javascript
{
	//estado = Estado consulta producto (00)correcto (05)error.
	"estado":"00",
	"data":"[
		{
			//Codigo del paquete
			"68":{
				//categoryId = Código del producto
				"categoryId":"pc",
				//internalCod = Código del operador
				"internalCod":"50012",
				//practiCode = Código que se debe enviar hacia la plataforma génesis para consumir el paquete o producto
				"practiCode":"68",
				//productDesc = Descripción del producto
				"productDesc":"60 minutos 2 dias",
				//cost = Costo del producto al mayorista
				"cost":"3000",
				//sell = Precio de venta al cliente final
				"sell":"3000",
				//validity = (decimal) vigencia del producto en días (si aplica)
				"validity":"2.0000"
			}
		},
		{
			"69":{
				"categoryId":"pc",
				"internalCod":"50140",
				"practiCode":"69",
				"productDesc":"50 minutos 1 dia",
				"cost":"1000",
				"sell":"1000",
				"validity":"1.0000"
			}
		},
		...
	]
}
```
{% endtab %}

{% tab title="400: Bad Request Response Erróneos" %}
```javascript
//  Error 
null
	
// parametros incompletos
{
	"error": "parametros incompletos"
}
```
{% endtab %}
{% endtabs %}

## Método usado por parte del cliente para consultar el saldo disponible en el comercio

<mark style="color:green;">`POST`</mark> `https://recargas.practisistemas.com/api_test/cSaldo`

<img src="https://253856789-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FoFEwwoWOJEGu86xMqQtQ%2Fuploads%2FhIXVLD3dOaC65pjHnWbP%2Fimage.png?alt=media&#x26;token=932f5dcc-b4a5-47ed-802c-453900f75a4f" alt="" data-size="original">

#### Request Body

| Name                                          | Type | Description                     |
| --------------------------------------------- | ---- | ------------------------------- |
| idcomercio <mark style="color:red;">\*</mark> | Int  | Id comercio dada por plataforma |
| claveventa <mark style="color:red;">\*</mark> | Int  | Clave venta dada por plataforma |

{% tabs %}
{% tab title="400: Bad Request Response Erróneos" %}
```javascript
{
    "error": "parametros incompletos"
}
```
{% endtab %}

{% tab title="200: OK " %}
```javascript
{
	//Estado de la consulta ( 00 - correcta; 05 - error de datos; )
	"estado": "00",
	//Saldo del cliente
	"saldo": "55000"
}
```
{% endtab %}
{% endtabs %}



<details>

<summary>Paquetes</summary>

<mark style="background-color:red;">**Nota:**</mark> Para poder vender este producto se debe agregar en `'valor'` el código del paquete



**Prueba**

```json
//Ejemplo metodo pracRec
{
	"idcomercio":1425,
	"claveventa":1234,
	"idtrans":1,
	"celular":"3214598028",
	"operador":"pc",
	"valor":61,
	"jsonAdicional":""
}
```

```php
//Response exitoso
	{
	  "idtrans": "1",
	  "estado": "00",
	  "respuesta": "Transaccion exitosa",
	  "codigoauth": "123456",
	  "saldo": "1000",
	  "codop": "123456",
	  "jsonAdicional": null
	}
	
//Response Erroneos
	//  error idcomercio / claveventa
	{
	    "idtrans": "1",
	    "estado": "00",
	    "respuesta": "Transaccion exitosa",
	    "codigoauth": "123456",
	    "saldo": "1000",
	    "codop": "123456",
	    "jsonAdicional": null
	}
	// Número no corresponde al operador
	{
	    "idtrans": "1",
	    "estado": "05",
	    "respuesta": "Transaccion no exitosa",
	    "codigoauth": "",
	    "saldo": "1000",
	    "codop": "001"
	}
	// parametros incompletos
	{
	    "error": "parametros incompletos"
	}
```

</details>

<details>

<summary>Certificado de tradición y libertad</summary>

<mark style="background-color:red;">**Nota:**</mark> Para poder vender este producto debe realizar **Preconsulta** antes.



**Prueba**

Preconsulta de **certTyl**

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "tipoConsulta":"certTyl",
    "data":{
	//Circulo = XXX String conteniendo circulo matricula a consultar
        "circulo":"002",
	//Matricula = XXX String conteniendo numero matricula a consultar
        "matricula":"1"
    },
    "idTrx":"1"
}
```

```php
//Response exitoso
	{
    "estado": "00",
    "respuesta": "Consulta Correcta",
    "data": {
        "estado": "00",
        "reply": "ok",
        "respuesta": "CALLE SANTA BARBARA (48)  54-46",
				//dir = XXX String conteniendo direccion
        "dir": "CALLE SANTA BARBARA (48)  54-46",
				//municipio = XXX String conteniendo nombre municipio
        "municipio": "ABEJORRAL",
        "valorVenta": "20500"
    }
	}
//Response Erroneos
	//  Error en campo data 
	{
	    "error": "parametros incompletos"
	}
	// Error en campo data 
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": {
	        "estado": "05",
	        "respuesta": "Error consultando Matricula, Posiblemente no existe",
	        "reply": "Error consultando Matricula, Posiblemente no existe"
	    }
	}
	// Error en campo tipoConsulta
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": null
	}
```



**Prueba**

pracRec **cb**&#x20;

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"3214598028",
    "operador":"cb",
    "valor":"103990",
    "jsonAdicional":{
				//circulo = XXX String conteniendo circulo matricula a consultar	
        "circulo":"002",
				//Matricula = XXX String conteniendo numero matricula a consultar
        "matricula":"1"
    },
    "idtrans":"1"
}
```

```php
//Response exitoso
	{
    "idtrans": "1",
    "estado": "00",
    "respuesta": "Transaccion exitosa",
    "codigoauth": "123456",
    "saldo": "1000",
    "codop": "123456",
    "jsonAdicional": {
        "Circulo": "002",
        "Matricula": "1",
        "Url": "http://dw.praw.biz/web/dw.php?h=dd42e571d74ca4d8"
    }
	}
//Response Erroneos
		//  Error en campo data 
		{
	    "idtrans": "",
	    "estado": "",
	    "respuesta": ":Datos erroneos para esta venta",
	    "codigoauth": "",
	    "saldo": "",
	    "codop": "0"
		}
		
		// Error body
		{
	    "error": "parametros incompletos"
		}
```

</details>

<details>

<summary>Certificado de tradición vehicular RUNT</summary>



**Prueba**

pracRec **hv**

```php

{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"3214598028",
    "operador":"hv",
    "valor":"1000",
    "jsonAdicional":{
	// placaVh = XXXXXX String conteniendo placa de vehiculo a expedir certificado e histórico runt
        "placaVh":"AAA113"
    },
    "idtrans":"1"
}
  
```

```php

//Response exitoso
	{
	  "idtrans": "1",
	  "estado": "00",
	  "respuesta": "Transaccion exitosa",
	  "codigoauth": "123456",
	  "saldo": "1000",
	  "codop": "123456",
	  "jsonAdicional": {
		// Url = XXX String conteniendo histórico vehicular
	      "Url": "http://dw.praw.biz/web/dw.php?h=0185e5719305e322",
		// Url2 = XXX String conteniendo histórico propietarios vehiculo
	      "Url2": "http://dw.praw.biz/web/dw.php?h=8f784feb563830a5"
	  }
	}

//Response Erroneos
	//  Error en campo jsonAdicional
	{
	    "idtrans": "1",
	    "estado": "05",
	    "respuesta": "Transaccion no exitosa",
	    "codigoauth": "",
	    "saldo": "1000",
	    "codop": "001"
	}
	// Error body
	{
	    "error": "Parametros incompletos"
	}
		
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
{
  "idcomercio":"1425",
  "claveventa":"1234",
  "tipoConsulta":"chkPlacaSoat",
  "data":{
		//placa = XXXXXX String conteniendo matricula vehiculo a consultar
    "placa":"JGV151",
		//docSoat = XXXXXX String conteniendo documento del propietario
		"docSoat":"6763449",
		//chkTipoDoc = tipo documento del propietario
		"chkTipoDoc":"1"
  },
  "idTrx":"1"
});
```

<mark style="background-color:red;">**Nota:**</mark> **tx\_id** deberá usarse para método **validación tarifa** y **método venta**

```php
//Response exitoso
{
    "estado": "00",
    "respuesta": "Consulta Correcta",
    "data": {
	// marca = XXX String conteniendo marca vehículo
        "marca": "KIA",
	// linea = XXX String conteniendo línea vehículo
        "linea": "CERATO PRO SX",
	// modelo = XXX String conteniendo modelo vehículo
        "modelo": "2017",
	// cc = XXX String conteniendo cilindraje vehículo
        "cc": "1591",
	// carroceria = XXX String conteniendo tipo carroceria vehículo
        "tipo_carroceria": "SEDAN",
	// no_motor = XXX String conteniendo numero motor vehículo
        "no_motor": "AAAAA",
	// no_chasis = XXX String conteniendo numero chasis vehículo
        "no_chasis": "BBBBB",
	// no_vin = XXX String conteniendo numero vin vehículo
        "no_vin": "CCCCCCC",
	// cnt_toneladas = XXX String conteniendo cantidad toneladas vehículo
        "cnt_toneladas": "0.00",
	// cnt_ocupantes = XXX String conteniendo cantidad ocupantes vehículo
        "cnt_ocupantes": "5",
	// inicio_vigencia = xxxx String conteniendo inicio vigencia soat nuevo, si retorna vacio, se obtendrá al validar costo soat
        "inicio_vigencia": "",
	// fin_vigencia = xxxx string conteniendo fin vigencia soat nuevo, si retorna vacio se obtendrá valor al validar costo soat
        "fin_vigencia": "",
	// valor = xxx string valor soat, si retorna vacío se obtendrá al validar costo soat
        "valor": "",
	// estado = estado consulta soat, si 00 correcto, si no código de error
        "estado": "00",
	// reply = estado proceso consulta, ok para correcto otro para error// reply = estado proceso consulta, ok para correcto otro para error
        "reply": "ok",
	// respuesta = descripción textual estado consulta
        "respuesta": "Consulta Correcta",
	// clase_soat = clase sugerida para la venta, corresponde a una de las disponibles en la lista clase_soat_list, puede usar otra en caso de ser necesario
        "clase_soat": "5",
	// tx_id = id de consulta en sistema génesis, deberá usarse para método validación tarifa y método venta
        "tx_id": 89174,
	// clase_soat_list = clases de soat disponibles para venta soat este vehiculo, separadas por “,”
        "clase_soat_list": "5,7"
    }
}
//Response Erroneos
	// Error Placa
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": {
	        "estado": "05",
	        "respuesta": "Error consultando Informacion, Quiza placa no existe",
	        "reply": "Error consultando Informacion, Quiza placa no existe",
	        "clase_soat_list": "",
	        "clase_soat": ""
	    }
	}

	//  Error en campo data 
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": null
	}
	// Error body
	{
	    "error": "parametros incompletos"
	}
		
```



**Prueba**

preConsulta **valPlacaSoat**&#x20;

```php
{
  "idcomercio":"1425",
  "claveventa":"1234",
  "tipoConsulta":"valPlacaSoat",
  "data":{
    //placa -> XXXXXX String conteniendo matricula vehículo a validar tarifa
    "placa":"JGV151",
    //tx_id-> xxxx String conteniendo tx_id devuelto en consultar placa soat
    "tx_id":"89174",
    //claseSoat -> xxx Clase soat a validar, debe ser una de las devueltas en el campo clase_soat_list devuelto en el método CONSULTA PLACA SOAT
    "claseSoat":"7"
  },
  "idTrx":"1"
}
```

```php
//Response exitoso
	{
	    "estado": "00",
	    "respuesta": "Consulta Correcta",
	    "data": {
					//valor-> valor venta final soat para la clase seleccionada
	        "valor": "489550",
					//inicio_vigencia->Inicio de vigencia del soat a vender
	        "inicio_vigencia": "06/05/2020",
					//fin_vigencia-> fin vigencia del soat a vender
	        "fin_vigencia": "05/05/2021",
					//estado-> estado consulta validación soat (00)correcto (05)error
	        "estado": "00",
					//reply-> respuesta validación soat, ok correcto otro para error
	        "reply": "ok",
					//respuesta-> respuesta textual validación valor soat
	        "respuesta": "Consulta Correcta"
	    }
	}	

//Response Erroneos
		// Error en body
		{
		    "error": "parametros incompletos"
		}
		// Error en data
		{
		    "estado": "05",
		    "respuesta": "Consulta con error",
		    "data": {
		        "estado": "05",
		        "respuesta": "Error validando tarifa",
		        "reply": "Error validando tarifa"
		    }
		}
		//Error 
		{
		    "estado": "05",
		    "respuesta": "Consulta con error",
		    "data": {
		        "reply": "Error en la placa vehiculo"
		    }
		}
		//Error
		{
		    "estado": "05",
		    "respuesta": "Consulta con error",
		    "data": {
		        "reply": "Error (nodata)"
		    }
		}
```



**Prueba**

preConsulta **chkDataDoc&#x20;**<mark style="background-color:red;">**(OPCIONAL)**</mark>&#x20;



<mark style="background-color:red;">**Nota:**</mark> Para el campo **chkTipoDoc** :

* 1 (cédula)
* 3 (NIT)

```php
{
  "idcomercio":"1425",
  "claveventa":"1265434",
  "tipoConsulta":"chkDataDoc",
  "data":{
		// nDocumento = xxxx String Numero Documento
    "nDocumento":"12345678",
		// tipoDocumento = xxxx int tipo Documento
    "tipoDocumento":"1"
  },
  "idTrx":"1"
}
```

```php
//Response exitoso
	{
	    "estado": "00",
	    "respuesta": "Consulta Correcta",
	    "data": {
					// reply = respuesta validación documento, ok si se encuentra documento, error si no se encuentra
	        "reply": "ok",
					// estado = estado consulta documento (00)correcto (05)error
	        "estado": "00",
					// nombre = Nombre encontrado cliente
	        "nombre": "ASTEBIA",
					// apellido = Apellido encontrado cliente
	        "apellido": "RAMOS",
					// sexo = Sexo del cliente
	        "sexo": "F",
	        "ciudadtxt": "CARTAGENA,BOLIVAR",
					// dptoNum = Departamento cliente
	        "dptoNum": "13",
					// ciuNum = Ciudad cliente
	        "ciuNum": "13001"
	    }
	}
	

//Response Erroneos
	// Error en body
	{
	    "error": "parametros incompletos"
	}
	// Error en data
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": null
	}
		
```



**Prueba**

<mark style="background-color:red;">**Nota:**</mark> Para la venta exitosa de SOAT, hay que tener en cuenta que la cédula con la que se consulto la placa ( <mark style="color:red;">`preConsulta chkPlacaSoat`</mark> ), debe ser la misma con la que se realice la venta ( <mark style="color:red;">`nDocumentoSoat`</mark> ).&#x20;

pracRec **so**&#x20;

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"3214598028",
    "operador":"so",
    "valor":"489550",
    "jsonAdicional":{
	// nombreSoat =  Nombre del cliente final
        "nombreSoat":"ASTEBIA",
	// apellidoSoat =  Apellido del cliente final
        "apellidoSoat":"RAMOS",
	// sexoSoat:  =  Sexo del cliente final, M para masculino, F para femenino
        "sexoSoat":"F",
	// documentoSoat = tipo de documento, permitido 1, 2 o 3
        "documentoSoat":"1",
	// nDocumentoSoat =  Numero de documento del cliente final
        "nDocumentoSoat":"12345678",
	// ciudadSoat =  Codigo de Ciudad Soat (documento adicional mundial)
        "ciudadSoat":"13001",
	// departamentoSoat = Código de Departamento Soat (documento adicional mundial)
        "departamentoSoat":"13",
	// direccionSoat =  dirección del cliente final
        "direccionSoat":"",
	// emailSoat = correo del cliente final(aquí se envía info de mundial)
        "emailSoat":"",
	// modeloCarro =  modelo del carro string obtenido en anterior consulta
        "modeloCarro":"2017",
	// placaSoat =  placa del soat enviado en anteriores consultas
        "placaSoat":"JGV151",
	// valorSoat = valor final de venta de soat
        "valorSoat":"489550",
	// Soattx_id = id transacción obtenido de la primera consulta
        "Soattx_id":"89174",
	// tipoSoat =  clase de soat elegido para venta final
        "tipoSoat":"5"
    },
    "idtrans":"1"
}
```

```php
//Response exitoso
{
    "idtrans": "1",
    "estado": "00",
    "respuesta": "Transaccion exitosa",
    "codigoauth": "123456",
    "saldo": "1000",
    "codop": "123456",
    "jsonAdicional": {
	//Placa->Placa del vehículo venta final
        "Placa": "JGV151",
	//Cedula-> Cedula del cliente venta final
        "Cedula": "12345678",
	//Nombre-> Nombre del cliente venta final
        "Nombre": "ASTEBIA",
	//Apellido-> Apellido del cliente venta final
        "Apellido": "RAMOS",
	//Telefono-> Teléfono del cliente venta final
        "Telefono": "3214598028",
	//Numero Soat-> Numero del formulario venta final
        "Numero Soat": "78490935",
	//Valor Prima Soat-> Valor de la prima soat venta final
        "Valor Prima Soat": 487850,
	//Valor RUNT-> valor del costo del runt venta final
        "Valor RUNT": 1700,
	//Terminos y Condiciones-> Url con términos y condiciones venta Soat
        "Terminos y Condiciones": "http://praw.biz/a/terminosSoat.pdf",
	//Url-> Url de descarga directa de PDF soat (funciona 10 segundos después de expedida póliza)
        "Url": "http://dw.praw.biz/web/dw.php?h=1164f900b8d35653",
	//
        "Emisor": "Mundial"
    }
}
//Response Erroneos
		// Error
		{
		    "idtrans": "1",
		    "estado": "05",
		    "respuesta": "Transaccion no exitosa",
		    "codigoauth": "",
		    "saldo": "1000",
		    "codop": "001"
		}
```

</details>

<details>

<summary>Facturas</summary>

(El sistema tomara automáticamente la comisió&#x6E;**,** sea factura de energía o gas, o el resto de los convenios) Para vender este producto, se debe primero consultar convenio manual o automático con código de barras&#x20;

Datos adicionales:

**Prueba**

preConsulta **convenios\_consulta**&#x20;

```php
{
  "idcomercio":"1425",
  "claveventa":"1234",
  "tipoConsulta":"convenios_consulta",
  "data":{
		//key = Palabras clave busqueda de convenio, en blanco para listar todos disponibles
    "key":"CLARO - COMCEL"
  },
  "idTrx":"1"
}
```

```php
//Response exitoso
	"data": {
		//reply-> respuesta validación documento, ok si se encuentra convenios, error si no se encuentran
		"reply": "ok",
		//estado-> estado consulta productos(00)correcto (05)error
		"estado": "00",
		//convenios-> array con los conveios de facturas disponibles, identificados de la siguiente forma:
		"convenios":"{
			//código del convenio-> código del convenio, contiene array con los siguientes datos
			\"8313\":{
				//id-> código del convenio
				\"id\":\"8313\",
				//codigoInterno -> este código debe validarse a diario, si código no corresponde a una consulta previa debe actualizarse todo el producto convenio
				\"codigoInterno\":null,
				//nombre-> nombre del convenio
				\"nombre\":\"CLARO - COMCEL\",
				//campoReferencia->ayuda de referencia para el usuario, se puede mostrar como guía
				\"campoReferencia\":\"CUENTA O REF. DE PAGO.\",
				//ciudad->ciudad perteneciente al recuado
				\"ciudad\":\"\",
				//pagoParcial ->indica si el cliente que esta pagando puede modificar el valor a pagar devuelto por la consulta de referencia
				\"pagoParcial\":\"0\",
				//soloAutomatico ->indica si se debe pagar únicamente leyendo el código de barras, o si se puede usar búsqueda de convenio manual y consulta de referencia manual (1) true (0) false
				\"soloAutomatico\":\"0\",
				//tipo -> indica tippo de convenio (0)facturas normales (1)Energia o gas
				\"tipo\":\"0\"
			},
			\"8314\":{
				\"id\":\"8314\",
				\"codigoInterno\":null,
				\"nombre\":\"GASES DE OCCIDENTE\",
				\"campoReferencia\":\"DIGITE REFERENCIA\",
				\"ciudad\":\"\",
				\"pagoParcial\":\"0\",
				\"soloAutomatico\":\"0\",
				\"tipo\":\"2\"
			}
			...
		}
	}
			

//Response Erroneos
	// Error en data
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": null
	}
	// Error 
	{
	    "error": "parametros incompletos"
	}
```



**Prueba**

preConsulta **consultaValorConvRef**&#x20;

```php
{
  "idcomercio":"1425",
  "claveventa":"1234",
  "tipoConsulta":"consultaValorConvRef",
  "data":{
		//idConv -> xxxx string id del convenio a recaudar
    "idConv":"134467",
		//extConvenio -> xxxx string numero de referencia solicitada por convenio de pago
    "extConvenio":"1234"
  },
  "idTrx":"1"
}
```

```php
//Response exitoso
	{
    "estado": "00",
    "respuesta": "Consulta Correcta",
    "data": {
				//reply = respuesta validación documento, ok si se encuentra documento, error si no se encuentra
        "reply": "ok",
				//estado = estado consulta documento (00)correcto (05)error
        "estado": "00",
				//referencia = Referencia a pagar
        "referencia": "1234",
				//nconvenio = Nombre del convenio a pagar
        "nconvenio": "MOVISTAR SERVICIOS MOVILES",
				//valorPago = valor de la factura a cancelar, se puede modificar si pago parcial lo permite
        "valorPago": "103990",
				//idPre = Numero de transacción, debe enviarse al momento de realizar el pago
        "idPre": "99694264",
				//pagoParcial = Si es 1, se podrá pagar un valor parcial del valor total de pago, si es 0, deberá pagarse el total indicado en valorMonto
        "pagoParcial": 1
    }
}

//Response Erroneos
	// Error en data
	{
		"estado": "05",
		"respuesta": "Consulta con error",
		"data": {
		      "reply": "Error en datos Codigo Interno"
		}
	}
	//Error
	{
	    "error": "parametros incompletos"
	}
	// Covenio no validoválido 
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": {
	        "reply": "Convenio no valido"
	    }
	}
```



**Prueba**

preConsulta **verifyBillEan**&#x20;

```php
{
  "idcomercio":"1425",
  "claveventa":"1234",
  "tipoConsulta":"verifyBillEan",
  "data":{
    //eanbill -> EAN escaneado del código de barras
    "eanbill":"41577071769601788020060055370041390000000000103990962020063060055370041"
  },
  "idTrx":"1"
}
```

```php
//Response exitoso
	{
	    "estado": "00",
	    "respuesta": "Consulta Correcta",
	    "data": {
	        //reply = respuesta validación documento, ok si se encuentra documento, error si no se encuentra			
					"reply": "ok",
					//estado = estado consulta documento (00)correcto (05)error
	        "estado": "00",
					//referencia = Referencia a pagar
	        "referencia": "1234",
					//nconvenio = Nombre del convenio a pagar
	        "nconvenio": "MOVISTAR SERVICIOS MOVILES",
					//valorPago = valor de la factura a cancelar, se puede modificar si pago parcial lo permite
	        "valorPago": "103990",
					//idPre = Numero de transacción, debe enviarse al momento de realizar el pago
	        "idPre": "99694264",
					//pagoParcial = Si es 1, se podrá pagar un valor parcial del valor total de pago, si es 0, deberá pagarse el total indicado en valorMonto
	        "pagoParcial": 1
	    }
	}

//Response Erroneos

// Error en data
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": {
	        "reply": "Error en datos EAN"
	    }
	}
	// Covenio no validoválido 
	{
	    "estado": "05",
	    "respuesta": "Consulta con error",
	    "data": {
	        "reply": "Convenio no valido"
	    }
	}
	//Error 
	{
	    "error": "parametros incompletos"
	}
```



**Prueba**&#x20;

pracRec **fc**&#x20;

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"3214598028",
    "operador":"fc",
    "valor":"103990",
    "jsonAdicional":{
				//idPre = id recibido previamente en consulta de factura
        "idPre":"99694264"
    },
    "idtrans":"1"
}
```

```php
//Response exitoso
{
    "idtrans": "1",
    "estado": "00",
    "respuesta": "Transaccion exitosa",
    "codigoauth": "123456",
    "saldo": "1000",
    "codop": "123456",
    "jsonAdicional": {
				// Codigo Convenio -> Codigo del convenio entregado por el banco
        "Codigo Convenio": "18931",
				// Nombre Convenio -> Nombre del convenio que se pago
        "Nombre Convenio": "MERCADOPAGO",
				// Referencia -> Referencia del convenio que se pago
        "Referencia": "1234"
    }
}

//Response Erroneos
	// Error
	{
		"error": "parametros incompletos"
	}
	//error en idPre
	{
	    "idtrans": "",
	    "estado": "",
	    "respuesta": ":Datos erroneos para esta venta",
	    "codigoauth": "",
	    "saldo": "",
            "codop": "0"
	}
	// Error en operador
	{
	    "idtrans": "1",
	    "estado": "05",
	    "respuesta": "Transaccion no exitosa",
	    "codigoauth": "",
	    "saldo": "1000",
	    "codop": "001"
	}
```

</details>

<details>

<summary>Pin EPM</summary>

**Prueba**

preConsulta **validarPinEpm**&#x20;

```php
{
  "idcomercio":"1425",
  "claveventa":"1234",
  "tipoConsulta":"validarPinEpm",
  "data":{
		// numeroContador -> Numero contador de energía
    "numeroContador":"01111111111",
		// cantidadVenta-> Valor de venta del pin de energía a expedir
    "cantidadVenta":"5000"
  },
  "idTrx":"1"
}

```

```php

//Response exitoso
	{
	    "estado": "00",
	    "respuesta": "Consulta Correcta",
	    "data": {
		//estado-> estado consulta documento (00)correcto (05)error
	        "estado": "00",
		//reply-> respuesta validación documento, ok si se encuentra documento, error si no se encuentra
	        "reply": "ok",
		//respuesta-> respuesta textual validación consulta factuar manual
	        "respuesta": "",
		//nombreClienteEpm -> Nombre Cliente
	        "nombreClienteEpm": "JUAN PEREZ",
		//dniClienteEpm ->Documento cliente
	        "dniClienteEpm": "123456",
		//direccionClienteEpm -> Dirección Contador
	        "direccionClienteEpm": "CRA 10 6 90",
		//localidadEpm -> Localidad ubicación Contador
	        "localidadEpm": "MEDELLIN",
		//departamentoEpm -> Departamento Ubicación Contador
	        "departamentoEpm": "ANTIOQUIA"
	    }
	}

//Response Erroneos
		// Error
		{
		    "error": "parametros incompletos"
		}
		// Error en data
		{
		    "estado": "05",
		    "respuesta": "Consulta con error",
		    "data": null
		}
		// Error en numeroContador
		{
		    "estado": "05",
		    "respuesta": "Consulta con error",
		    "data": {
		        "reply": "ok",
		        "estado": "05",
		        "nombre": "Usuario no existe"
		    }
		}
		
```



**Prueba**

pracRec **em**&#x20;

<mark style="background-color:red;">**Nota:**</mark> En el campo `‘celular’` debe enviarse el campo `‘numeroContador’` de la preconsulta anterior y en el campo `‘valor’` el campo `‘cantidadVenta’`, ver ejemplo.&#x20;

Se debe enviar en el campo jsonAdicional ( **telEnvio** ) en caso de querer enviar el sms por parte de GENESIS, en caso de que no, se puede enviar un numero de teléfono genérico ( 3100000000 )

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"01111111111",
    "operador":"em",
    "valor":"5000",
    "jsonAdicional":{
	// telEnvio -> Numero telefónico para envio pin, en caso de host se envía en respuesta del host
        "telEnvio":"3100000000"
    },
    "idtrans":"1"
}
```

```php
//Response exitoso
	{
	    "idtrans": "1",
	    "estado": "00",
	    "respuesta": "Transaccion exitosa",
	    "codigoauth": "123456",
	    "saldo": "1000",
	    "codop": "123456",
	    "jsonAdicional": {
		//Numero Pin -> Numero del pin solicitado
	        "Numero Pin": "6754 4382 1411 6542 0312",
		//Cupon -> Cupón informativo
	        "Cupon": "",
		//Cantidad Kwh -> Cantidad kwh entregados por el pin
	        "Cantidad Kwh": "35.006",
		//Estrato -> Estrato Ubicación Contador
	        "Estrato": "2",
		//Subsidio -> Subsidio Entregado
	        "Subsidio": "9860.9",
		//Contrubucion -> Info Contribución
	        "Contribucion": "0.0",
		//Otros Cobros -> Otros cobros información
	        "Otros Cobros": "0.0",
		//Valor Aplicar Deuda -> Valor Aplicar a deuda Información
	        "Valor Aplicar Deuda": "0",
		//Saldo Pendiente -> Saldo Pendiente información
	        "Saldo Pendiente": "83892",
		//Valor Tasa Aseo -> Valor tasa aseo información
	        "Valor Tasa Aseo": "0.0",
		//Saldo Tasa Aseo -> Saldo tasa aseo información
	        "Saldo Tasa Aseo": "0.0"
	    }
	}
	
//Response Erroneos
		// Error
		{
		    "error": "parametros incompletos"
		}
		//error en telEnvio
		{
		    "idtrans": "",
		    "estado": "",
		    "respuesta": ":Datos erroneos para esta venta",
		    "codigoauth": "",
		    "saldo": "",
		    "codop": "0"
		}
		// Error en operador
		{
		    "idtrans": "1",
		    "estado": "05",
		    "respuesta": "Transaccion no exitosa",
		    "codigoauth": "",
		    "saldo": "1000",
		    "codop": "001"
		}
```

</details>

<details>

<summary>Pines multimedia</summary>

Se envía en el campo `valor`, el id del pin a vender, si desea se puede enviar un numero genérico(3100000000) en el campo teléfono, ya que GENESIS no enviara el sms



**Prueba**

pracRec **Código Operador:** Al final de la pagina [](<> "mention") &#x20;

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"3100000000",
    "operador":"nx",
    "valor":"177",
    "jsonAdicional":{
      
    },
    "idtrans":"123456"
}
```

```php
//Response exitoso
	{
	    "idtrans": "123456",
	    "estado": "00",
	    "respuesta": "Transaccion exitosa",
	    "codigoauth": "123456",
	    "saldo": "1000",
	    "codop": "123456",
	    "jsonAdicional": {
	        "info": "aa-bb-cc-dd"
	    }
	}

//Response Erroneos
	// Error
	{
	    "error": "parametros incompletos"
	}
	// Error en data
	{
	    "idtrans": "1",
	    "estado": "05",
	    "respuesta": "Transaccion no exitosa",
	    "codigoauth": "",
	    "saldo": "1000",
	    "codop": "001"
	}
```



</details>

<details>

<summary>Wallets</summary>

Para **CashOut** se debe enviar en `valor` un 0, ya que al ser cashout no se asigna valor de venta.&#x20;

Para **CashIn,** jsonAdicional se debe enviar vacío.



**Prueba**

pracRec **Código Operador:** Al final de la pagina [](<> "mention") &#x20;

```php
{
    "idcomercio":"1425",
    "claveventa":"1234",
    "celular":"3214598028",
    "operador":"nr",
    "valor":"0",
    "jsonAdicional":{
	// tokenr -> Token generado por la aplicación para realizar el retiro
        "tokenr":"123456",
	// valorr -> Valor del retiro
        "valorr":"10000"
    },
    "idtrans":"123456"
}
```

```php
//Response exitoso
	{
	    "idtrans": "123456",
	    "estado": "00",
	    "respuesta": "Transaccion exitosa",
	    "codigoauth": "123456",
	    "saldo": "1000",
	    "codop": "123456",
	    "jsonAdicional": null
	}

//Response Erroneos
	// error data
	{
	    "error": "parametros incompletos"
	}
	// Error tokenr
	{
	    "idtrans": "",
	    "estado": "",
	    "respuesta": ":token no encontrado",
	    "codigoauth": "",
	    "saldo": "",
	    "codop": "0"
	}
	// Error en operador
	{
	    "idtrans": "123456",
	    "estado": "05",
	    "respuesta": "Transaccion no exitosa",
	    "codigoauth": "",
	    "saldo": "1000",
	    "codop": "001"
	}
```

</details>

<details>

<summary>Retiros Bancos ATH</summary>

Para **retiros** se debe enviar en `valor` un 0, ya que al ser retiro no se asigna valor de venta.

**Prueba**

pracRec&#x20;

**Código Operador: br**

**athBanco:** 1 <mark style="background-color:blue;">**Banco de Bogotá**</mark>, 2 <mark style="background-color:blue;">**Banco popular**</mark>, 3 <mark style="background-color:blue;">**Occidente**</mark>, 4 <mark style="background-color:blue;">**Av villas**</mark>

```json

{
    "idcomercio":1425,
    "claveventa":1234,
    "idtrans":2,
    "celular":"3214598028",
    "operador":"br",
    "valor":"0",
    "jsonAdicional":{
        "athBanco":"4",
        "athCuenta":"1",
        "athIdentificacion":"12345",
        "athCelular":"3214598028",
        "athNuCuenta":"12345",
        "valorr":10000,
        "tokenr":"123456"
    }  
}

```

```json
//Response exitoso
    {
        "idtrans": 2,
        "estado": "00",
        "respuesta": "Transaccion exitosa",
        "codigoauth": "123456",
        "saldo": "1000",
        "codop": "123456",
        "jsonAdicional": {
            "Nombre Convenio": "Retiro",
            "Referencia": "****1234",
            "Id Aprobacion": "12345",
            "Codigo Convenio": "RETIRO",
            "No Identificacion": "****1234",
            "Celular": "3214598028",
            "Banco": "BOGOTA",
            "Cuenta": "****1234",
            "Valor Retiro": "10000",
            "LINEA DE ATENCION AL CLIENTE": "BOGOTA:7432626 /NACIONAL: 018000512825"
        }
    }

//Response Erroneos
	// error 
	data{
            "idtrans": "",
            "estado": "",
            "respuesta": ":token no encontrado",
            "codigoauth": "",
            "saldo": "",
            "codop": "0"
        }
```

</details>

<details>

<summary>Depositos Banco Caja Social</summary>

## Pre-Consulta

Para **Depositos** se debe enviar una pre-consulta con:

<table><thead><tr><th>Action</th><th>Content</th></tr></thead><tbody><tr><td>Method</td><td>POST</td></tr><tr><td>URL</td><td><a href="https://recargas.practisistemas.com/api_test/preConsulta">https://recargas.practisistemas.com/api_test/preConsulta</a></td></tr><tr><td>Body</td><td><p></p><pre class="language-postman_json"><code class="lang-postman_json">{
    "idcomercio":"1425",
    "claveventa":"1234",
    "tipoConsulta":"consultaSd",
    "data":{
    "valorSd":"10000",
    "sdCuenta":"24083018583",
    "operador":"sd"
    },
    "idTrx":"1"
}
</code></pre></td></tr></tbody></table>

**Glosario**&#x20;

| Variable     | Value        | Description                                                                                   |
| ------------ | ------------ | --------------------------------------------------------------------------------------------- |
| operador     | sd           | Social Deposito                                                                               |
| tipoConsulta | consultaSd   | Consulta Social Deposito                                                                      |
| idTrx        | Valor String | Este ID lo proporciona cliente quien esta integrando para trasabilidad entre los dos sistemas |
| valorSd      | Valor String | Valor deposito                                                                                |
| sdCuenta     | Valor String | Cuenta destino                                                                                |

**Response**

```
{
    "estado": "00",
    "respuesta": "Consulta Correcta",
    "data": {
        "estado": "00",
        "reply": "ok",
        "nombre_cliente": "YOLANDA - CLAROS V",
        "id_trx": 100934079
    }
}
```

## Venta

Para realizar la Venta se requiere:

<table><thead><tr><th>Action</th><th>Content</th></tr></thead><tbody><tr><td>Method</td><td>POST</td></tr><tr><td>URL</td><td><a href="https://recargas.practisistemas.com/api_test/pracRec">https://recargas.practisistemas.com/api_test/pracRec</a></td></tr><tr><td>Body</td><td><p></p><pre class="language-postman_json"><code class="lang-postman_json">{
    "idcomercio": "1425",
    "claveventa": "1234",
    "celular": "24083018583",
    "operador": "sd",
    "valor": "10000",
    "idtrans": "1234",
    "jsonAdicional": {"caCelular":3214598028,"id_trx":100933393}
}
</code></pre></td></tr></tbody></table>

**Glosario**

<table><thead><tr><th>Variable</th><th>Value</th><th>Description</th></tr></thead><tbody><tr><td>operador</td><td>sd</td><td>Social Deposito</td></tr><tr><td>celular</td><td>Valor String</td><td>Cuenta  a la que se va a depositar</td></tr><tr><td>idtrans</td><td>Valor String</td><td>Este ID lo proporciona cliente quien esta integrando para trasabilidad entre los dos sistemas</td></tr><tr><td>caCelular</td><td>Valor String</td><td>Numero celular de quien deposita</td></tr><tr><td>id_trx</td><td>Valor String</td><td><p>ID obtenido de la preconsulta del response, campo </p><pre class="language-json"><code class="lang-json">id_trx
</code></pre></td></tr></tbody></table>

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
        "Terminal": "1838",
        "Comercio": "15034",
        "Ciudad": "Chia",
        "Direccion": "km 32 cajica chia",
        "NombreCliente": "Nombre de pruebas",
        "CuentaDeposito": "*******8583",
        "CostoTransaccion": 0,
        "ValorTransaccion": 2000,
        "CodigoAuth": "443980",
        "Url": "https://recargas.practisistemas.com/web/ticket.php?h=32d887e28ff3711a"
    }
}
```

</details>

<details>

<summary>Retiros Banco Caja Social</summary>

## Venta

Para **Retiros** se debe enviar una venta  con:

<table><thead><tr><th>Action</th><th>Content</th></tr></thead><tbody><tr><td>Method</td><td>POST</td></tr><tr><td>URL</td><td><a href="https://recargas.practisistemas.com/api_test/pracRec">https://recargas.practisistemas.com/api_test/pracRec</a></td></tr><tr><td>Body</td><td><p></p><pre class="language-postman_json"><code class="lang-postman_json">{
    "idcomercio": "1425",
    "claveventa": "1234",
    "celular": "12345678",
    "operador": "sr",
    "valor": "0",
    "idtrans": "1234",
    "jsonAdicional": 
        {
            "tokenr":123456,
            "valorr":10000,
            "sdCelular":3214598028
        }
}
</code></pre></td></tr></tbody></table>

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
