![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.001.jpeg)

**Boletín CIMPRA N° 541** Adecuaciones al Boletín CIMPRA N° 535  

QR interoperable para pagos con tarjeta de crédito/ notificación de pago (desde adquirente/agregador hacia 

billetera)

**Introducción** 

El Boletín CIMPRA N° 535 incorporó adecuaciones a la Interfaz Estandarizada de Pagos (IEP) definida en primera instancia en el boletín CIMPRA 525 y a los QR dinámicos que no utilicen la “API resolve” (boletín CIMPRA 530), enriqueciendo sus criterios operativos y realizando cambios en la mensajería del ecosistema a fin de extender a los pagos con tarjeta de crédito a través de códigos  QR  la  interoperabilidad  que  hoy  existe  en  los  pagos  con  transferencia  (PCT).  La arquitectura de los esquemas presentados se desarrolló y consensuó en las “mesas técnicas” de trabajo realizadas en el marco de la CIMPRA. 

Este  documento  incorpora  adecuaciones  en  el  punto  2.4.  Notificación  de  pago  (desde adquirente/agregador  hacia  billetera)  aclarando  que  los  estados  de  REFUNDED  como  el CHARGED BACK no ocurren al momento del pago, ya que se originan con posterioridad en plazos que están fuera del alcance del Boletín 535 y sus complementarios. 

**QR interoperable, versión pago con tarjeta de crédito Índice** 

1. **Solicitud de Pago Pasiva** 
   1. **Flujo de pago con utilización de “API resolve”** 
   1. **Flujo de pago sin utilización de “API resolve”** 
   1. **Flujo de devolución de pago** 
1. **Resolución del pago** 
1. **Lectura de QR (desde billetera hacia adquirente/agregador)** 
1. **QR con utilización de “API resolve” (boletín CIMPRA 525)** 
   1. **Monto abierto** 
1. **QR sin utilización de “API resolve” (boletín CIMPRA 530)** 
2. **Obtención de planes (desde billetera hacia adquirente/agregador)** 
   1. **PATCH plans** 
2. **Envío intención de pago (desde billetera hacia adquirente/agregador)** 
   1. **POST Payment**  
2. **Notificación de pago (desde adquirente/agregador hacia billetera)** 

**2.4.1.  POST payments/notify** 

3. **Anexos** 
1. **Pospago** 
   1. **GET Payment by ID** 
   1. **GET Order by ID** 
1. **Acceptor token** 
1. **Códigos de respuesta** 
1. **Estándar de tipos definidos** 
1. **Estados de operación** 
1. **Otras consideraciones** 
1. **Swagger de APIs** 

**1. Solicitud de Pago Pasiva**  

1. **Flujo de pago con utilización de API resolve** 

El  esquema del flujo  de  pago  involucra  tres  roles: billetera digital  interoperable  (“billetera”), adquirente/agregador y el comercio. La mayor cantidad de interacciones se da entre el backen[d ](#_page3_x77.00_y748.92)[^1] de la billetera con el del adquirente/agregador.  

El propósito de este esquema es proporcionar un flujo de pagos eficiente, que involucra consultas a la Interfaz Estandarizada de Pagos (IEP): identificar al adquirente/agregador, tipo de monto (abierto  o  cerrado)  y  si  el  adquirente/agregador  permite  operar  con  tarjeta  de  crédito.  Las consultas al backend del adquirente/agregador tienen que ver con planes de pago, monto de cuota y notificación de pago realizado, si fue aceptado o rechazado.

![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.002.jpeg)

1. Ante la lectura de un código QR (estático o dinámico), la billetera enviará el contenido completo del QR a su backend para ser procesado. 
1. El backend de la billetera analizará el QR bajo estándar EMVCo Merchant Presented Code, y determinará el adquirente/agregador mediante la búsqueda de qué Merchant Account Information contiene el QR (identificadores 26 a 49, ya que las demás posiciones ya se encuentran reservadas por el propio standard EMVCo o por la resolución del BCRA). Cada template contiene bajo el id 00 como identificador universal de fácil identificación un dominio invertido del adquirente/agregador (para mayor detalle consultar boletín CIMPRA 525). 
1. Una  vez  procesado  el  QR  en  el  backend  de  la  billetera  y  confirmado  que  el adquirente/agregador utiliza la IEP, se realizará un GET de información a la IEP para la obtención de mayor información para poder iniciar el pago. 

a.  Respuesta de la IEP con información. La respuesta del monto desde la IEP puede 

ser a monto cerrado o abierto, incluyendo información acerca de si el comercio permite operar o no con tarjetas de crédito. 

4. En caso de aceptar tarjetas de crédito, la billetera solicita los planes con los cuales el adquirente/agregador permite operar. El adquirente/agregador devuelve los planes, junto a los montos de las cuotas calculados por el adquirente/agregador.  
4. Se produce la confirmación del usuario del pago a realizar (selección de plan de pagos), ejecutando desde la billetera la iniciación del pago. Este paso contempla una respuesta síncrona de aprobación o rechazo, o una respuesta de error.  

a.  La billetera podrá consultar el estado de ese pago en el caso de que no haya 

recibido respuesta o información. 

2. **Flujo de pago sin utilización de API resolve** 

El esquema del flujo de pago involucra tres roles: billetera digital, adquirente/agregador y el comercio. La mayor cantidad de interacciones se da entre el backend de la billetera con el del adquirente/agregador.  

El propósito de este esquema es proporcionar un flujo de pagos eficiente, que al escanear el código  QR  la  billetera  pueda:  identificar  al  adquirente/agregador;  informarse  de  que  el adquirente/agregador no tiene IEP, monto de la transacción y si el adquirente/agregador permite operar con tarjeta de crédito. Las consultas al backend del adquirente/agregador tienen que ver con planes de pago, monto de cuota y notificación de monto de cuota y notificación de pago realizado, si fue aceptado o rechazado. 

![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.003.jpeg)

1. El backend de la billetera analizará el QR bajo estándar EMVCo Merchant Presented Code, y determinará si el adquirente/agregador utiliza IEP (entre campos 43 a 46, subcampo 99 por boletín CIMPRA 530) 
1. Si no utiliza IEP el campo aparecerá en 00, y deberá enviar la consulta de planes al backend del adquirente/agregador, con la información del monto, moneda, informado en los campos del QR.  
1. El adquirente/agregador devuelve los planes disponibles, junto a los montos de las cuotas calculados. 
1. Se  produce  la  confirmación  del  usuario  y  una  posterior  iniciación  del  pago,  con  la respuesta síncrona de aprobación o rechazo. Se puede consultar el estado de ese pago por el caso de que no haya recibido respuesta o información. 

**Estructura de los campos del código QR (sin IEP)** 

00 02 01  01 02 12  

43 55  

00 10 com.fiserv   URL 

96 02 11   PAYMENT\_METHODS\_ALLOWED \* 97 01 1                                  MAX\_BINS\_ALLOWED\* 

98 11 30692264785  CUIT COELSA 

99 02 00   NO IEP 

50 15 00 11 27260448213   CUIT 

51 26 00 22 0000068000000002222956     CVU 

52 04 5812    MCC 

53 03 032   CURRENCY 

54 06 100.00  AMOUNT 

58 02 AR  COUNTRY CODE 

59 13 POSNET.SA   MERCHANT NAME 

60 12 VILLA GESELL                 MERCHANT CITY 

61 05 07165  POSTAL CODE 

62 59  

01 12 000100000001  BILL NUMBER 

05 21 123456789012345678901  ID QR 

07 08 26044821     DEVICE POS 

08 02 00   PURCHASE 

80 50  

`      `00 10 com.fiserv             URL 

`      `01 05 02.01                     VERSION 

`      `02 02 01                                      INSTALLMENTS (Ej. **Cuota Simple** =7 --> 12 cuotas)       03 12 220830154145             DATETIME (YYMMDDHHMMSS UTC -3) 

`      `04 01 1                        DEVICE (POS, SISTEMA PROPIO, CLOVER, etc.) 

63 04 0e06 

\+ información PAYMENT\_METHODS\_ALLOWED  43 96 02 

11   PCT, CARD (1 habilitado PCT  y  1  habilitado Tarjeta)  10  PCT, CARD (1 habilitado PCT - 0 no habilitado Tarjeta) 01  PCT, CARD (0 no habilitado PCT - 1 habilitado Tarjeta) 

MAX\_BINS\_ALLOWED   43 97     

Si en PAYMENT\_METHODS\_ALLOWED está habilitado tarjeta enviar los bines según las siguientes restricciones: 

01 1    Aceptación de 1 bin. En caso de que se informe 1 bin deberá 

seleccionar el medio de pago que cursará en la operación: PCT o tarjeta. Si selecciona tarjeta, el bin enviado en el mensaje de Plans,  deberá  ser  el  bin  correspondiente  a  la  tarjeta  que  se enviará en la mensajería de Payment.  Y no se podrá cambiar a PCT. 

02 99**   Aceptación de N bines**.**  

3. **Flujo de devolución de pago** 

El  único  actor  que  puede  iniciar  el  flujo  de  devoluciones  es  el  comercio,  pidiéndole  al adquirente/agregador la devolución de un pago; luego este, será quien notifique a la billetera la devolución. 

![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.004.jpeg)

1. El comercio inicia una solicitud de devolución de pago adquirente/agregador. 
1. El backend del adquirente/agregador recibe la solicitud y procesa la devolución.  
1. Procesada la devolución, el backend del adquirente/agregador responde al comercio con el “payment\_ID” 
1. Notifica a la billetera el “payment\_ID” de la devolución. 
1. La billetera podrá consultar el estado de ese pago realizando un “GET Paymen[t” ](#_page7_x77.00_y758.92)[^2] con el “payment\_id” que recibió previamente. 
2. **Resolución del pago**
1. **Lectura de QR (desde billetera hacia adquirente/agregador)** 

Al momento de la lectura del QR, la billetera deberá identificar si el adquirente/agregador utiliza IEP o no, tal cual se define en el boletín CIMPRA 530. 

1. **QR con utilización de “API resolve” (boletín CIMPRA 525)** 

Una vez interpretado el QR, y detectado que utiliza IEP, la billetera realizará un “GET resolve” para solicitar la información necesaria para poder posteriormente cursar el pago. Estos QR podrán ser de monto abierto o monto cerrado.** 

**Ejemplo de QR monto abierto:** 

{ 

`  `"status": "open\_amount", 

`  `"administrator": { 

"identification\_number": "string", 

"name": "string"

`  `}, 

`  `"collector": { 

"account": "string", 

"identification\_number": "string", 

"mcc": "string", 

"name": "string", 

"postal\_code": "C1064AAD" 

`  `}, 

`  `"order": { 

"id": "string", 

"items": { 

"currency\_id": "ARS", 

"description": "Producto 1", 

"quantity": 1, 

"title": "string", 

"unit\_price": 10000.99 

}, 

"total\_amount": 10000.99 

`  `}, 

`  `"payment\_methods\_allowed": [ //los payment\_methods\_allowed deben ir explicitos porque son los que permiten a la billetera saber que metodos de pago ofrecer//

{ 

"id": "CARD", 

"restrictions": { 

"min\_amount\_allowed": 10000.99, 

"max\_amount\_allowed": 10000.99, 

"max\_bins\_allowed": "1235678" 

} 

} 

`  `], 

`  `"additional\_info": {}

2. **QR sin utilización de “API resolve” (boletín CIMPRA 530)** 

Una vez interpretado el QR, y detectado que no utiliza IEP, la billetera deberá avanzar al siguiente paso de obtención de planes de pago. 

Tener en cuenta que para los QR sin utilización de API resolve las billeteras deberán completar los siguientes datos con la información del QR DATA. 

**Ejemplo:** 

order\_id: "123456789012345678901", // FISERV QRdata: 62. 05 QR ID  value: "100.0", // FISERV QRdata: 53 CURRENCY 

currency: "ARS"// FISERV QRdata: 54 AMOUNT 

2. **Obtención de planes (desde billetera hacia adquirente/agregador)** 

La billetera solicita los planes con los cuales el adquirente/agregador permite operar. 

**2.2.1.  PATCH plans** 

Endpoint: /orders/{order\_id}/plans Sentido: Billetera >> Adquirente/agregador Timeout: 30000 ms 

Header 

x-request-id: guid 

authorization: bearer (access\_token) **Request:**   

{ 

`  `"bins": [

{ 

"original\_bin": "1235678", "issuer\_id": "string", "type": "CREDIT", "brand\_id": "string"

} 

`  `], 

`  `"amount": { 

"value": 10000.99, "currency": "ARS" 

`  `}, 

`  `"additional\_info": {}

} 

**Response Status 200:**  

{ 

`  `“supported\_bins”: [

{ 

“brand\_id”: “string”, “type”: “CREDIT”, “original\_bins”: [

“1235678” 

], 

“plans”: [

{ 

“id”: “string”,

“type”: “AHORA”,

“description”: “string”,

“installments”: 1, 

“total\_amount”: {

“value”: 10000.99, 

“currency”: “ARS”

}, 

“installment\_amount”: {

“value”: 10000.99, 

“currency”: “ARS”

}, 

“financial\_info”: {

“total\_fianancial\_cost”: “80.00”, 

“nominal\_annual\_rate”: “80.00” 

}, 

“required\_fields”: [ //el adquirente/agregador deberá informar a la billetera los campos obligatorios a través de required\_fields

“payment\_method.card.holder.name” 

] 

} 

] 

} 

`  `], 

`  `“unsupported\_bins”: [

“1235678” 

`  `], 

`  `“additional\_info”: {}

} 

**Response Status > 4XX / 5XX:** { 

`  `"code": "string", 

`  `"message": "string", 

`  `"additionalProp1": {} } 

3. **Envío intención de pago (desde billetera hacia adquirente/agregador)** 

El usuario ya ha seleccionado la forma de pago, dando lugar a la iniciación del pago por parte de la billetera. 

**2.3.1.  POST Payment**  

Endpoint: /orders/{order\_id}/payments Sentido: Billetera >> Adquirente/agregador 

Observación:  el  objeto  payment\_method  debe  proponerse  con  una  estructura  que  quede escalable para la posterior etapa de tokenización con las marcas. El flujo de pago de adquirente/ agregador contempla 3 escenarios: 

- card\_data: Envío de datos planos de la tarjeta para cuando la billetera o el habilitador tecnológico que se utilice sea “PCI compliance“ 
- acceptor\_token: implica enviar un token custom de cada adquirente/ agregador, utilizado y disponible solo para ese pago en ese momento. 
- brand\_token: utiliza el token de marca 

Timeout: 15000 ms 

Header 

x-request-id: guid x-idempotency-key: guid authorization: bearer (access\_token) 

**Request:** 

{ 

`  `"plan": { 

"id": "string", 

"type": "AHORA", 

"description": "string", 

"installments": 1, 

"total\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"installment\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

} 

`  `}, 

`  `"payment\_method": { 

"card": { 

"holder": { 

"name": "string", 

"identification\_type": "DNI", 

"identification\_number": "string"

}, 

// solo una de las 3 opciones "card\_data", "acceptor\_token", "brand\_token” // "card\_data": { 

"number": "string", //numero de tarjeta

"security\_code": "string", //cvv 

"expiration\_month": 2, //mes de vencimiento

"expiration\_year": 9999, //año de vencimiento

"entry\_mode": "manual" //cof, manual

}, 

"acceptor\_token": "string", //en caso de requerir un acceptor token, se deberá obtener el mismo a través de la api /payments/tokens

"brand\_token": { 

"original\_bin": "string", //bin del pan original

"original\_last4": "string", //últimos 4 del pan original

"token": "string", 

"cryptogram": "string", 

"security\_indicator": "string", //Indicador de nivel de seguridad ej:

"par": "string" //Hash 

"expiration\_month": 2, //mes de vencimiento

"expiration\_year": 9999, //año de vencimiento

"token\_requestor\_id": "string"

}, 

}, 

"wallet": { 

"name": "string", //nombre de la wallet que inicia el pago ej: Mercado pago, Cuenta DNI, Santander, Naranja X, Galicia, MODO, etc.

"provider": "string", //nombre del proveedor de infraestructura que permite hacer ese pago como por ej el valor es MODO

"brand\_wallet\_id": "string", //id específico que la marca de tarjeta asigna a cada billetera, aplica especialmente para VISA y MASTER

"user": { 

"email": "user@example.com", 

"phone": "string", 

"device\_id": "string", 

"identification\_type": "DNI", 

"identification\_number": "string"

} 

} 

`  `}, 

`  `"additional\_info": {}

} 

**Response Status 200:** 

{ 

`  `"payment\_id": "string", 

`  `"order\_id": "string", 

`  `"status": "APPROVED", 

`  `"status\_code": "APPROVED", 

`  `"amount": { 

"value": 10000.99, 

"currency": "ARS" 

`  `}, 

`  `"authorized\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

`  `}, 

`  `"plan": { 

"id": "string", 

"type": "AHORA", 

"description": "string", "installments": 1, "total\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"installment\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"financial\_info": { 

"total\_fianancial\_cost": "80.00", "nominal\_annual\_rate": "80.00"

} 

`  `}, 

`  `"card": { 

"original\_bin": "1235678", "original\_last4": "string", 

"type": "string", 

"brand\_id": "string", 

"issuer\_id": "string", 

"holder": { 

"name": "string", "identification\_type": "DNI", "identification\_number": "string"

} 

`  `}, 

`  `"wallet": { 

"name": "string", 

"provider": "string", 

"brand\_wallet\_id": "string", 

"user": { 

"email": "user@example.com", 

"phone": "string", 

"device\_id": "string", "identification\_type": "DNI", "identification\_number": "string"

} 

`  `}, 

`  `"authorization\_code": "string",//ej: “123456”   "refunds": [

{ 

"amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"created\_at": "2023-10-26T17:26:03.953Z"

} 

`  `], 

`  `"created\_at": "2023-10-26T17:26:03.953Z", 

`  `"updated\_at": "2023-10-26T17:26:03.953Z", 

`  `"additional\_info": {}

} 

` `Boletín CIMPRA 541 | 14 

**Response Status > 4XX / 5XX**  { 

`  `"code": "string", 

`  `"message": "string", 

`  `"additionalProp1": {} } 

**Generic error response:**  

` `Boletín CIMPRA 541 |  

4. **Notificación de pago (desde adquirente/agregador hacia billetera)** 

Una  vez  que  el  adquirente/agregador  confirme  que  la  operación  concluyó,  el adquirente/agregador comunicará el resultado de la intención de pago a la billetera.  

El adquirente/agregador notificará siempre a la billetera los siguientes estados: APPROVED, REJECTED, REFUNDED, CHARGED  BACK;  el único  estado  que  no  será  notificado  sin  previa consulta por parte de la billetera será el de PROCESSING. 

Nota: Tanto el estado REFUNDED como el CHARGED BACK no ocurren al momento del pago. El estado REFUNDED puede originarse en cualquier momento desde que ocurre el pago hasta los 90 días posteriores al mismo. El estado CHARGED BACK puede ocurrir desde el momento del pago hasta los 60 días posteriores. Los valores mencionados se agregan a modo informativo y su definición y/o modificación no está en el alcance de este boletín. 

**2.4.1.  POST payments/notify** 

**Request:** 

{ 

`  `"payment\_id": "string", 

`  `"domain\_reverse": "string" } 

**Response Status 204:** OK** 

` `Boletín CIMPRA 541 | 15 

**Response Status > 4XX / 5XX**  { 

`  `"code": "string", 

`  `"message": "string", 

`  `"additionalProp1": {} } 

**Generic error response:**  

` `Boletín CIMPRA 541 |  

3. **Anexos** 
1. **Pospago** 
1. **GET Payment by ID** 

La  billetera  podrá  realizar  una  consulta  de  un  pago  a  través  del  GET  Payment  by  ID  para asegurarse el estado final de la operación, dado que puede haber contracargos, cancelaciones o devoluciones de pago. 

**Response Status 2XX:** 

{ 

`  `"payment\_id": "string", 

`  `"order\_id": "string", 

`  `"status": "PROCESSING", 

`  `"status\_code": "PROCESSING", 

`  `"amount": { 

"value": 10000.99, 

"currency": "ARS" 

`  `}, 

`  `"authorized\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

`  `}, 

`  `"plan": { 

"id": "string", 

"type": "AHORA", 

"description": "string", "installments": 1, "total\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"installment\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"financial\_info": { 

"total\_fianancial\_cost": "80.00", "nominal\_annual\_rate": "80.00"

} 

`  `}, 

`  `"card": { 

"original\_bin": "1235678", "original\_last4": "string", "type": "string", 

"brand\_id": "string", 

"issuer\_id": "string", 

"holder": { 

"name": "string", "identification\_type": "DNI", 

"identification\_number": "string"

} 

`  `}, 

`  `"wallet": { 

"name": "string", 

"provider": "string", 

"brand\_wallet\_id": "string", 

"user": { 

"email": "user@example.com", 

"phone": "string", 

"device\_id": "string", "identification\_type": "DNI", "identification\_number": "string"

} 

`  `}, 

`  `"authorization\_code": "string", 

`  `"refunds": [

{ 

"amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"created\_at": "2023-10-26T19:50:04.954Z"

} 

`  `], 

`  `"created\_at": "2023-10-26T19:50:04.954Z",   "updated\_at": "2023-10-26T19:50:04.954Z",   "additional\_info": {}

} 

` `Boletín CIMPRA 541 | 17 

**Response Status > 4XX / 5XX**  { 

`  `"code": "string", 

`  `"message": "string", 

`  `"additionalProp1": {} } 

**Generic error response:**  

` `Boletín CIMPRA 541 |  

2. **GET Order by ID** 

Como opción adicional, la billetera podrá consultar todos los pagos relacionados a una orden a través del endpoint GET/orders/{order\_id}/payments. 

**Response Status 2XX:** 

[ 

`  `{ 

"payment\_id": "string", "order\_id": "string", "status": "PROCESSING", "status\_code": "PROCESSING", "amount": { 

"value": 10000.99, "currency": "ARS" 

}, 

"authorized\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"plan": { 

"id": "string", 

"type": "AHORA", 

"description": "string", 

"installments": 1, 

"total\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"installment\_amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"financial\_info": { 

"total\_fianancial\_cost": "80.00", "nominal\_annual\_rate": "80.00"

} 

}, 

"card": { 

"original\_bin": "1235678", "original\_last4": "string", 

"type": "string", 

"brand\_id": "string", 

"issuer\_id": "string", 

"holder": { 

"name": "string", "identification\_type": "DNI", "identification\_number": "string"

} 

}, 

"wallet": { 

"name": "string", 

"provider": "string", 

"brand\_wallet\_id": "string", 

"user": { 

"email": "user@example.com", 

"phone": "string", 

"device\_id": "string", "identification\_type": "DNI", "identification\_number": "string"

} 

}, 

"authorization\_code": "string", "refunds": [

{ 

"amount": { 

"value": 10000.99, 

"currency": "ARS" 

}, 

"created\_at": "2023-10-26T19:56:13.912Z"

} 

], 

"created\_at": "2023-10-26T19:56:13.912Z", "updated\_at": "2023-10-26T19:56:13.912Z", "additional\_info": {}

`  `} ] 

` `Boletín CIMPRA 541 | 18 

**Response Status > 4XX / 5XX**  { 

`  `"code": "string", 

`  `"message": "string", 

`  `"additionalProp1": {} } 

**Generic error response:**  

` `Boletín CIMPRA 541 | 19 

2. **Acceptor token** 

Cuando se deba utilizar el token de adquirente/agregador, la billetera lo obtendrá a través de la siguiente API/payments/tokens. 

Para realizar el post, se podrá enviar tanto card\_data como brand\_token (solo se deberá enviar uno de los dos). 

**Request:** 

{ 

`  `"card": { 

"holder": { 

"name": "string", 

"identification\_type": "DNI", "identification\_number": "string"

}, //se envía una sola de las siguientes opciones "card\_data": { 

"number": "string", 

"security\_code": "string", "expiration\_month": 12, 

"expiration\_year": 9999, 

"entry\_mode": "MANUAL" 

}, 

"brand\_token": { 

"original\_bin": "string", //bin del pan original

"original\_last4": "string", //ultimos 4 del pan original

"token": "string", 

"cryptogram": "string", 

"security\_indicator": "string", //Indicador de nivel de seguridad ej: "par": "string" //Hash 

"expiration\_month": 2, //mes de vencimiento

"expiration\_year": 9999, //año de vencimiento

"token\_requestor\_id": "string"

} 

`  `} } 

**Response Status 2XX:** 

{ 

`  `"token": "string", 

`  `"created\_at": "2023-11-09T19:36:03.456Z", 

`  `"expiry\_at": "2023-11-09T19:36:03.456Z"

} 

**Response Status > 4XX / 5XX   Generic error response:**  { 

`  `"code": "string", 

`  `"message": "string", 

`  `"additionalProp1": {} 

} 

3. **Códigos de respuesta** 

Las respuestas deben responderse con códigos HTTP apropiados para todas las API. 



|**Código** |**Respuesta**  |**Descripción** |
| - | - | - |
|200 |Ok |La petición fue exitosa. También se utiliza para peticiones a colecciones vacías |
|400 |Bad Request |Existe algún problema en el formato del request |
|401 |Unauthorized |Problemas de autenticación: el request no contiene credenciales o las mismas son inválidas |
|403 |Forbidden |El request es correcto, pero el usuario no tiene permisos suficientes |
|404 |Not Found |No se encontraron {entidad} |
|500 |Internal Server Error |Algún error inesperado sucedió en el servidor |
|502 |Bad Gateway |El servicio que provee la API está ok, pero un servicio del cual depende respondió con un error interno |
|503 |Service Unavailable |El servidor no está disponible debido a algún problema operativo propio del servicio, |



|||como una sobrecarga |
| :- | :- | - |
|504 |Gateway timeout |El servicio que provee la API está OK, pero un servicio del cual depende no está contestando dentro del tiempo esperado |

4. **Estándar de tipos definidos** 



<table><tr><th colspan="1"><b>Atributo</b> </th><th colspan="1"><b>enum</b> </th></tr>
<tr><td colspan="1" valign="top">brand_id </td><td colspan="1">VISA, MASTER, AMEX, CABAL, MAESTRO, NARANJA, SUCREDITO, ARGENCARD, DISCOVER, NATIVA </td></tr>
<tr><td colspan="1">card.type </td><td colspan="1">DEBIT, CREDIT, PREPAID </td></tr>
<tr><td colspan="1">identification_type </td><td colspan="1">DNI, CUIT, CUIL </td></tr>
<tr><td colspan="1">entry_mode </td><td colspan="1">MANUAL, COF </td></tr>
<tr><td colspan="1" valign="top">plans.type </td><td colspan="1"><p>CUOTA SIMPLE: Plan financiado por el emisor con tasa fijada por el gobierno. EMISOR: Plan financiado por el emisor. ADQUIRENTE: Plan financiado por el adquirente. </p><p>COMERCIO: Plan financiado por el comercio. ALIMENTAR: xx </p><p>ZETA: Plan financiado por Naranja. </p></td></tr>
<tr><td colspan="1">currency </td><td colspan="1">ARS </td></tr>
<tr><td colspan="1" rowspan="6" valign="top">issuer_id </td><td colspan="1" valign="bottom"><p>Detallar tomando como base los listados de entidades financieras del BCRA y el registro de empresas no financieras emisoras de tarjetas de crédito o compra. </p><p>[https://www.bcra.gob.ar/SistemasFinanciero](https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Sistema_financiero_nomina_de_entidades.asp?bco=AAA00&tipo=1)</p></td></tr>
<tr><td colspan="1" valign="top">[sYdePagos/Sistema_financiero_nomina_de_](https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Sistema_financiero_nomina_de_entidades.asp?bco=AAA00&tipo=1)</td></tr>
<tr><td colspan="1" valign="top">[entidades.asp?bco=AAA00&tipo=1 ](https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Sistema_financiero_nomina_de_entidades.asp?bco=AAA00&tipo=1) </td></tr>
<tr><td colspan="1" valign="bottom">[https://www.bcra.gob.ar/SistemasFinanciero](https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Emisoras_tarjetas_credito_compra.asp)</td></tr>
<tr><td colspan="1" valign="top">[sYdePagos/Emisoras_tarjetas_credito_comp](https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Emisoras_tarjetas_credito_compra.asp)</td></tr>
<tr><td colspan="1" valign="top">[ra.asp ](https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Emisoras_tarjetas_credito_compra.asp) </td></tr>
</table>

5. **Estados de operación** 

Se contemplan los siguientes estados para las operaciones: 

- PROCESSING 
- APPROVED 
- REJECTED 
- REFUNDED 
- CHARGED BACK 

Se define la siguiente máquina de estados: 

![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.005.jpeg)

@startuml 

[\*] -down-> PROCESSING PROCESSING -down-> APPROVED APPROVED -down-> REFUNDED APPROVED -> CHARGED\_BACK REFUNDED -> CHARGED\_BACK REFUNDED -left-> APPROVED PROCESSING -> REJECTED REJECTED -> [\*] 

REFUNDED -> [\*] CHARGED\_BACK -down-> [\*] @enduml 

En el status code se obtendrá mayor información del estado informado, pudiendo ser: 



|**Status\_code** |**Descripción** |
| - | - |
|processing ||


|approved ||
| - | :- |
|rejected\_insufficient\_funds  |fondos insuficientes, excede límites  |
|rejected\_call\_for\_auth ||
|rejected\_declined  |motivos 05, otros como fraude  |
|rejected\_invalid\_card  |Cualquier error relacionado a la tarjeta: número incorrecto, tarjeta denunciada, validaciones de tokenización, tarjeta expirada, cvv incorrecto  |
|rejected\_invalid\_transaction  |cuotas no soportadas, bin incorrecto, tipo de tarjeta incorrecto (debit, credit)  |
|rejected\_invalid\_merchant  |comercio no configurado correctamente  |
|rejected\_system\_error  |emisor fuera de línea, adquirente fuera de línea  |
|rejected\_invalid\_order  |orden cancelada, la orden ya fue pagada  |
|rejected\_max\_attempts  |máximo de intentos alcanzados  |
|refunded  |devolución y anulación  |
|refunded\_partially ||
|charged\_back ||
6. **Otras consideraciones** 
- Zona horaria: UTC-3 
- Autenticación  para  servicios  expuestos  entre  adquirente/agregador  y  billetera:  los servicios que sean consumidos *“frontend to backend”* deberán contar con credenciales de autenticación distintas a las utilizadas para consumir servicios *“backend to backend”.* Las credenciales de autenticación podrán tener un tiempo de vida y un mecanismo para su rotación. 
- Servicios  de  “notify”  a  las  billeteras:  al  momento  de  realizar  la  integración  entre adquirentes/agregadores y billeteras, se solicitará a esta última informar la URL en la cual deberá recibir las notificaciones por parte del adquirente/agregador. 
7. **Swagger de APIs** 

**openapi: 3.0.3![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.006.png)**

**info:** 

`  `**title: Interfaz Estandarizada de Pagos - QR** 

`  `**version: 0.0.1**

`  `**description: This contract define the APIs exposed by acquirers to any wallet operating QR payment solutions inside Argentina.**

`  `**contact:![ref1]**

**name: Tomás Mingo Suarez**

**email: tomas.mingo@globant.com**

**externalDocs:**

`  `**description: find IEP Specification here**

`  `**url: https://www.bcra.gob.ar/pdfs/sistemasfinancierosydepagos/SNP3525.pdf**

**paths:** 

`  `**/resolve:**

**get:** 

**operationId: getResolve**

**summary: Receives the qr string and return the standarized answer for T3 , TD and TC** 

**tags:** 

- **Resolve** 

**parameters:**

- **$ref: '#/components/parameters/QRData'**
- **$ref: '#/components/parameters/AccessToken'**
- **$ref: '#/components/parameters/TraceId'**

**responses:**

**'2XX':**

**$ref: '#/components/responses/Resolve200'**

**'4XX':**

**$ref: '#/components/responses/GenericError'**

**'5XX':**

**$ref: '#/components/responses/GenericError'**

`  `**/orders/{order\_id}/plans:**

**patch:** 

**operationId: patchPlansByOrderId**

**summary: Receives the order\_Id, amount (if the order has the 'status' field with 'open\_amount' value) and bins and return all available plans for the defined order.**

**tags:** 

- **Orders** 

**parameters:**

- **$ref: '#/components/parameters/TraceId'**
- **$ref: '#/components/parameters/OrderId'**

**requestBody:**

**required: true**

**content:![ref1]**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/PlansRequest' responses:**

**'2XX':**

**$ref: '#/components/responses/Plans200'**

**'4XX':**

**$ref: '#/components/responses/GenericError' '5XX':**

**$ref: '#/components/responses/GenericError'**

`  `**/orders/{order\_id}/payments:**

**post:** 

**operationId: postPaymentsByOrderId**

**summary: Execute a payment from the given order\_id tags:** 

- **Orders** 

**parameters:**

- **$ref: '#/components/parameters/IdempotencyId'**
- **$ref: '#/components/parameters/TraceId'**
- **$ref: '#/components/parameters/OrderId'**

**requestBody:**

**required: true**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/PaymentsRequest' responses:**

**'2XX':**

**$ref: '#/components/responses/Payments200' '4XX':**

**$ref: '#/components/responses/GenericError' '5XX':**

**$ref: '#/components/responses/GenericError'**

**get:** 

**operationId: getPaymentsByOrderId**

**summary: Get a payment from the given order\_id**

**tags: ![ref1]**

- **Orders** 

**parameters:**

- **$ref: '#/components/parameters/TraceId'**
- **$ref: '#/components/parameters/OrderId'**

**responses:**

**'2XX':**

**$ref: '#/components/responses/GetPayments200'**

**'4XX':**

**$ref: '#/components/responses/GenericError'**

**'5XX':**

**$ref: '#/components/responses/GenericError'**

`  `**/payments/{payment\_id}:**

**get:** 

**operationId: getPaymentsByPaymentId**

**tags:** 

- **Payments** 

**parameters:**

- **$ref: '#/components/parameters/TraceId'**
- **$ref: '#/components/parameters/PaymentId'**

**responses:**

**'2XX':**

**$ref: '#/components/responses/Payments200'**

**'4XX':**

**$ref: '#/components/responses/GenericError'**

**'5XX':**

**$ref: '#/components/responses/GenericError'**

`  `**/payments/notify:**

**post:** 

**operationId: postPaymentsNotifyUpdate**

**summary: Endpoint exposed by any wallet provider to receive a notification that something changes in a particular payment**

**tags:** 

- **Wallet** 

**requestBody:**

**required: true**

**content:**

**application/json:![ref1]**

**schema:** 

**$ref: '#/components/schemas/PaymentNotifyUpdate' responses:**

**'204':**

**description: OK**

**'4XX':**

**$ref: '#/components/responses/GenericError'**

**'5XX':**

**$ref: '#/components/responses/GenericError'**

`  `**/payments/tokens:**

**post:** 

**operationId: postPaymentsTokenization**

**summary: Generate an Acceptor Token to be used in a payment. tags:** 

- **Payments** 

**parameters:**

- **$ref: '#/components/parameters/TraceId'**

**requestBody:**

**required: true**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/TokenRequest'**

**responses:**

**'2XX':**

**$ref: '#/components/responses/Tokenization200'**

**'4XX':**

**$ref: '#/components/responses/GenericError'**

**'5XX':**

**$ref: '#/components/responses/GenericError'**

**components:   schemas:** 

- **Requests** 

**TokenRequest:![ref1]**

**type: object**

**properties:**

**card:** 

**type: object**

**properties:**

**holder:**

**$ref: '#/components/schemas/CardHolder' oneOf:** 

- **$ref: '#/components/schemas/CardData'**
- **$ref: '#/components/schemas/BrandToken'**

**PlansRequest:**

**type: object**

**properties:**

**bins:** 

**type: array**

**items:** 

**$ref: '#/components/schemas/Bin'**

**amount:** 

**$ref: '#/components/schemas/Amount' additional\_info:**

**type: object**

**required:**

- **order\_id**
- **bins** 
- **amount** 

**PaymentsRequest:**

**type: object**

**properties:**

**plan:** 

**$ref: '#/components/schemas/Plan' payment\_method:** 

**$ref: '#/components/schemas/PaymentMethod' additional\_info:**

**type: object**

**required:**

- **plan** 
- **payment\_method ![ref1]**

**PaymentNotifyUpdate: type: object properties: payment\_id:**

**type: string**

**domain\_reverse:**

**type: string**

**description: Same field exposed in the QR required:**

- **payment\_id** 
- **domain\_reverse**
- **Responses** 

**Resolve:** 

**type: object**

**properties:**

**status:**

**type: string**

**description: Define if the amount is open to be added by the wallet or not enum:** 

- **"open\_amount"**
- **"closed\_amount"**

**administrator:**

**$ref: '#/components/schemas/Administrator'**

**collector:**

**$ref: '#/components/schemas/Collector'**

**order:** 

**$ref: '#/components/schemas/Order'**

**payment\_methods\_allowed:**

**type: array**

**items:** 

**$ref: '#/components/schemas/PaymentMethodIEP'**

**additional\_info:**

**type: object**

**required:**

- **status ![ref1]**
- **administrator**
- **collector**
- **order** 
- **payment\_methods\_allowed**

**PlansResponse:**

**type: object**

**properties:**

**supported\_bins:**

**type: array**

**items:** 

**$ref: '#/components/schemas/SupportedBin' unsupported\_bins:**

**type: array**

**items:** 

**$ref: '#/components/schemas/BinField' additional\_info:**

**type: object**

**GetPaymentsResponse:**

**type: array**

**items:** 

**$ref: '#/components/schemas/PaymentsResponse' PaymentsResponse:** 

**type: object**

**properties:**

**payment\_id:**

**type: string**

**order\_id:**

**type: string**

**status:**

**$ref: '#/components/schemas/PaymentStatus' status\_code:**

**$ref: '#/components/schemas/PaymentStatusCode' amount:** 

**$ref: '#/components/schemas/Amount' authorized\_amount:**

**$ref: '#/components/schemas/Amount'**

**plan: ![ref1]**

**$ref: '#/components/schemas/Plan' card:** 

**$ref: '#/components/schemas/Card' wallet:**

**$ref: '#/components/schemas/Wallet' authorization\_code:**

**type: string**

**refunds:**

**type: array**

**items:** 

**$ref: '#/components/schemas/Refund' created\_at:**

**type: string**

**format: date-time** 

**updated\_at:**

**type: string**

**format: date-time** 

**additional\_info:**

**type: object**

**required:**

- **payment\_id** 
- **order\_id**
- **status** 
- **status\_code**
- **amount** 
- **authorized\_amount**
- **plan** 
- **created\_at**
- **updated\_at**

**TokenizationResponse:**

**type: object**

**properties:**

**token:** 

**type: string**

**created\_at:**

**type: string**

**format: date-time ![ref1]expiry\_at:**

**type: string**

**format: date-time Error:**

**type: object additionalProperties: true properties:**

**code:** 

**type: string message:** 

**type: string required:**

- **code** 
- **message** 
- **Objects**

**Administrator:**

**type: object**

**properties: identification\_number:**

**type: string**

**name:** 

**type: string required:**

- **identification\_number**
- **name** 

**Collector:**

**type: object**

**properties:**

**account:** 

**type: string identification\_number:**

**type: string**

**mcc:** 

**type: string**

**name: ![ref1]**

**type: string**

**postal\_code:**

**type: string**

**example: "C1064AAD"**

**required:**

- **account** 
- **identification\_number**
- **mcc** 
- **name** 
- **postal\_code**

**Order:** 

**type: object**

**properties:**

**id:** 

**type: string**

**items:** 

**$ref: '#/components/schemas/Items' total\_amount:**

**$ref: '#/components/schemas/AmountField' required:**

- **id** 
- **total\_amount**

**Items:** 

**type: object**

**properties:**

**currency\_id:**

**$ref: '#/components/schemas/CurrencyField' description:**

**type: string**

**example: "Producto 1"**

**quantity:**

**type: integer**

**example: 1**

**title:**

**type: string**

**unit\_price:**

**$ref: '#/components/schemas/AmountField' required:![ref1]**

- **currency\_id**
- **description**
- **title**
- **unit\_price**

**PaymentMethodIEP:** 

**type: object**

**properties:**

**id:** 

**type: string**

**enum:** 

- **"CARD"** 
- **"TRANSFER"** 

**restrictions:**

**type: object**

**properties:**

**min\_amount\_allowed:**

**$ref: '#/components/schemas/AmountField' max\_amount\_allowed:**

**$ref: '#/components/schemas/AmountField' max\_bins\_allowed:**

**$ref: '#/components/schemas/BinField' required:**

- **id** 

**Bin:** 

**type: object**

**properties:**

**original\_bin:**

**$ref: '#/components/schemas/BinField' issuer\_id:**

**$ref: '#/components/schemas/IssuerIdField' type:** 

**$ref: '#/components/schemas/CardTypeField' brand\_id:**

**$ref: '#/components/schemas/BrandIdField' required:**

- **original\_bin![ref1]**
- **issuer\_id**
- **type** 
- **brand\_id** 

**Amount:** 

**type: object**

**properties:**

**value:** 

**$ref: '#/components/schemas/AmountField' currency:**

**$ref: '#/components/schemas/CurrencyField' required:**

- **amount** 

**SupportedBin:**

**type: object** 

**properties:**

**brand\_id:**

**$ref: '#/components/schemas/BrandIdField'**

**type:** 

**$ref: '#/components/schemas/CardTypeField' original\_bins:**

**type: array**

**items:** 

**$ref: '#/components/schemas/BinField'**

**plans:** 

**type: array**

**items:** 

**allOf:**

- **$ref: '#/components/schemas/Plan'**
- **$ref: '#/components/schemas/RequiredFields'**

**required:**

- **brand\_id** 
- **type** 
- **original\_bin**
- **plans** 

**RequiredFields:**

**type: object**

**properties:![ref1]**

**required\_fields:**

**type: array**

**description: Any optional field from the POST /payments endpoint could be added on this list. If a field is reported here, that field will be considered mandatory in the context of this particular order. In the case of nested fields all objects will be included using a dot "." as separator**

**items:** 

**type: string**

**example: "payment\_method.card.holder.name"**

**readOnly: true**

**Plan:** 

**type: object**

**properties:**

**id:** 

**type: string**

**type:** 

**$ref: '#/components/schemas/PlanTypeField'**

**description:**

**type: string**

**description: A description ready to be rendered in the frontend installments:**

**type: integer**

**example: 1**

**total\_amount:**

**$ref: '#/components/schemas/Amount'**

**installment\_amount:**

**$ref: '#/components/schemas/Amount'**

**financial\_info:**

**$ref: '#/components/schemas/FinancialInfo'**

**required:**

- **id** 
- **type** 
- **description**
- **installments**
- **total\_amount**
- **installment\_amount**

` `Boletín CIMPRA 541 | 36 

**FinancialInfo:![ref1]**

**type: object**

**properties:**

**total\_financial\_cost:**

**$ref: '#/components/schemas/PercentageField' nominal\_annual\_rate:**

**$ref: '#/components/schemas/PercentageField' readOnly: true**

**PaymentMethod:** 

**type: object**

**properties:**

**card:** 

**type: object**

**properties:**

**holder:**

**$ref: '#/components/schemas/CardHolder' oneOf:** 

- **$ref: '#/components/schemas/AcceptorToken'**
- **$ref: '#/components/schemas/CardData'**
- **$ref: '#/components/schemas/BrandToken'**

**wallet:**

**$ref: '#/components/schemas/Wallet'**

**required:**

- **card** 
- **wallet** 

**CardData:** 

**type: object**

**properties:**

**card\_data:**

**type: object**

**properties:**

**number:** 

**type: string**

**security\_code:**

**type: string**

**expiration\_month:**

**type: integer**

**minimum: 1 ![ref1]**

**maximum: 12** 

**expiration\_year:**

**type: integer**

**minimum: 1000** 

**maximum: 9999** 

**entry\_mode:**

**type: string**

**enum:** 

- **MANUAL** 
- **COF** 

**required:**

- **number** 
- **security\_code**
- **expiration\_month**
- **expiration\_year**
- **entry\_mode** 

**required:**

- **card\_data** 

**BrandToken:** 

**type: object**

**properties:**

**brand\_token:**

**type: object**

**properties:**

**original\_bin:**

**$ref: '#/components/schemas/BinField'**

**original\_last4:**

**type: string**

**token:** 

**type: string**

**cryptogram:**

**type: string**

**security\_indicator:**

**type: string**

**description: Security level indicator according to brand standards (for example ECI for VISA)**

**par: ![ref1]**

**type: string**

**expiration\_year:**

**type: integer**

**minimum: 1000** 

**maximum: 9999** 

**expiration\_month:**

**type: integer**

**minimum: 1** 

**maximum: 12** 

**token\_requestor\_id:**

**description: Token Requestor ID**

**type: string**

**required:**

- **original\_bin**
- **original\_last4**
- **token** 
- **cryptogram** 
- **security\_indicator**

**required:**

- **brand\_token**

**AcceptorToken:**

**type: object**

**properties:**

**acceptor\_token:**

**type: string**

**required:**

- **acceptor\_token**

**CardHolder:**

**type: object**

**properties:**

**name:** 

**type: string**

**identification\_type:**

**$ref: '#/components/schemas/IdentificationTypeField' identification\_number:**

**type: string**

**Card: ![ref1]**

**type: object**

**properties:**

**original\_bin:**

**$ref: '#/components/schemas/BinField' original\_last4:**

**type: string**

**type:** 

**type: string**

**brand\_id:**

**$ref: '#/components/schemas/BrandIdField' issuer\_id:**

**$ref: '#/components/schemas/IssuerIdField' holder:**

**$ref: '#/components/schemas/CardHolder' required:**

- **original\_bin**
- **original\_last4**

**Wallet:**

**type: object**

**properties:**

**name:** 

**type: string**

**provider:**

**type: string**

**brand\_wallet\_id:**

**type: string**

**user:** 

**$ref: '#/components/schemas/User' required:**

- **name** 
- **provider**

**User:** 

**type: object**

**properties:**

**email:** 

**type: string**

**format: email![ref1]**

**phone:** 

**type: string**

**device\_id:**

**type: string**

**identification\_type:**

**$ref: '#/components/schemas/IdentificationTypeField' identification\_number:**

**type: string**

**Refund:** 

**type: object**

**properties:**

**amount:** 

**$ref: '#/components/schemas/Amount'**

**created\_at:**

**type: string**

**format: date-time** 

**required:**

- **amount** 
- **created\_at**
- **Fields**

**PaymentStatus:**

**type: string**

**enum:** 

- **PROCESSING** 
- **APPROVED** 
- **REJECTED** 
- **REFUNDED** 
- **CHARGED\_BACK** 

**PaymentStatusCode:**

**type: string**

**enum:** 

- **PROCESSING** 
- **APPROVED** 
- **REJECTED\_INSUFFICIENT\_FUNDS** 
- **REJECTED\_CALL\_FOR\_AUTH ![ref1]**
- **REJECTED\_DECLINED** 
- **REJECTED\_INVALID\_CARD** 
- **REJECTED\_INVALID\_TRANSACTION** 
- **REJECTED\_INVALID\_MERCHANT** 
- **REJECTED\_SYSTEM\_ERROR** 
- **REJECTED\_INVALID\_ORDER** 
- **REJECTED\_MAX\_ATTEMPTS** 
- **REFUNDED** 
- **REFUNDED\_PARTIALLY** 
- **CHARGED\_BACK** 

**IdentificationTypeField:**

**type: string**

**enum:** 

- **DNI** 
- **CUIL** 

**PercentageField:**

**type: string**

**format: percentage**

**example: "80.00"**

**PlanTypeField:**

**type: string**

**example: "AHORA"**

**description: This value must be taken from the standardized list of plan types. The list itself is part of the IEP documentation.**

**AmountField:**

**type: number**

**format: amount**

**example: 10000.99**

**CurrencyField:**

**type: string**

**format: currencyCodeISO4217**

**default: "ARS"**

**example: "ARS"**

**BinField:**

**type: string**

**format: bins**

**example: "1235678"![ref1]**

**IssuerIdField:**

**type: string**

**description: This value must be taken from the standardized list of card issuers ids. The list itself is part of the IEP documentation and is based on the BCRA's 'Nomina de Entidades'(https://www.bcra.gob.ar/SistemasFinancierosYdePagos/Sistema\_financiero\_nomi na\_de\_entidades.asp?bco=AAA00&tipo=1)**

**CardTypeField:**

**type: string**

**enum:** 

- **CREDIT** 
- **DEBIT** 
- **PREPAID** 

**BrandIdField:**

**type: string**

**description: This value must be taken from the standardized list of card brands ids. The list itself is part of the IEP documentation.**

`  `**parameters:**

**QRData:** 

**name: data** 

**in: query**

**schema:** 

**type: string**

**AccessToken:** 

**name: access\_token**

**in: query**

**schema:** 

**type: string**

**deprecated: true**

**IdempotencyId:**

**name: idempotency-id** 

**in: header**

**description: Id for idempotency recognition. Used to recognize several attemps of the same requests. In a retry logic the same request-id must be sent.**

**schema:** 

**type: string**

**TraceId:![ref1]**

**name: trace-id** 

**in: header**

**description: Id for tracing purpose. Used to trace the request through every internal system. Must be different on every transaction or attempt.**

**schema:** 

**type: string**

**OrderId:**

**name: order\_id**

**in: path**

**description: Unambiguous order identifier**

**schema:** 

**type: string**

**required: true**

**PaymentId:** 

**name: payment\_id**

**in: path**

**description: Unambiguous payment identifier**

**schema:** 

**type: string**

**required: true**

`  `**responses:**

**Resolve200:**

**description: Successful response**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/Resolve'**

**Plans200:** 

**description: Successful response**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/PlansResponse'**

**Payments200:** 

**description: Successful response**

**content:**

**application/json:![](Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.008.png)**

**schema:** 

**$ref: '#/components/schemas/PaymentsResponse' GetPayments200:** 

**description: Successful response**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/GetPaymentsResponse' Tokenization200:**

**description: Successful response**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/TokenizationResponse' GenericError:**

**description: Generic error response**

**content:**

**application/json:**

**schema:** 

**$ref: '#/components/schemas/Error'**

`  `**securitySchemes:**

**bearerAuth:**

**type: http**

**scheme: bearer security:**

- **bearerAuth: []**
` `Boletín CIMPRA 541 | 45 

[^1]: <a name="_page3_x77.00_y748.92"></a> Ejecuta funciones que no son visibles para el usuario final, pero de importancia para el buen funcionamiento y desarrollo del proceso. 
[^2]: <a name="_page7_x77.00_y758.92"></a> Método utilizado para consultar respecto a un pago específico. 
[ref1]: Aspose.Words.8a01fb4d-c53c-4762-ac1c-1f7d17067077.007.png
