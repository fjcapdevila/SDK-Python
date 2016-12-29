<a name="inicio"></a>		
Todo Pago - módulo SDK-Python para conexión con gateway de pago
=======

 + [Instalación](#instalacion)
 	+ [Versiones de Python soportadas](#Versionesdepysoportadas)
 	+ [Generalidades](#general)
 + [Uso](#uso)		
    + [Inicializar la clase correspondiente al conector (TodoPago\Sdk)](#initconector)
    + [Solicitud de autorización](#solicitudautorizacion)
    + [Confirmación de transacción](#confirmatransaccion)
    + [Ejemplo](#ejemplo)
    + [Modo test](#test)
 + [Datos adicionales para prevención de fraude](#datosadicionales) 
    + [Datos de referencia](#datosreferencia) 
 + [Características](#caracteristicas)
    + [Status de la operación](#status)
    + [Consulta de operaciones por rango de tiempo](#statusdate)
    + [Devolución](#devolucion)
    + [Devolución parcial](#devolucionparcial)
    + [Formulario híbrido](#formhidrido)
    + [Obtener Credenciales](#credenciales)
 + [Diagrama de secuencia](#secuencia)
 + [Tablas de referencia](#tablareferencia)		
 + [Tabla de errores](#codigoerrores)		 

<a name="instalacion"></a>		
## Instalación		
El SDK utiliza como dependencias *suds-jurko*, *requests* y *xmltodict*. Para instalarlas correr las siguientes líneas en la consola:
ej: pip install suds-jurko <-- instalar la dependencia
```bash
> pip install suds-jurko
> pip install requests
```

<a name="Versionesdepysoportadas"></a>   
##Versiones de Python soportadas
La versión implementada de la SDK, esta testeada para versiones desde Python 2.7 en adelante. 

<a name="general"></a>
##Generalidades
Esta versión soporta únicamente pago en moneda nacional argentina (CURRENCYCODE = 32).
Todos los métodos devuelven la respuesta en forma de diccionario.
[<sub>Volver a inicio</sub>](#inicio)
	
<br>
<a name="uso"></a>		
## Uso		

<a name="initconector"></a>
####Inicializar la clase correspondiente al conector (TodoPagoConnector).

- crear un JSON con los http header suministrados por todo pago
```python
j_header_http = {
    "Authorization":"PRISMA f3d8b72c94ab4a06be2ef7c95490f7d3"
}
```
- crear una instancia de la clase TodoPago para hacer pruebas en el ambiente developers se pasa como modo "test" y para producción "prod".

```python
tpc = TodoPagoConnector(j_header_http, 'test')

```		

<a name="solicitudautorizacion"></a>
####Solicitud de autorización		
En este caso hay que llamar a sendAuthorizeRequest(). 		
```python	
response = tpc.sendAuthorizeRequest(optionsSAR_comercio, optionsSAR_operacion)
```				
<ins><strong>datos propios del comercio</strong></ins>		
optionsSAR_comercio debe ser un diccionario con la siguiente estructura:		
<a name="url_ok"></a>		
<a name="url_error"></a>	
```python
optionsSAR_comercio = {
"Session": "ABCDEF-1234-12221-FDE1-00000200",
"Security": "f3d8b72c94ab4a06be2ef7c95490f7d3",
"EncodingMethod": "XML",
"URL_OK": "http,//someurl.com/ok/",
"URL_ERROR": "http,//someurl.com/fail/",
"EMAILCLIENTE": "email_cliente@dominio.com"
}	
```		

*En el ejemplo se envían parámetros en la url (en nuestro ejemplo: ?Order=27398173292187), para ser recibidos por la tienda vía **get** y de este modo recuperar el valor en un próximo paso.

<ins><strong>datos propios de la operación</strong></ins>		
optionsSAR_operacion debe ser un diccionario con la siguiente estructura:		
		
```python
optionsSAR_operacion = {
"MERCHANT": "2866",
"OPERATIONID": "06",
"CURRENCYCODE": "032",
"AMOUNT": "54",
"MININSTALLMENTS": "3",
"MAXINSTALLMENTS": "6",
"CSBTCITY": "Villa General Belgrano",
"CSSTCITY": "Villa General Belgrano",
"CSMDD6" : "",
"CSBTCOUNTRY": "AR",
"CSSTCOUNTRY": "AR",

"CSBTEMAIL": "todopago@hotmail.com",
"CSSTEMAIL": "todopago@hotmail.com",

"CSBTFIRSTNAME": "Juan",
"CSSTFIRSTNAME": "Juan",

"CSBTLASTNAME": "Perez",
"CSSTLASTNAME": "Perez",

"CSBTPHONENUMBER": "541160913988",
"CSSTPHONENUMBER": "541160913988",

"CSBTPOSTALCODE": "1010",
"CSSTPOSTALCODE": "1010",

"CSBTSTATE": "B",
"CSSTSTATE": "B",

"CSBTSTREET1": "Cerrito 740",
"CSSTSTREET1": "Cerrito 740",


"CSBTSTREET2": "Cerrito 740",
"CSSTSTREET2": "Cerrito 740",

"CSBTCUSTOMERID": "453458",
"CSBTIPADDRESS": "192.0.0.4",
"CSPTCURRENCY": "ARS",
"CSPTGRANDTOTALAMOUNT": "125.38",
"CSMDD7": "",
"CSMDD8": "Y",
"CSMDD9": "",
"CSMDD10": "",
"CSMDD11": "",
"STCITY": "rosario",
"STCOUNTRY": "",
"STEMAIL": "jose@gmail.com",
"STFIRSTNAME": "Jose",
"STLASTNAME": "Perez",
"STPHONENUMBER": "541155893737",
"STPOSTALCODE": "1414",
"STSTATE": "D",
"STSTREET1": "San Martin 123",
"CSMDD12": "", 
"CSMDD13": "", 
"CSMDD14": "", 
"CSMDD15": "", 
"CSMDD16": "", 
"CSITPRODUCTCODE": "electronic_good#electronic_good",
"CSITPRODUCTDESCRIPTION": "NOTEBOOK L845 #SP4304LA DF TOSHIBA",
"CSITPRODUCTNAME": "NOTEBOOK# L845 SP4304LA DF TOSHIBA",
"CSITPRODUCTSKU": "LEVJNS#L36GN",
"CSITTOTALAMOUNT": "1254.40#980.80",
"CSITQUANTITY": "1#2",
"CSITUNITPRICE": "1254.40#980.80"
}

```
Donde MININSTALLMENTS y MAXINSTALLMENTS son datos opcionales para informar la cantidad mínima y máxima de cuotas que ofrecerá el formulario de pago (generalmente de 1 a 12)


<p><strong>Ejemplo de Respuesta</strong></p>
```python	
    {
    'StatusCode' : -1, 
    'StatusMessage' : 'Solicitud de Autorizacion Registrada', 
    'URL_Request' : 'https://developers.todopago.com.ar/formulario/commands?command=formulario&m=6d2589f2-37e6-1334-7565-3dc19404480c',
    'RequestKey' : '6d2589f2-37e6-1334-7565-3dc19404480c',
    'PublicRequestKey' : '6d2589f2-37e6-1334-7565-3dc19404480c'
```
La **url_request** es donde está hosteado el formulario de pago y donde hay que redireccionar al usuario, una vez realizado el pago según el éxito o fracaso del mismo, el formulario redireccionará a una de las 2 URLs seteadas en **optionsSAR_comercio** ([URL_OK](#url_ok), en caso de éxito o [URL_ERROR](#url_error), en caso de que por algún motivo el formulario rechace el pago)

Si, por ejemplo, se pasa mal el <strong>MerchantID</strong> se obtendrá la siguiente respuesta:
```python
{'StatusMessage': ERROR: Cuenta Inexistente, 'StatusCode': 702}

```
<a name="confirmatransaccion"></a>
####Confirmación de transacción.		
En este caso hay que llamar a **getAuthorizeAnswer()**, enviando como parámetro un diccionario como se describe a continuación.		
```python		
{		
		'Security': '1234567890ABCDEF1234567890ABCDEF', // Token de seguridad, provisto por TODO PAGO. MANDATORIO.		
		'Merchant'   : '12345678',		
		'RequestKey' : '0123-1234-2345-3456-4567-5678-6789',		
		'AnswerKey'  : '1111-2222-3333-4444-5555-6666-7777' // *Importante		
}		
```		

Se deben guardar y recuperar los valores de los campos <strong>RequestKey</strong> y <strong>AnswerKey</strong>.

El parámetro <strong>RequestKey</strong> es siempre distinto y debe ser persistido de alguna forma cuando el comprador es redirigido al formulario de pagos.

<ins><strong>Importante</strong></ins> El campo **AnswerKey** se adiciona  en la redirección que se realiza a alguna de las direcciones ( URL ) epecificadas en el  servicio **SendAurhorizationRequest**, esto sucede cuando la transacción ya fue resuelta y es necesario regresar al site para finalizar la transacción de pago, también se adiciona el campo Order, el cual tendrá el contenido enviado en el campo **OPERATIONID**. Para nuestro ejemplo: <strong>http://susitio.com/paydtodopago/ok?Order=27398173292187&Answer=1111-2222-3333-4444-5555-6666-7777</strong>		
		
```python		
{	
  'StatusCode'       : -1, 		
  'StatusMessage'    : 'APROBADA',		
  'AuthorizationKey' : '1294-329E-F2FD-1AD8-3614-1218-2693-1378',		
  'EncodingMethod'   : 'XML',		
  'Payload'          : 		
    {		
      'Answer' : 		
         {		
          'DATETIME'               : '2014/08/11 15:24:38',		
          'RESULTCODE'             : '-1',		
          'RESULTMESSAGE'          : 'APROBADA',		
          'CURRENCYNAME'           : 'Pesos',		
          'PAYMENTMETHODNAME'      : 'VISA',		
          'TICKETNUMBER'           : '12',		
          'CARDNUMBERVISIBLE'      : '450799******4905',		
          'AUTHORIZATIONCODE'      : 'TEST38', 
          'INSTALLMENTPAYMENTS'    : '6' }, 
      'Request' : 		
        {
          'MERCHANT'               : '12345678',		
          'OPERATIONID'            : 'ABCDEF-1234-12221-FDE1-00000012',		
          'AMOUNT'                 : '1.00',		
          'CURRENCYCODE'           : '032', 		
          }		
```		
Este método devuelve el resumen de los datos de la transacción.		

Si se pasa mal el <strong>AnswerKey</strong> o el <strong>RequestKey</strong> se verá el siguiente rechazo:

```python
{
  'StatusCode' :  404
  'StatusMessage' : string 'ERROR: Transaccion Inexistente'}


<a name="test"></a>      
####Modo Test

El SDK-Python permite trabajar con los ambiente de desarrollo y de produccion de Todo Pago.<br>
El ambiente se debe instanciar como se indica a continuacion.

Para utlilizar el modo test se debe pasar un end point de prueba (provisto por TODO PAGO).
````python
tpc = TodoPagoConnector(j_header_http_test, j_wsdls_test, end_point_test)
```

[<sub>Volver a inicio</sub>](#inicio)
<br>

<a name="datosadicionales"></a>		
## Datos adicionales para control de fraude		
Los datos adicionales para control de fraude son **obligatorios**, de lo contrario baja el score de la transacción.

Los campos marcados como **condicionales** afectan al score negativamente si no son enviados, pero no son mandatorios o bloqueantes.

```python	
optionsSAR_operacion={ 		
...........................................................................		
'CSBTCITY': "Villa General Belgrano", #Ciudad de facturación, #MANDATORIO.		
'CSBTCOUNTR': "AR", #País de facturación. MANDATORIO. Código ISO. (http://apps.cybersource.com/library/documentation/sbc/quickref/countries_alpha_list.pdf)		
'CSBTCUSTOMERID': "453458", #Identificador del usuario al que se le emite la factura. MANDATORIO. No puede contener un correo electrónico.		
'CSBTIPADDRESS': "192.0.0.4", #IP de la PC del comprador. MANDATORIO.		
'CSBTEMAIL' : "some@someurl.com", #Mail del usuario al que se le emite la factura. MANDATORIO.		
'CSBTFIRSTNAME': "Juan", #Nombre del usuario al que se le emite la factura. MANDATORIO.		
'CSBTLASTNAME': "Perez", #Apellido del usuario al que se le emite la factura. MANDATORIO.		
'CSBTPHONENUMBER': "541160913988", #Teléfono del usuario al que se le emite la factura. No utilizar guiones, puntos o espacios. Incluir código de país. MANDATORIO.		
'CSBTPOSTALCODE': "1010", #Código Postal de la dirección de facturación. MANDATORIO.		
'CSBTSTATE': "B",  #Provincia de la dirección de facturación. MANDATORIO. Ver tabla anexa de provincias.		
'CSBTSTREET1' : "Some Street 2153", #Domicilio de facturación (calle y nro). MANDATORIO.		
'CSBTSTREET2' :"Piso 8", #Complemento del domicilio. (piso, departamento). NO MANDATORIO.		
'CSPTCURRENCY': "ARS",  #Moneda. MANDATORIO.		
'CSPTGRANDTOTALAMOUNT': "10.01", #Con decimales opcional usando el puntos como separador de decimales. No se permiten comas, ni como separador de miles ni como separador de decimales. MANDATORIO. (Ejemplos:$125,38-> 125.38 $12-> 12 o 12.00)
'CSMDD7':"", # Fecha registro comprador(num Dias). NO MANDATORIO.		
'CSMDD8':"", #Usuario Guest? (Y/N). En caso de ser Y, el campo CSMDD9 no deberá enviarse. NO MANDATORIO.	
'CSMDD9':"", #Customer password Hash: criptograma asociado al password del comprador final. NO MANDATORIO.	
'CSMDD10':"", #Histórica de compras del comprador (Num transacciones). NO MANDATORIO.		
'CSMDD11':"", #Customer Cell Phone. NO MANDATORIO.		

#Retail
'CSSTCITY':"Villa General Belgrano",  #Ciudad de enví­o de la orden. MANDATORIO.		
'CSSTCOUNTRY':"AR", #País de envío de la orden. MANDATORIO.		
'CSSTEMAIL': "some@someurl.com", #Mail del destinatario, MANDATORIO.		
'CSSTFIRSTNAME':"Jose", #Nombre del destinatario. MANDATORIO.		
'CSSTLASTNAME':"Perez", #Apellido del destinatario. MANDATORIO.		
'CSSTPHONENUMBER':"541155893737", #Número de teléfono del destinatario. MANDATORIO.		
'CSSTPOSTALCODE':"1010", #Código postal del domicilio de envío. MANDATORIO.		
'CSSTSTATE':"B", #Provincia de envío. MANDATORIO. Son de 1 caracter		
'CSSTSTREET1':"Some Street 2153", #Domicilio de envío. MANDATORIO.		

'CSITPRODUCTCODE':"electronic_good", #Código de producto. CONDICIONAL. Valores posibles(adult_content;coupon;default;electronic_good;electronic_software;gift_certificate;handling_only;service;shipping_and_handling;shipping_only;subscription)		
'CSITPRODUCTDESCRIPTION':"Test Prd Description", #Descripción del producto. CONDICIONAL.	
'CSITPRODUCTNAME':"TestPrd", #Nombre del producto. CONDICIONAL.		
'CSITPRODUCTSKU':"SKU1234", #Código identificador del producto. CONDICIONAL.		
'CSITTOTALAMOUNT':"10.01", #CSITTOTALAMOUNT=CSITUNITPRICE*CSITQUANTITY "999999[.CC]" Con decimales opcional usando el punto como separador de decimales. No se permiten comas, ni como separador de miles ni como separador de decimales. CONDICIONAL.		
'CSITQUANTITY':"1", #Cantidad del producto. CONDICIONAL.		
'CSITUNITPRICE': "10.01", #Formato Idem CSITTOTALAMOUNT. CONDICIONAL.		

'CSMDD12':"" #Shipping, DeadLine (Num Dias). NO MADATORIO.		
'CSMDD13':"", #Método de Despacho. NO MANDATORIO.		
'CSMDD14':"", #Customer requires Tax Bill ? (Y/N). NO MANDATORIO.		
'CSMDD15':"", #Customer Loyality Number. NO MANDATORIO. 		
'CSMDD16':"", #Promotional / Coupon Code. NO MANDATORIO. #Retail: datos a enviar por cada producto, los valores deben estar separados con #:		

...........................................................		
```
<a name="datosreferencia"></a>    
#### Datos de referencia   

<table style="max-width:200px;">
<tr><th>Nombre del campo</th><th>Required/Optional</th><th>Data Type</th><th>Mínimo</th><th>Comentarios</th></tr>
<tr><td style="max-width:200px;">CSBTCITY</td><td>Required</td><td>String (50)</td><td>1</td><td>Ciudad / Debe comenzar con una letra</td></tr>
<tr><td>CSBTCOUNTRY</td><td>Required</td><td>String (2)</td><td>1</td><td>Código ISO</td></tr>
<tr><td> CSBTCUSTOMERID</td><td>Required</td><td>String (50)</td><td>1</td><td>Identificador del usuario unico logueado al portal (No puede ser una direccion de email)</td></tr>
<tr><td> CSBTEMAIL</td><td>Required</td><td>String (100)</td><td>1</td><td>Correo electrónico del comprador con formato válido (solo letras (a-z), números, puntos y sin espacios).</td></tr>
<tr><td>CSBTFIRSTNAME</td><td>Required</td><td>String (60)</td><td>1</td><td>Nombre del tarjeta habiente / Sin caracteres especiales como acentos invertidos, sólo letras, números y espacios</td></tr>
<tr><td>CSBTIPADDRESS</td><td>Required</td><td>String (15)</td><td>1</td><td>"End Customer´s IP address, such as 10.1.27.63, reported by your Web server via socket information."</td></tr>
<tr><td> CSBTLASTNAME</td><td>Required</td><td>String (60)</td><td>1</td><td>Apellido del tarjetahabiente / Sin caracteres especiales como acentos invertidos, sólo letras, números y espacios</td></tr> 
<tr><td>CSBTPHONENUMBER</td><td>Required</td><td>String (15)</td><td>6</td><td>Número de telefono</td></tr>
<tr><td>CSBTPOSTALCODE</td><td>Required</td><td>String (10)</td><td>1</td><td>Codigo Postal</td></tr> 
<tr><td>CSBTSTATE</td><td>Required</td><td>String (2)</td><td>1</td><td>Estado (Si el country = US, el campo se valida para un estado valido en USA)</td></tr>
<tr><td>CSBTSTREET1</td><td>Required</td><td>String (60)</td><td>1</td><td>Calle Numero interior Numero Exterior</td></tr> 
<tr><td>CSBTSTREET2</td><td>Optional</td><td>String (60)</td><td></td><td>Barrio</td></tr>
<tr><td>CSITPRODUCTCODE</td><td>Conditional</td><td>String (255)</td><td></td><td></td> </tr>
<tr><td>CSITPRODUCTDESCRIPTION</td><td>Conditional</td><td>String (255)</td><td></td><td>Descripción general del producto</td></tr> 
<tr><td>CSITPRODUCTNAME</td><td>Conditional</td><td>String (255)</td><td></td><td>Nombre en catalogo del producto</td></tr>
<tr><td>CSITPRODUCTSKU</td><td>Conditional</td><td>String (255)</td><td></td><td>SKU en catalogo</td></tr> 
<tr><td>CSITQUANTITY</td><td>Conditional</td><td>Integer (10)</td><td></td><td>Cantidad productos del mismo tipo agregados al carrito</td></tr> 
<tr><td>CSITTOTALAMOUNT</td><td>Conditional</td><td></td><td></td><td>"Precio total = Precio unitario * quantity / CSITTOTALAMOUNT = CSITUNITPRICE * CSITQUANTITY ""999999.CC"" Es mandatorio informar los decimales, usando el punto como separador de decimales. No se permiten comas, ni como separador de miles ni como separador de decimales."</td></tr>
<tr><td>CSITUNITPRICE</td><td>Conditional</td><td>String (15)</td><td></td><td>"Precio Unitaro del producto / ""999999.CC"" Es mandatorio informar los decimales, usando el punto como separador de decimales. No se permiten comas, ni como separador de miles ni como separador de decimales."</td></tr> 
<tr><td>CSPTCURRENCY</td><td>Required</td><td>String (5)</td><td>1</td><td>Currencies=>'032'(Peso Argentino)</td></tr> 
<tr><td>CSPTGRANDTOTALAMOUNT</td><td>Required</td><td>Decimal (15)</td><td>1</td><td>"Cantidad total de la transaccion./""999999.CC"" Con decimales obligatorios, usando el puntos como separador de decimales. No se permiten comas, ni como separador de miles ni como separador de decimales."</td></tr> 
<tr><td>CSSTCITY</td><td>Required</td><td>String (50)</td><td>1</td><td>Ciudad / Debe comenzar con una letra</td>
<tr><td> CSSTCOUNTRY</td><td>Required</td><td>String (2)</td><td>1</td><td>Código ISO</td></tr>
<tr><td>CSSTEMAIL</td><td>Required</td><td>String (100)</td><td>1</td><td>Correo electrónico del comprador con formato válido (solo letras (a-z), números, puntos y sin espacios).</td></tr> 
<tr><td>CSSTFIRSTNAME</td><td>Required</td><td>String (60)</td><td>1</td><td>Nombre del tarjeta habiente / Sin caracteres especiales como acentos invertidos, sólo letras, números y espacios</td></tr> 
<tr><td>CSSTLASTNAME</td><td>Required</td><td>String (60)</td><td>1</td><td>Apellido del tarjetahabiente / Sin caracteres especiales como acentos invertidos, sólo letras, números y espacios</td></tr> 
<tr><td>CSSTPHONENUMBER</td><td>Required</td><td>String (15)</td><td>6</td><td>"Número de telefono. Cuidar el hecho que por default algunos comercios envían ""54"", contando entonces con 2 de los 6 caracteres requeridos."</td></tr> 
<tr><td>CSSTPOSTALCODE</td><td>Required</td><td>String (10)</td><td>1</td><td>Código Postal</td></tr>
<tr><td>CSSTSTATE</td><td>Required</td><td>String (2)</td><td>1</td><td>Estado (Si el country = US, el campo se valida para un estado v lido en USA)</td><tr> 
<tr><td>CSSTSTREET1</td><td>Required</td><td>String (60)</td><td>1</td><td>Calle Numero interior Numero Exterior / Para los casos que no son de envío a domicilio, jam s enviar la dirección propia del comercio o correo donde se retire la mercadería, en ese caso replicar los datos de facturación.</td></tr>
<tr><td> CSSTSTREET2</td><td>Optional</td><td>String (60)</td><td></td><td>Barrio</td></tr> 
<tr><td>CSMDD1 </td><td>Required</td><td>String (255)</td><td>1</td><td>Incluir numero de comercio proveniente del campo NROCOMERCIO del API DECIDIR</td></tr> 
<tr><td>CSMDD2</td><td>Required</td><td>String (255)</td><td>1</td><td>Incluir el nombre del comercio, Decidir puede obtener este dato del portal de configuracion de comercios</td></tr>
<tr><td> CSMDD3</td><td>Required (Catalogo)</td><td>String (255)</td><td>1</td><td>"Valores ejemplo: (retail, digital goods, services, travel, ticketing) Es recomendable que el API de decidir fije opciones seleccionables y no sean de captura libre para el comercio"</td></tr> 
<tr><td>CSMDD4</td><td>Optional (Catalogo)</td><td>String (255)</td><td></td><td>"Valores ejemplo: (Visa, Master Card, Tarjeta Shopping, Banelco...) Es recomendable que el API de decidir fije opciones seleccionables y no sean de captura libre para el comercio. Se tienen que incluir todos los medios de pago aceptados"</td></tr>
<tr><td> CSMDD5</td><td>Optional</td><td>String (255)</td><td></td><td>Valor numerico que detalle el numero de cuotas</td></tr>
<tr><td>CSMDD6</td><td>Optional (Catalogo)</td><td>String (255)</td><td></td><td>"Valores ejemplo: (Web, Call Center, Mobile, Kiosko) Es recomendable que el API de decidir fije opciones seleccionables y no sean de captura libre para el comercio."</td></tr> 
<tr><td>CSMDD7</td><td>Optional</td><td>String (255)</td><td></td><td>Numero de dias que tiene registrado un cliente en el portal del comercio.</td></tr>
<tr><td>CSMDD8</td><td>Optional</td><td>String (255)</td><td></td><td>Valor Boleano para indicar si el usuario esta comprando como invitado en la pagina del comercio. Valores posibles (S/N)</td></tr> 
<tr><td>CSMDD9</td><td>Optional</td><td>String (255)</td><td></td><td>Valor del password del usuario registrado en el portal del comercio. Incluir el valor en hash</td></tr> 
<tr><td>CSMDD10</td><td>Optional</td><td>String (255)</td><td></td><td>Conteo de transacciones realizadas por el mismo usuario registrado en el portal del comercio</td></tr>
<tr><td>CSMDD11</td><td>Optional</td><td>String (255)</td><td></td><td>Incluir numero de telefono adicional del comprador</td></tr> 
<tr><td>CSMDD12</td><td>Optional</td><td>String (255)</td><td></td><td>Numero de dias que tiene el comercio para hacer la entrega</td></tr> 
<tr><td>CSMDD13</td><td>Optional (Catalogo)</td><td>String (255)</td><td></td><td>"Valores ejemplo: (domicilio, click and collect, carrier) Es recomendable que el API de decidir fije opciones seleccionables y no sean de captura libre para el comercio."</td></tr> 
<tr><td>CSMDD14</td><td>Optional</td><td>String (255)</td><td></td><td>Valor booleano para identificar si el cliente requiere un comprobante fiscal o no S / N</td></tr>
<tr><td>CSMDD15</td><td>Optional</td><td>String (255)</td><td></td><td>Incluir numero de cliente frecuente</td></tr>
<tr><td>CSMDD16</td><td>Optional</td><td>String (255)</td><td></td><td>Incluir numero de cupon de descuento</td></tr>
<tr><td>CSMDD35</td><td>Conditional (Transaccion con Visa)</td><td>String (255)</td><td></td><td>Tipo de documento solicitado por el comercio al cliente</td></tr>
<tr><td>CSMDD36</td><td>Conditional (Transaccion con Visa)</td><td>String (255)</td><td></td><td>Numero de documento solicitado por el comercio al cliente</td></tr>
<tr><td>CSMDD37</td><td>Conditional (Transaccion con Visa)</td><td>String (255)</td><td></td><td>Numero de puerta</td></tr>
<tr><td> CSMDD38</td><td>onditional (Transaccion con Visa)</td><td>String (255)</td><td></td><td>Fecha de nacimiento del comprador, dato solicitado por el comercio. DECIDIR tiene el formato exacto de como se debe de capturar</td></tr>
<tr><td>CSMDD39</td><td>Conditional (Transaccion con Visa)</td><td>String (255)</td><td></td><td>Valor numero correspondiente a la validacion de cada uno de los datos anteriores ejemplo: 1012</td></tr>
<tr><td> CSMDD40</td><td>Optional</td><td>String(1)</td><td></td><td>"Valor para identificar si la transaccion ha sido reportada como fraude por parte del emisor. Incluir el parametro con valor = S Este parametro lo genera decidir a partir de la respuesta del emisor. En caso de una transaccion aceptada por el emisor o con rechazo diferente a fraude, NO INCLUIR"</td></tr> 
<tr><td>CSMDD41</td><td>Optional</td><td>String(1)</td><td></td><td>Datos proporcionado por DECIDIR en el form. De pago. Valores posibles S/N</td></tr> 
<tr><td>CSMDD42</td><td>Optional</td><td>String(1)</td><td></td><td>Datos proporcionado por DECIDIR en el form. De pago. Valores posibles S/N</td></tr>
<tr><td>CSMDD80</td><td>Required </td><td>Integer (20)</td><td></td><td>Número de cuenta del vendedor</td></tr>
<tr><td> CSMDD81</td><td>Required </td><td>String(255)</td><td></td><td>Mail del vendedor en TP</td></tr>
<tr><td> CSMDD82</td><td>Required </td><td>Integer (6)</td><td></td><td>Rubro asignado por el analista de riesgos de Back Office</td></tr>
<tr><td>CSMDD83</td><td>Required </td><td>Integer (2)</td><td></td><td>Antigüedad de la cuenta vendedor</td></tr> 
<tr><td>CSMDD84</td><td>Required </td><td>String (15)</td><td></td><td>Consumidor Final / Profesional / Empresa</td></tr> 
<tr><td>CSMDD85</td><td>Required </td><td>Integer(1)</td><td></td><td>0 (No se le pidió) / 1 (Se le pidió y se validó) / 2 (Uso Futuro)</td></tr>
<tr><td> CSMDD86</td><td>Requerido (para Billetera)</td><td>Integer(20)</td><td></td><td>Número de cuenta del comprador</td></tr>
<tr><td>CSMDD87</td><td>Requerido (para Billetera)</td><td>Integer (3)</td><td></td><td>Antigüedad de la cuenta comprador (Meses)</td></tr> 
<tr><td>CSMDD88</td><td>Requerido (para Billetera)</td><td>Integer (3)</td><td></td><td>Cantidad de tarjetas Habilitadas de la Billetera</td></tr> 
<tr><td>CSMDD89</td><td>Requerido (para Billetera)</td><td>Integer (2)</td><td></td><td>Nivel de Riesgo asignado al Medio de Pago que Utiliza</td></tr>
</table>

[<sub>Volver a inicio</sub>](#inicio)
<br>

<a name="caracteristicas"></a>
## Características

<a name="status"></a>
####Status de la Operación
La SDK cuenta con un método para consultar el status de la transacción desde la misma SDK. El método se utiliza de la siguiente manera:
```python
optionsGS = {
'MERCHANT': merchant, #merchant es una variable que contiene al id site 
'OPERATIONID': operationid # operationid es una variable (id de la operación a consultar)
}
print tpc.getByoperationId(optionsGS)
```
El siguiente método retornará el status actual de la transacción en Todopago.

<ins><strong>Ejemplo de Respuesta</strong></ins>
```python
  '{Operations': 
   		{
      'RESULTCODE' :  '999'
      'RESULTMESSAGE' :  'RECHAZADA'
      'DATETIME' :  '2015-05-13T14:11:38.287+00:00' 
      'OPERATIONID' :  '01' 
      'CURRENCYCODE' :  '32' 
      'AMOUNT' :  54
      'TYPE':  'compra_online' 
      'INSTALLMENTPAYMENTS' :  '4'
      'CUSTOMEREMAIL' :  'cosme@fulanito.com' 
      'IDENTIFICATIONTYPE' :  'DNI'
      'IDENTIFICATION' :  '1212121212' 
      'CARDNUMBER' :  '12121212XXXXXX1212' 
      'CARDHOLDERNAME' :  'Cosme Fulanito' 
      'TICKETNUMBER' :  0
      'AUTHORIZATIONCODE' : null
      'BARCODE' : null
      'COUPONEXPDATE' : null
      'COUPONSECEXPDATE' : null
      'COUPONSUBSCRIBER' : null}
      }
```

Además, se puede conocer el estado de las transacciones a través del portal [www.todopago.com.ar](http://www.todopago.com.ar/). Desde el portal se verán los estados "Aprobada" y "Rechazada". Si el método de pago elegido por el comprador fue Pago Fácil o RapiPago, se podrán ver en estado "Pendiente" hasta que el mismo sea pagado.
	

<a name="statusdate"></a>
####Consulta de operaciones por rango de tiempo
En este caso hay que llamar a getByRangeDateTime() y devolverá todas las operaciones realizadas en el rango de fechas dado

```python
optionsGBRDT = {
'MERCHANT': '2153',
'STARTDATE': '2015-01-01T17:34:42.903',
'ENDDATE': '2015-12-10T17:34:42.903'
}
response = tpc.getByRangeDateTime(optionsGBRDT)
```
<a name="devolucion"></a>
####Devolución

La SDK dispone de métodos para realizar la devolución, de una transacción realizada a traves de TodoPago.

Se debe llamar al método ```voidRequest``` de la siguiente manera:
```python
options = {
"Security" : "837BE68A892F06C17B944F344AEE8F5F", #API Key del comercio asignada por TodoPago 
"Merchant" : "35", #Merchant o Nro de comercio asignado por TodoPago
"RequestKey" : "6d2589f2-37e6-1334-7565-3dc19404480c" #RequestKey devuelto como respuesta del servicio SendAutorizeRequest
}
resp = tpc.voidRequest(options)	
```

También se puede llamar al método ```voidRequest``` de la esta otra manera:
```python

options = {
"Security" : "837BE68A892F06C17B944F344AEE8F5F", #API Key del comercio asignada por TodoPago 
"Merchant" : "35", #Merchant o Nro de comercio asignado por TodoPago
"AuthorizationKey" : "6d2589f2-37e6-1334-7565-3dc19404480c" #AuthorizationKey devuelto como respuesta del servicio GetAuthorizeAnswer
}
resp = tpc.voidRequest(options)	
```

**Respuesta del servicio:**
Si la operación fue realizada correctamente se informará con un código 2011 y un mensaje indicando el éxito de la operación.

```python
{
	"StatusCode" : 2011,
	"StatusMessage" : "Operación realizada correctamente"
}
```
<br>

<a name="devolucionparcial"></a>
####Devolución parcial

La SDK dispone de métodos para realizar la devolución parcial, de una transacción realizada a través de TodoPago.

Se debe llamar al método ```returnRequest``` de la siguiente manera:
```python

options = {
"Security" : "837BE68A892F06C17B944F344AEE8F5F", #API Key del comercio asignada por TodoPago 
"Merchant" : "35", #Merchant o Nro de comercio asignado por TodoPago
"RequestKey" : "6d2589f2-37e6-1334-7565-3dc19404480c" #RequestKey devuelto como respuesta del servicio SendAutorizeRequest
"AMOUNT" : "23.50" #Opcional. Monto a devolver, si no se envía, se trata de una devolución total
}
resp = tpc.returnRequest(options)
```

También se puede llamar al método ```returnRequest``` de la esta otra manera:
```python

options = {
"Security" : "837BE68A892F06C17B944F344AEE8F5F", #API Key del comercio asignada por TodoPago 
"Merchant" : "35", #Merchant o Nro de comercio asignado por TodoPago
"AuthorizationKey" : "6d2589f2-37e6-1334-7565-3dc19404480c" #AuthorizationKey devuelto como respuesta del servicio GetAuthorizeAnswer
"AMOUNT" : "23.50" #Opcional. Monto a devolver, si no se envía, se trata de una devolución total
}
resp = tpc.returnRequest(options)	
```

**Respuesta de servicio:**
Si la operación fue realizada correctamente se informará con un código 2011 y un mensaje indicando el éxito de la operación.

```python
{
	"StatusCode" => 2011,
	"StatusMessage" => "Operación realizada correctamente",
}
```
<br>
<a name="formhidrido"></a>
####Formulario hibrido

**Conceptos basicos**<br>
El formulario hibrido, es una alternativa al medio de pago actual por redirección al formulario externo de TodoPago.<br> 
Con el mismo, se busca que el comercio pueda adecuar el look and feel del formulario a su propio diseño.

**Libreria**<br>
El formulario requiere incluir en la pagina una libreria Javascript de TodoPago.<br>
El endpoint depende del entorno:
+ Desarrollo: https://developers.todopago.com.ar/resources/TPHybridForm-v0.1.js
+ Produccion: https://forms.todopago.com.ar/resources/TPHybridForm-v0.1.js

**Restricciones y libertades en la implementación**

+ Ninguno de los campos del formulario podrá contar con el atributo name.
+ Se deberá proveer de manera obligatoria un botón para gestionar el pago con Billetera Todo Pago.
+ Todos los elementos de tipo <option> son completados por la API de Todo Pago.
+ Los campos tienen un id por defecto. Si se prefiere utilizar otros ids se deberán especificar los
mismos cuando se inicialice el script de Todo Pago.
+ Pueden aplicarse todos los detalles visuales que se crean necesarios, la API de Todo Pago no
altera los atributos class y style.
+ Puede utilizarse la API para setear los atributos placeholder del formulario, para ello deberá
especificar dichos placeholders en la inicialización del formulario "window.TPFORMAPI.hybridForm.setItem". En caso de que no se especifiquen los placeholders se usarán los valores por defecto de la API.

**HTML del formulario**

El formulario implementado debe contar al menos con los siguientes campos.

```html
<body>
	<select id="formaDePagoCbx"></select>
	<select id="bancoCbx"></select>
	<select id="promosCbx"></select>
	<label id="labelPromotionTextId"></label>
	<input id="numeroTarjetaTxt"/>
	<input id="mesTxt"/>
	<input id="anioTxt"/>
	<input id="codigoSeguridadTxt"/>
	<label id="labelCodSegTextId"></label>
	<input id="apynTxt"/>
	<select id="tipoDocCbx"></select>
	<input id="nroDocTxt"/>
	<input id="emailTxt"/><br/>
	<button id="MY_btnPagarConBilletera" class="tp-button"/>
	<button id="MY_btnConfirmarPago"/>
</body>
```

**Inicialización y parámetros requeridos**<br>
Para inicializar el formulario se usa window.TPFORMAPI.hybridForm.initForm. El cual permite setear los elementos y ids requeridos.

Para inicializar un ítem de pago, es necesario llamar a window.TPFORMAPI.hybridForm.setItem. Este requiere obligatoriamente el parámetro publicKey que corresponde al PublicRequestKey (entregado por el SAR).
Se sugiere agregar los parámetros usuario, e-mail, tipo de documento y número.

**Javascript**
```js
window.TPFORMAPI.hybridForm.initForm({
    callbackValidationErrorFunction: 'validationCollector',
	callbackCustomSuccessFunction: 'customPaymentSuccessResponse',
	callbackCustomErrorFunction: 'customPaymentErrorResponse',
        callbackBilleteraFunction: 'billeteraPaymentResponse',
	botonPagarId: 'MY_btnConfirmarPago',
	modalCssClass: 'modal-class',
	modalContentCssClass: 'modal-content',
	beforeRequest: 'initLoading',
	afterRequest: 'stopLoading'
});

window.TPFORMAPI.hybridForm.setItem({
    publicKey: 'taf08222e-7b32-63d4-d0a6-5cabedrb5782', //obligatorio
    defaultNombreApellido: 'Usuario',
    defaultNumeroDoc: 20234211,
    defaultMail: 'todopago@mail.com',
    defaultTipoDoc: 'DNI'
});

//callbacks de respuesta del pago
function validationCollector(parametros) {
}
function customPaymentSuccessResponse(response) {
}
function customPaymentErrorResponse(response) {
}
function billeteraPaymentResponse(response) {
}
function initLoading() {
}
function stopLoading() {
}
```

**Callbacks**<br>
El formulario define callbacks javascript, que son llamados según el estado y la información del pago realizado:
+ customPaymentSuccessResponse: Devuelve response si el pago se realizó correctamente.
+ customPaymentErrorResponse: Si hubo algún error durante el proceso de pago, este devuelve el response con el código y mensaje correspondiente.

** Boton Pagar con Billetera **
En el formulario aparecerá la posibilidad de utilizar la billetera virtual de todopago 
en un boton que se llama "Boton Pagar con Billetera"

**Ejemplo de Implementación**:
<a href="/resources/form_hibrido-ejemplo/index.html" target="_blank">Formulario hibrido</a>
<br>

[<sub>Volver a inicio</sub>](#inicio)

<a name="credenciales"></a>
####Obtener credenciales
El SDK permite obtener las credenciales "Authentification", "MerchandId" y "Security" de la cuenta de Todo Pago, ingresando el usuario y contraseña.<br>
Esta funcionalidad es útil para obtener los parámetros de configuración dentro de la implementación.

- Crear una instancia de la clase User:
```python

j_header_http = {
	'Authorization':''
}

tpc = TodoPagoConnector(j_header_http, "test")

#usario de TodoPago
datosUsuario = {
	'USUARIO' : "usuario@todopago.com.ar", 
	'CLAVE' : "contraseña"
}

#este metodo devuelve un json con las credenciales
tpc.getCredentials(userCredenciales);

```
**Observación**: El Security se obtiene a partir de apiKey, eliminando TODOPAGO de este ultimo.

[<sub>Volver a inicio</sub>](#inicio)
<br>

<a name="secuencia"></a>
##Diagrama de secuencia
![imagen de configuracion](https://raw.githubusercontent.com/TodoPago/imagenes/master/README.img/secuencia-page-001.jpg)

[<sub>Volver a inicio</sub>](#inicio)
<br>

<a name="tablareferencia"></a>    
## Tablas de Referencia   
######[Provincias](#p)    
				
<p>Solo utilizado para incluir los datos de control de fraude</p>
<table>		
<tr><th>Provincia</th><th>Código</th></tr>		
<tr><td>CABA</td><td>C</td></tr>		
<tr><td>Buenos Aires</td><td>B</td></tr>		
<tr><td>Catamarca</td><td>K</td></tr>		
<tr><td>Chaco</td><td>H</td></tr>		
<tr><td>Chubut</td><td>U</td></tr>		
<tr><td>Córdoba</td><td>X</td></tr>		
<tr><td>Corrientes</td><td>W</td></tr>		
<tr><td>Entre Ríos</td><td>E</td></tr>		
<tr><td>Formosa</td><td>P</td></tr>		
<tr><td>Jujuy</td><td>Y</td></tr>		
<tr><td>La Pampa</td><td>L</td></tr>		
<tr><td>La Rioj
