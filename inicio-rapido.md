# Inicio rápido

## Descripción del servicio

Servicio host to host implementado sobre el protocolo **SOAP** ó **REST**, el cual define

* 3 métodos de recarga
  * Recargas
  * Paquetes
  * Venta de productos de multi-producto.
* Auto consulta,
* Consulta de saldo,
* Método de pre-consulta y consulta de productos.

La comunicación se podrá hacer sobre **HTTPS**, **VPN** o **Internet** a conveniencia del cliente, el cual será autenticado con:

* Usuario
* Clave
* IP de origen

## Procedimiento de implementación

El cliente deberá realizar la implementación de este protocolo dentro de su plataforma usando la URL de pruebas, la cual es un Dummie, el cual únicamente permite enviar tramas de prueba, sin necesidad de consumir saldo.

Luego de realizar el desarrollo se hará una certificación en ambas partes afirmando el entendimiento del protocolo y exonerando a **PRACTISISTEMAS** por errores causados de parte del cliente.

## Consideraciones Generales

El tiempo máximo de espera deberá ser definido en 150 segundos para productos **MULTIPRODUCTO**, Y 60 segundos para **RECARGAS**, dándose por entendido que transacción no devuelta en este tiempo deberá darse por **Rechazada**, si de alguna forma **PRACTISISTEMAS** procesa esta transacción, deberá hacerse una conciliación entre ambas partes para determinar el origen del fallo.

**Consideración:** Para que una devolución sea aplicable deberá demostrarse que el cliente realizo auto consulta posterior al intento de inicio de transacción.
