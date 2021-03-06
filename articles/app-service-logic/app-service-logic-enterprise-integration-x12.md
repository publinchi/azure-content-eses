<properties 
	pageTitle="Información general sobre X12 y Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Aprenda a utilizar los contratos X12 para crear Aplicaciones lógicas." 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Integración de empresas con X12 

## Creación de un contrato X12 
Para poder intercambiar mensajes X12, hay que crear un contrato X12 y almacenarlo en la cuenta de integración. Los siguientes pasos lo guían a lo largo del proceso de creación de un contrato X12.

### Requisitos para poder comenzar
- Debe tener una [cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) definida en su suscripción de Azure
- Debe contar con, como mínimo, dos [partners](./app-service-logic-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración

>[AZURE.NOTE]Al crear un contrato, el contenido del archivo de contrato debe coincidir con el tipo de contrato.


Cuando haya [creado una cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) y [agregado los partners](./app-service-logic-enterprise-integration-partners.md), podrá generar un contrato X12 siguiendo estos pasos:

### En la página principal del Portal de Azure

Después de iniciar sesión en el [Portal de Azure](http://portal.azure.com "Portal de Azure"):
1. En el menú de la izquierda, seleccione **Examinar**.

>[AZURE.TIP]Si no ve el vínculo **Examinar**, puede que primero tenga que expandir el menú. Para ello, haga clic en el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba *integration* en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados.![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)
3. En la hoja **Integration Accounts** (Cuentas de integración) que se abre, seleccione la cuenta de integración en la que va a crear el contrato. Si no ve ninguna lista de cuentas de integración, [cree una antes](./app-service-logic-enterprise-integration-accounts.md "Todo sobre las cuentas de integración"). ![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)
4.  Seleccione el icono **Agreements** (Contratos). Si no lo ve, agréguelo antes. ![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)
5. Seleccione el botón **Agregar** de la hoja Agreements (Contratos) que se abre. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Escriba un **nombre** para el contrato; después, seleccione **Agreement type** (Tipo de contrato), **Host Partner** (Partner anfitrión), **Host Identity** (Identidad anfitriona), **Guest Partner** (Partner invitado), **Guest Identity** (Identidad invitada) en la hoja Agreements (Contratos) que se abre. ![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)
7. Una vez que haya establecido las propiedades de configuración de recepción, seleccione el botón **Aceptar**. Continuemos:
8. Seleccione **Receive Settings** (Configuración de recepción) para configurar cómo se controlan los mensajes que se reciben por medio de este contrato.
9. El control de la configuración de recepción se divide en las siguientes secciones: Identifiers (Identificadores), Acknowledgement (Confirmación), Schemas (Esquemas), Envelopes (Sobres), Control Numbers (Números de control), Validations (Validaciones) e Internal Settings (Configuración interna). Configure estas propiedades en función del contrato con el asociado con el que intercambiará mensajes. A continuación se ofrece una vista de estos controles. Configúrelos en función de cómo desee que este contrato identifique y controle los mensajes entrantes: ![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)
10. Seleccione el botón **Aceptar** para guardar la configuración.

### Identifiers (Identificadores)

|Propiedad|Descripción |
|---|---|
|ISA1 (Authorization Qualifier) (ISA1 (calificador de autorización))|Seleccione el valor del calificador de autorización en la lista desplegable.|
|ISA2|Opcional. Escriba el valor de la información de autorización. Si el valor especificado en ISA1 es distinto de 00, escriba como mínimo un carácter alfanumérico y, como máximo, diez.|
|ISA3 (Security Qualifier) (ISA3 (calificador de seguridad))|Seleccione el valor del calificador de seguridad en la lista desplegable.|
|ISA4|Opcional. Escriba el valor de la información de seguridad. Si el valor especificado en ISA3 es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez.|

### Agradecimientos 

|Propiedad|Descripción |
|----|----|
|TA1 expected (TA1 esperada)|Seleccione esta casilla para devolver una confirmación técnica (TA1) al remitente del intercambio. Estas confirmaciones se envían al remitente del intercambio según la configuración de envío del contrato.|
|FA expected (FA esperada)|Seleccione esta casilla para devolver una confirmación funcional (FA) al remitente del intercambio. A continuación, seleccione si desea la confirmación 997 o la 999, en función de las versiones del esquema con las que esté trabajando. Estas confirmaciones se envían al remitente del intercambio según la configuración de envío del contrato.|
|Include AK2/IK2 Loop (Incluir bucle AK2/IK2)|Active esta casilla para habilitar la generación de bucles AK2 en confirmaciones funcionales para conjuntos de transacciones aceptados. Nota: Esta casilla se habilita solo si activó la casilla FA expected (FA esperada).|

### Esquemas

Elija un esquema para cada tipo de transacción (ST1) y aplicación remitente (GS2). La canalización de recepción desensambla el mensaje entrante haciendo coincidir los valores de ST1 y GS2 del mensaje entrante con los valores que establezca aquí y el esquema del mensaje entrante con el esquema que configure aquí.

|Propiedad|Descripción |
|----|----|
|Versión|Seleccione la versión de X12.|
|Transaction Type (ST01) (Tipo de transacción (ST01))|Seleccione el tipo de transacción.|
|Sender Application (GS02) (Aplicación remitente (GS02))|Seleccione la aplicación remitente.|
|Esquema|Seleccione el archivo de esquema que desee enviarnos. Los archivos de esquema se encuentran en la cuenta de integración.|

### Envelopes (Sobres)

|Propiedad|Descripción |
|----|----|
|ISA11 Usage (Uso de ISA11)|Utilice este campo para especificar el separador de un conjunto de transacciones:</br></br>Seleccione Standard Identifier (Identificador estándar) para utilizar la notación decimal "." en lugar de la notación decimal del documento entrante en la canalización de recepción de EDI.</br></br>Seleccione Repetition Separator (Separador de repeticiones) para especificar el separador de las repeticiones de un elemento de datos simple o de una estructura de datos. Por ejemplo, se suele utilizar (^) como separador de repeticiones. En los esquemas HIPAA, solo se puede usar (^).|

### Control Numbers (Números de control)

|Propiedad|Descripción |
|----|----|
|Disallow Interchange Control Number duplicates (No permitir duplicados del número de control de intercambio)|Active esta opción para bloquear los intercambios duplicados. Si la selecciona, el portal Servicios de BizTalk comprueba que el número de control de intercambio (ISA13) del intercambio recibido no coincida con el número de control de intercambio. Si se detecta una coincidencia, la canalización de recepción no procesa el intercambio.<br/>Si optó por no permitir números de control de intercambio duplicados, puede especificar el número de días que se realizará la comprobación indicando el valor pertinente en el cuadro de comprobación de duplicados de ISA13 cada x días.|
|Disallow Group control number duplicates (No permitir duplicados del número de control de grupo)|Active esta opción para bloquear los intercambios con números de control de grupo duplicados.|
|Disallow Transaction set control number duplicates (No permitir duplicados del número de control del conjunto de transacciones)|Active esta opción para bloquear los intercambios con números de control de conjuntos de transacciones duplicados.|

### Validations (Validaciones)

|Propiedad|Descripción |
|----|----|
|Message Type (Tipo de mensaje)|Tipo de mensaje de EDI como, por ejemplo, 890-Purchase Order (850: Pedido de compra) o 999-Implementation Acknowledgement (999: Confirmación de la implementación).|
|EDI Validation (Validación de EDI)|Realiza una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales.|
|Extended Validation (Validación extendida)|Si el tipo de datos no es EDI, lo que se valida es el requisito de elemento de datos, así como las repeticiones permitidas, las enumeraciones y la validación de la longitud del elemento de datos (mín./máx.).|
|Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales)|Se conservan todos los espacios y ceros adicionales que se encuentren a la izquierda o a la derecha. No se eliminan.|
|Trailing Separator Policy (Directiva de separador final)|Genera separadores finales en el intercambio recibido. Entre las opciones se encuentran NotAllowed (No permitidos), Optional (Opcionales) y Mandatory (Obligatorios).|

### Internal Settings (Configuración interna)

|Propiedad|Descripción |
|----|----|
|Convert implied decimal format Nn to base 10 numeric value (Convertir formato decimal implícito Nn a valor numérico de base 10)|Convierte un número EDI especificado con el formato Nn en un valor numérico de base 10 en el XML intermedio del portal Servicios de BizTalk.|
|Create empty XML tags if trailing separators are allowed (Crear etiquetas XML vacías si se permiten separadores finales)|Seleccione esta casilla para que el remitente del intercambio incluya etiquetas XML vacías para los separadores finales.|
|Inbound batching processing (Procesamiento por lotes entrantes)|Split Interchange as transaction sets - suspend transaction sets on error (Dividir intercambio como conjuntos de transacciones: suspender conjuntos de transacciones en caso de error): Suspende todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado al conjunto de transacciones. Con esta opción, si uno o más conjuntos de transacciones del intercambio no superan la validación, Servicios de BizTalk suspenderá solo esos conjuntos de transacciones. </br></br>Split Interchange as transaction sets - suspend interchange on error (Dividir intercambio como conjuntos de transacciones: suspender intercambio en caso de error): Redistribuye todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado. Con esta opción, si uno o más conjuntos de transacciones del intercambio no superan la validación, Servicios de BizTalk suspende el intercambio por completo.</br></br>Preserve Interchange - suspend transaction sets on error (Conservar intercambio: suspender conjuntos de transacciones en caso de error): Deja el intercambio intacto, ya que crea un documento XML para todo el intercambio por lotes. Con esta opción, si uno o más conjuntos de transacciones del intercambio no superan la validación, Servicios de BizTalk suspende el intercambio por completo.</br></br>Preserve Interchange - suspend interchange on error (Conservar intercambio: suspender intercambio en caso de error): Deja el intercambio intacto, ya que crea un documento XML para todo el intercambio por lotes. Con esta opción, si uno o más conjuntos de transacciones del intercambio no superan la validación, Servicios de BizTalk suspenderá el intercambio por completo.</br></br>|

El contrato está preparado para controlar los mensajes entrantes que cumplen el esquema seleccionado.

Para configurar las opciones que controlan los mensajes que se envían a los asociados, siga estos pasos:
11. Seleccione **Send Settings** (Configuración de envío) para configurar cómo se controlarán los mensajes enviados por medio de este contrato.

El control de la configuración de envío se divide en las siguientes secciones: Identifiers (Identificadores), Acknowledgement (Confirmación), Schemas (Esquemas), Envelopes (Sobres), Control Numbers (Números de control), Character Sets and Separators (Juegos de caracteres y separadores) y Validation (Validación).

A continuación, se muestra una vista de estos controles. Realice las selecciones en función de cómo desee controlar los mensajes que envía a los asociados por medio de este contrato: ![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)
12. Seleccione el botón **Aceptar** para guardar la configuración.

### Identifiers (Identificadores)
|Propiedad|Descripción |
|----|----|
|ISA1 (Authorization Qualifier) (ISA1 (calificador de autorización))|Seleccione el valor del calificador de autorización en la lista desplegable.|
|ISA2|Escriba el valor de la información de autorización. Si el valor especificado es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez.|
|ISA3 (Security Qualifier) (ISA3 (calificador de seguridad))|Seleccione el valor del calificador de seguridad en la lista desplegable.|
|ISA4|Escriba el valor de la información de seguridad. Si el valor especificado es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez en el cuadro de texto del valor (ISA4).|

### Acknowledgement (Confirmación)
|Propiedad|Descripción |
|----|----|
|TA1 expected (TA1 esperada)|Seleccione esta casilla para devolver una confirmación técnica (TA1) al remitente del intercambio. Esta configuración especifica que el asociado del host que envía el mensaje solicita una confirmación del asociado invitado en el contrato. El asociado del host espera estas confirmaciones en función de la configuración de recepción del contrato.|
|FA expected (FA esperada)|Seleccione esta casilla para devolver una confirmación funcional (FA) al remitente del intercambio y seleccione si desea la confirmación 997 o la 999, en función de las versiones del esquema con el que esté trabajando. El asociado del host espera estas confirmaciones en función de la configuración de recepción del contrato.|
|FA Version (Versión de FA)|Seleccione la versión de FA|

### Esquemas
|Propiedad|Descripción |
|----|----|
|Versión|Seleccione la versión de X12.|
|Transaction Type (ST01) (Tipo de transacción (ST01))|Seleccione el tipo de transacción.|
|SCHEMA (ESQUEMA)|Seleccione el esquema que se utilizará. Los esquemas se encuentran en la cuenta de integración. Para obtener acceso a los esquemas, antes debe vincular su cuenta de integración con su aplicación lógica.|

### Envelopes (Sobres)
|Propiedad|Descripción |
|----|----|
|ISA11 Usage (Uso de ISA11)|Utilice este campo para especificar el separador de un conjunto de transacciones:</br></br>Seleccione Standard Identifier (Identificador estándar) para utilizar la notación decimal "." en lugar de la notación decimal del documento entrante en la canalización de recepción de EDI.</br></br>Seleccione Repetition Separator (Separador de repeticiones) para especificar el separador de las repeticiones de un elemento de datos simple o de una estructura de datos. Por ejemplo, se suele utilizar (^) como separador de repeticiones. En los esquemas HIPAA, solo se puede usar (^).</br>|
|Repetition separator (Separador de repeticiones)|Escriba el separador de repeticiones.|
|Control version number (ISA12) (Número de versión de control (ISA12))|Seleccione la versión de X12 estándar que usa el portar Servicios de BizTalk para generar un intercambio saliente.|
|Usage Indicator (ISA15) (Indicador de uso (ISA15))|Especifique si el contexto de un intercambio es información (I), los datos de producción (P) o los datos de pruebas (T). La canalización de recepción de EDI promociona esta propiedad en el contexto.|
|Esquema|Puede especificar la forma en que el portal Servicios de BizTalk debe generar los segmentos GS y ST para un intercambio X12 codificado que envía a la canalización de envío.</br></br>Puede asociar los valores de los elementos de datos GS1, GS2, GS3, GS4, GS5, GS7 y GS8 con los valores de los elementos de datos de versión y de tipo de transacción. Si el portal Servicios de BizTalk determina que un mensaje XML tiene los valores establecidos para los elementos de versión y de tipo de transacción en una fila de la cuadrícula, a continuación, rellena los elementos de datos GS1, GS2, GS3, GS4, GS5, GS7 y GS8 en el sobre del intercambio saliente con los valores de la misma fila de la cuadrícula. Los valores los elementos de tipo de transacción y de versión deben ser únicos.</br></br>Opcional. En GS1, seleccione un valor para el código funcional en la lista desplegable.</br></br>Obligatorio. En GS2, especifique un valor alfanumérico para el remitente de la aplicación con dos caracteres como mínimo y quince como máximo.</br></br>Obligatorio. En GS3, especifique un valor alfanumérico para el receptor de la aplicación con dos caracteres como mínimo y quince como máximo.</br></br>Opcional. En GS4, seleccione CCYYMMDD (SSAAMMDD) o YYMMDD (AAMMDD).</br></br>Opcional. En GS5, seleccione HHMM, HHMMSS o HHMMSSdd.</br></br>Opcional. En GS7, seleccione un valor para la agencia responsable de la lista desplegable.</br></br>Opcional. En GS8, especifique un valor alfanumérico para el documento identificado con un carácter como mínimo y doce como máximo.</br></br>**Nota**: Estos son los valores que especifica el portal Servicios de BizTalk en los campos GS del intercambio que está creando si los elementos de tipo de transacción y de versión de la misma fila coinciden con los que están asociados con el intercambio.|

### Control Numbers (Números de control)
|Propiedad|Descripción |
|----|----|
|Interchange Control Number (ISA13) (Número de control de intercambio (ISA13))|Obligatorio. Escriba un intervalo de valores para el número de control de intercambio utilizado por el portal de Servicios de BizTalk para generar un intercambio saliente. Escriba un valor numérico: 1 como mínimo y 999999999 como máximo.|
|Group Control Number (GS06) (Número de control de grupo (GS06))|Obligatorio. Escriba el intervalo de números que debe usar el portal Servicios de BizTalk para el número de control de grupo. Escriba un valor numérico con un carácter como mínimo y nueve como máximo.|
|Transaction Set Control Number (ST02) (Número de control de conjunto de transacciones (ST02))|En este campo, especifique un intervalo de valores numéricos para los campos obligatorios centrales y valores alfanuméricos para el prefijo y sufijo opcionales. La longitud máxima de los cuatro campos es de nueve caracteres.|
|Prefijo|Para designar el intervalo de números de control de conjunto de transacciones utilizados en una confirmación, escriba los valores en los campos de número de control de ACK (ST02). Escriba un valor numérico en los dos campos centrales y un valor alfanumérico (si lo desea) en los campos Prefijo y Sufijo. Los campos centrales son obligatorios y contienen los valores mínimo y máximo del número de control; el prefijo y el sufijo son opcionales. La longitud máxima de los tres campos es de nueve caracteres.|
|Sufijo|Para designar el intervalo de números de control de conjunto de transacciones utilizados en una confirmación, escriba los valores en los campos de número de control de ACK (ST02). Escriba un valor numérico en los dos campos centrales y un valor alfanumérico (si lo desea) en los campos Prefijo y Sufijo. Los campos centrales son obligatorios y contienen los valores mínimo y máximo del número de control; el prefijo y el sufijo son opcionales. La longitud máxima de los tres campos es de nueve caracteres.|

### Character Sets and Separators (Juegos de caracteres y separadores)
Además del juego de caracteres, puede especificar un conjunto de delimitadores diferente para cada tipo de mensaje. Si no se especifica un juego de caracteres en un esquema de mensaje concreto, se usará el juego de caracteres predeterminado.

|Propiedad|Descripción |
|----|----|
|Character Set to be used (Juego de caracteres que se va a usar)|Seleccione el juego de caracteres de X12 para validar las propiedades que especifique para el contrato.</br></br>**Nota**: El portal Servicios de BizTalk solo usa esta configuración para validar los valores especificados para las propiedades relacionadas del contrato. La canalización de recepción o envío omite esta propiedad del juego de caracteres al procesar el tiempo de ejecución.|
|Esquema|Seleccione el símbolo (+) y seleccione un esquema de la lista desplegable. En el esquema seleccionado, seleccione el conjunto de separadores que se debe usar:</br></br>Component element separator (Separador de elementos de componente): Especifique un único carácter para separar los elementos de datos compuestos.</br></br>Data Element Separator (Separador de elementos de datos): Especifique un único carácter para separar los elementos de datos simples dentro de los elementos de datos compuestos.</br></br></br></br>Replacement Character (Carácter de reemplazo): Seleccione esta casilla si los datos de carga contienen caracteres que también se utilizan como datos, segmentos o separadores de componentes. Seguidamente, podrá especificar un carácter de reemplazo. Al generar el mensaje X12 saliente, todas las instancias de caracteres separadores de los datos de carga se reemplazan con el carácter especificado.</br></br>Segment Terminator (Terminador de segmento): Especifique un único carácter para indicar el final de un segmento EDI.</br></br>Sufijo: Seleccione el carácter que se usa con el identificador de segmento. Si designa un sufijo, el elemento de datos del terminador de segmento podrá estar vacío. Si se deja vacío el terminador de segmento, deberá designar un sufijo.|

### Validación
|Propiedad|Descripción |
|----|----|
|Message Type (Tipo de mensaje)|Al seleccionar esta opción, se habilita la validación en el receptor del intercambio. Esta validación realiza la validación de EDI en los elementos de datos del conjunto de transacciones, lo que valida los tipos de datos, las restricciones de longitud, los elementos de datos vacíos y los separadores finales.|
|EDI Validation (Validación de EDI)||
|Extended Validation (Validación extendida)|Al seleccionar esta opción, se habilita la validación extendida de los intercambios recibidos del remitente del intercambio. Esto incluye la validación de la longitud del campo, la opcionalidad y el número de repeticiones, además de la validación del tipo de datos XSD. Puede habilitar la validación extendida sin habilitar la validación de EDI o viceversa.|
|Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales)|Al seleccionar esta opción, se especifica que un intercambio EDI recibido de la entidad superará la validación si un elemento de datos de un intercambio EDI no cumple el requisito de longitud debido a los espacios finales, pero sí lo cumple si estos se eliminan.|
|Trailing Separator (Separador final)|Al seleccionar esta opción, se especifica que un intercambio EDI recibido de la entidad superará la validación si un elemento de datos de un intercambio EDI no cumple el requisito de longitud debido a los ceros a la izquierda (o finales), pero sí lo cumple si estos se eliminan.</br></br>Seleccione Not Allowed (No permitidos) si no desea permitir delimitadores y separadores finales en un intercambio recibido del remitente del intercambio. Si el intercambio contiene delimitadores y separadores finales, se declarará no válido.</br></br>Seleccione Optional (Opcionales) para aceptar intercambios con o sin delimitadores y separadores finales.</br></br>Seleccione Mandatory (Obligatorios) si el intercambio recibido debe contener delimitadores y separadores finales.|

Después de seleccionar **OK** (Aceptar) en las hojas abiertas, siga estos pasos:
13. Seleccione el icono **Agreements** (Contratos) de la hoja Integration Account (Cuenta de integración) y verá en la lista el contrato que acaba de agregar. ![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)

## Más información
- [Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0713_2016-->