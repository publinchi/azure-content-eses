<properties
    pageTitle="Incorporación del conector de Usuarios de Office 365 a PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de Usuarios de Office 365 con parámetros de la API de REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introducción al conector de Usuarios de Office 365

Conéctese a Usuarios de Office 365 para obtener perfiles, buscar usuarios, etc. El conector de usuarios de Office 365 puede usarse desde:

- Aplicaciones lógicas 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

&nbsp;

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.


Con Usuarios de Office 365, puede:

- Compilar el flujo de su negocio en función de los datos que obtiene de Usuarios de Office 365. 
- Usar acciones que obtienen informes directos, obtienen el perfil de usuario de un administrador, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, obtenga informes directos de la persona y, luego, tome esta información y actualice una base de datos de SQL Azure. 
- Agregar el conector de Usuarios de Office 365 a PowerApps Enterprise. Así, los usuarios pueden utilizar este conector en sus aplicaciones. 

Si desea obtener información sobre cómo agregar un conector a PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector de Usuarios de Office 365 tiene las siguientes acciones disponibles: No hay desencadenadores.

| Desencadenadores | Acciones|
| --- | --- |
|None | <ul><li>Obtener administrador</li><li>Obtener mi perfil</li><li>Obtener subordinados directos</li><li>Obtener perfil de usuario</li><li>Buscar usuarios</li></ul>|

Todos los conectores admiten datos en formato JSON y XML.


## Creación de una conexión a Usuarios de Office 365

Al agregar este conector a las aplicaciones lógicas, debe iniciar sesión en su cuenta de Usuarios de Office 365 y permitir que estas se conecten a su cuenta.

>[AZURE.INCLUDE [Pasos para crear una conexión a Usuarios de Office 365](../../includes/connectors-create-api-office365users.md)]

Después de crear la conexión, especifique las propiedades de Usuarios de Office 365, como el identificador de usuario. En la **referencia de la API de REST** de este tema, se describen estas propiedades.

>[AZURE.TIP] Esta misma conexión de Usuarios de Office 365 se puede usar en otras aplicaciones lógicas.


## Referencia de API de REST de Usuarios de Office 365
Se aplica a la versión: 1.0.

### Obtener mi perfil 
Recupera el perfil del usuario actual. ```GET: /users/me```

No hay parámetros para esta llamada.

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener perfil de usuario 
Recupera un perfil de usuario específico. ```GET: /users/{userId}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener administrador 
Recupera el perfil de usuario del administrador del usuario especificado. ```GET: /users/{userId}/manager```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|



### Obtener informes directos 
Obtiene subordinados directos. ```GET: /users/{userId}/directReports```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|



### Buscar usuarios 
Recupera los resultados de búsqueda de perfiles de usuario. ```GET: /users```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|searchTerm|cadena|no|query|Ninguna|Cadena de búsqueda (se aplica a: nombre para mostrar, nombre dado, apellidos, correo, sobrenombre de correo y nombre principal del usuario)|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|



## Definiciones de objeto

#### Usuario: clase de modelo de usuario.

|Nombre de propiedad | Tipo de datos |Obligatorio
|---|---|---|
|DisplayName|cadena|no|
|GivenName|cadena|no|
|Surname|cadena|no|
|Mail|cadena|no|
|MailNickname|cadena|no|
|TelephoneNumber|cadena|no|
|AccountEnabled|boolean|no|
|Id|cadena|yes
|UserPrincipalName|cadena|no|
|Departamento|cadena|no|
|JobTitle|cadena|no|
|mobilePhone|cadena|no|


## Pasos siguientes

[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

Volver a la [lista de API](apis-list.md)

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0525_2016-->