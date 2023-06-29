  # MiiD Gateway Runt Api

![N|Solid](https://miid.bio/wp-content/uploads/2021/06/MiID_ico-100x100.png)

**MiiD Gateway Runt** Es un Api que provee la funcionalidad de validacion de un rostro capturado contra la base de datos del RUNT 

## Operaciones

 - [Authorization - Login](#ApiGatewayAutorization)
 - [Match Face Multipart](#ApiGatewayRuntMatchFaceMultipart)
 - [Match Face Base64](#ApiGatewayRuntMatchFaceBase64) 
 - [Respuestas del Servicio](#ApiGatewayRuntMatchFaceResponse) 


**MiiD Gateway**  is a commercial Service, to use it contact [info@bytte.com.co](mailto:info@bytte.com.co) for instructions on how to obtain the required resources and access
**MiiD** is a trademark registered by Bytte S.A.S 
 
## Operaciones:
## <a name="ApiGatewayAutorization"></a>Api Gateway Autorization
Para el uso de esta API, Debe tener credenciales asignadas por el equipo de MiiD previamente,

Para el uso de esta API, el cliente debe tener:

    * Usuario 
    * Clave asignada
    * Identificador de Cliente Empresarial
    * IP/URL del Servicio Runt 
    * Acceso desde las Ip(s) publicas donde se origina el llamado (Permisos de Firewall)

Url - Login User - POST
https://servicesdev.miid.bio/Login/LoginUser/

```json
{
    "emailAddress": "MyUseraccess@mymail.bio",
    "password": "MyPassword"
}
```
Si el usuario y Password son Validos, el Api retornará un Json como el siguiente;

```json
{
    "tokenType": "Bearer",
    "accessToken": "eyJ0eXAiiJKV1QiLCJhbGc....",
    "successOperation": true
}
```

Se deberá usar el campo "accessToken", para hacer uso del servicio Runt

**El token tiene caducidad de 20 minutos, y puede ser usado en multiples llamados**

---
## <a name="ApiGatewayRuntMatchFaceMultipart"></a> Match Face Multipart
**Mas Eficiente**

Esta Operacion Permite hacer Match contra en Runt, enviando la imagen binaria (Multipart) 

Url - **POST**
> https://IP_SERVICE/Runt/MatchFaceMultipart/

Body Type
* **Form/Data**

Headers
* **Authorization** : Tipo Bearer, se obtiene del servicio **LoginUser**

Form Variables
* **Key** : Archivo Binario con la imagen en formato PNG/JPG
* **EnterpriseClientId** : Identificador provisto por MiiD al cliente Empresarial para identificarlo
* **DocumentType** : Tipo de Documento del Cliente a Validar en Runt
* **DocumentNumber** : Numero de Documento del Cliente a Validar en Runt

Ejemplo de Llamado usando CURL - POST
```console
curl --location 'https://IP_SERVICE/Runt/MatchFaceMultipart/' \
--header 'Authorization: Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6Ilg1....' \
--form 'key=@"imgSelfie.jpg"' \
--form 'documenttype="1"' \
--form 'documentnumber="42889508"' \
--form 'enterpriseclientid="10424"'
```
---
## <a name="ApiGatewayRuntMatchFaceBase64"></a> Match Face Imagen Base64
Esta Operacion Permite hacer Match contra en Runt, enviando el request con la imagen en base64 JSON

Url - **POST**
> https://IP_SERVICE/Runt/MatchFaceBase64/

Body Type
* **application/json**

Example Json
```sh
--header Authorization": Bearer eyJ0eXAi......."
```
Body: 
```json
{
  "enterpriseClientId": 10424,
  "documentType": "1",
  "documentNumber": "802082232",  
  "selfieImage": "/9j/4AAQSkZJRgABA"  --> ImageBase64
}
```

Json Variables
* **SelfieImage** : Archivo Codificado en base64 con la imagen de la Selfie en formato PNG/JPG
* **EnterpriseClientId** : Identificador provisto por MiiD al cliente para identificarlo
* **DocumentType** : Tipo de Documento del Cliente Empresarial a Validar en Runt
* **DocumentNumber** : Numero de Documento del Cliente a Validar en Runt

Ejemplo de Llamado usando CURL - POST
```console
curl --location 'https://IP_SERVICE/Runt/MatchFaceBase64/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer eyJhbGciOiJSUzI1...' \
--data '{
  "documentType": "1",
  "documentNumber": "80208223",  
  "selfieImage": "/9j/4AAQSkZJRgABAQAAS...",  
  "enterpriseClientId": 1
}'
``` 
---
## <a name="ApiGatewayRuntMatchFaceResponse"></a> Respuestas del Servicio

> **Respuesta 1: El Usuario No existe en el Runt**
Response:
```json
{
    "utn": "649de118f347e12015450e6c",
    "code": "TRANS000",
    "message": {
        "clientId": "I-BYTTE",
        "id": "802082213",
        "transId": "649de118f347e12015450e6c",
        "hit": false,
        "existeRunt": false,
        "descripcionRespuesta": "El ciudadano no está registrado en el sistema RUNT",
        "fechaTransaccion": "2023-06-29T19:52:58.245+0000"
    },
    "score": 0
}
```

> **Respuesta 2: El Usuario existe en el Runt y el proceso retorno HIT Positivo, Campo hit** : true
```json
{
    "utn": "649ddb2e61af454bfc71496c",
    "code": "TRANS000",
    "message": {
        "clientId": "I-BYTTE",
        "id": "80208223",
        "transId": "649ddb2e61af454bfc71496c",
        "hit": true,
        "existeRunt": true,
        "descripcionRespuesta": "AUTORIZADO",
        "fechaTransaccion": "2023-06-29T19:27:45.066+0000",
        "primerNombre": "ALLAN",
        "segundoNombre": "YEISSON",
        "primerApellido": "DIAZ",
        "segundoApellido": "BALLESTEROS",
        "nut": "1def837e-3464-435b-9931-32ca34739bde"
    },
    "score": 0
}
```

> **Respuesta 3: El Usuario existe en el Runt y el proceso retorno HIT Negativo, Campo hit** : false
```json
{
    "utn": "649de36298a98384d775e1c2",
    "code": "TRANS000",
    "message": {
        "clientId": "I-BYTTE",
        "id": "80208223",
        "transId": "649de36298a98384d775e1c2",
        "hit": false,
        "existeRunt": true,
        "descripcionRespuesta": "NO AUTORIZADO",
        "fechaTransaccion": "2023-06-29T20:02:44.845+0000"
    },
    "score": 0
}
```
---

**Descripcion de los campos de respuesta**:
* **descripcionRespuesta** : Respuesta de la autenticación.
* **nut** : Código de solicitud de la consulta biométrica
* **primerNombre** : Primer Nombre de la persona a autenticar
* **segundoNombre** : Segundo Nombre de la persona a autenticar.
* **primerApellido** : Primer Apellido de la persona a autenticar.
* **segundoApellido** : Segundo Apellido de la persona a autenticar.
* **fechaTransaccion** : Fecha de la transacción en RUNT
* **hit** : booleano - Indicador de la transacción en RUNT
 
** Datos cuando existe un error:
* **code** : Código de resultado de la operación
* **error** : Mensaje del error de la operación

** Codigos de Error:
* **HRUNT000** : Error general servicio RUNT
* **HRUNT004** : Error en matcher
* **HRUNT005** : Error con descripción asociada.
* **TRANS000** : TRANSACTION_OK
