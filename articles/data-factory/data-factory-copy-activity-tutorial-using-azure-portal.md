<properties 
	pageTitle="Tutorial: Crear una canalización con la actividad de copia mediante el editor de la factoría de datos" 
	description="En este tutorial, creará una canalización de la factoría de datos de Azure con una actividad de copia mediante el Editor de la factoría de datos en el Portal de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/16/2016" 
	ms.author="spelluru"/>

# Tutorial: Crear una canalización con la actividad de copia mediante el editor de la factoría de datos
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Uso del Editor de Data Factory.](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Uso de PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Uso de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Uso del Asistente para copia](data-factory-copy-data-wizard-tutorial.md)


Este tutorial contiene los siguientes pasos:

Paso | Descripción
-----| -----------
[Crear una factoría de datos de Azure](#create-data-factory) | En este paso creará una factoría de datos de Azure denominada **ADFTutorialDataFactory**.  
[Crear servicios vinculados](#create-linked-services) | En este paso, creará dos servicios vinculados: **AzureStorageLinkedService** y **AzureSqlLinkedService**. AzureStorageLinkedService vincula el Almacenamiento de Azure y AzureSqlLinkedService vincula la Base de datos SQL de Azure a ADFTutorialDataFactory. Los datos de entrada para la canalización se encuentran en un contenedor de blob en el almacenamiento de blobs de Azure y los datos de salida se almacenarán en una tabla en la base de datos SQL de Azure. Por lo tanto, agregue estos almacenes de datos como servicios vinculados en la factoría de datos.      
[Crear conjuntos de datos de entrada y salida](#create-datasets) | En el paso anterior, creó servicios vinculados que hacen referencia a los almacenes de datos que contienen datos de entrada y salida. En este paso, definirá dos tablas de factoría de datos: **EmpTableFromBlob** y **EmpSQLTable**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos. Para EmpTableFromBlob, especificará el contenedor de blob que contiene un blob con los datos de origen y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida. También especificará otras propiedades como la estructura de los datos, la disponibilidad de los datos, etc. 
[Crear una canalización](#create-pipeline) | En este paso, creará una canalización denominada **ADFTutorialPipeline** en ADFTutorialDataFactory. La canalización dispondrá de una opción para la **actividad de copia** que copia datos de entrada del blob de Azure a la tabla de salida de Azure SQL. La actividad de copia realiza el movimiento de datos en Data Factory de Azure y la actividad funciona con un servicio disponible generalmente que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Para más información acerca de la actividad de copia, consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md). 
[Supervisión de la canalización](#monitor-pipeline) | En este paso, va a supervisar los sectores de las tablas de entrada y salida con el Portal de Azure.

> [AZURE.IMPORTANT] 
Revise el artículo [Información general del tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) y realice los pasos de requisitos previos antes de completar este tutorial.

## Creación de Data Factory
En este paso, utilice el Portal de Azure para crear una factoría de datos de Azure llamada **ADFTutorialDataFactory**.

1.	Tras iniciar sesión en el [Portal de Azure][azure-portal], haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **Análisis de datos** en la hoja **Crear** y haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

	![New->DataFactory][image-data-factory-new-datafactory-menu]

6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialDataFactory** como **nombre**.
	
  		![Hoja Nueva Factoría de datos][image-data-factory-getstarted-new-data-factory-blade]
	2. Haga clic en **NOMBRE DE GRUPO DE RECURSOS** y haga lo siguiente:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba **ADFTutorialResourceGroup** para el **nombre** del grupo de recursos y haga clic en **Aceptar**.

			![Crear grupo de recursos][image-data-factory-create-resource-group]

		En algunos de los pasos de este tutorial se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../resource-group-overview.md).
7. En la hoja **Nueva factoría de datos**, observe que está seleccionada la opción **Agregar al Panel de inicio**.
8. Haga clic en **Crear** en la hoja **Nueva factoría de datos**.

	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre del generador de datos "ADFTutorialDataFactory" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
	 
	![Nombre de Factoría de datos no disponible][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.
	> 
	> Para crear instancias de Data Factory, debe ser administrador o colaborador en la suscripción de Azure.

9. Haga clic en el concentrador **NOTIFICACIONES** de la izquierda y busque las notificaciones del proceso de creación. Haga clic en **X** para cerrar la hoja **NOTIFICACIONES** si está abierta.
10. Una vez completada la creación, verá la hoja **FACTORÍA DE DATOS** como se muestra a continuación:

    ![Página principal de Factoría de datos][image-data-factory-get-stated-factory-home-page]

## Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un almacenamiento de Azure, una base de datos SQL de Azure o una base de datos SQL Server local.

En este paso, creará dos servicios vinculados: **AzureStorageLinkedService** y **AzureSqlLinkedService**. El servicio vinculado AzureStorageLinkedService vincula una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos SQL de Azure a **ADFTutorialDataFactory**. Más adelante en este tutorial creará una canalización que copia datos de un contenedor de blobs de AzureStorageLinkedService en una tabla SQL de AzureSqlLinkedService.

### Crear un servicio vinculado para una cuenta de almacenamiento de Azure
1.	En la hoja **FACTORÍA DE DATOS**, haga clic en la ventana **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar][image-author-deploy-tile]

	 
5. En el **Editor**, haga clic en el botón **Nuevo almacén de datos** de la barra de herramientas y seleccione **Almacenamiento de Azure** en el menú desplegable. Debería ver la plantilla JSON para crear un servicio vinculado de almacenamiento de Azure en el panel derecho.

	![Botón Nuevo almacén de datos del Editor][image-editor-newdatastore-button]
    
6. Reemplace **accountname** y **accountkey** por los valores de clave de cuenta y nombre de cuenta para la cuenta de almacenamiento de Azure.

	![JSON de almacenamiento de blobs del Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)
	
	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Haga clic en **Implementar** en la barra de herramientas para implementar el servicio vinculado AzureStorageLinkedService. Confirme que aparece el mensaje **SERVICIO VINCULADO CREADO CORRECTAMENTE** en la barra de título.

	![Implementar almacenamiento de blobs del editor][image-editor-blob-storage-deploy]

### Crear un servicio vinculado para la base de datos SQL de Azure
1. En el **Editor de la factoría de datos**, haga clic en el botón **Nuevo almacén de datos** de la barra de herramientas y seleccione **Base de datos SQL de Azure** en el menú desplegable. Verá la plantilla JSON para crear un servicio vinculado SQL de Azure en el panel derecho.

	![Configuración de SQL de Azure del editor][image-editor-azure-sql-settings]

2. Reemplace **servername**, **databasename**, **username@servername** y **password** por los nombres de servidor, base de datos, cuenta de usuario y contraseña de SQL Azure.
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar AzureSqlLinkedService.
   

## Creación de conjuntos de datos
En el paso anterior, creó los servicios vinculados **AzureStorageLinkedService** y **AzureSqlLinkedService** para vincular una cuenta de Almacenamiento de Azure y una base de datos SQL de Azure a la factoría de datos: **ADFTutorialDataFactory**. En este paso, definirá dos tablas de factoría de datos: **EmpTableFromBlob** y **EmpSQLTable**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos a los que hace referencia AzureStorageLinkedService y AzureSqlLinkedService, respectivamente. Para EmpTableFromBlob, especificará el contenedor de blob que contiene un blob con los datos de origen y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida.

### Creación de un conjunto de datos de entrada 
Una tabla es un conjunto de datos rectangular y tiene un esquema. En este paso, creará una tabla denominada **EmpBlobTable** que apunta a un contenedor de blobs en Almacenamiento de Azure representado por el servicio vinculado **AzureStorageLinkedService**.

1. En el **Editor** para Factoría de datos, haga clic en el botón **Nuevo conjunto de datos** de la barra de herramientas y haga clic en botón **tabla Blob** en el menú desplegable.
2. Reemplace JSON en el panel derecho por el siguiente fragmento JSON:

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Tenga en cuenta lo siguiente:
	
	- **type** de conjunto de datos está establecido en **AzureBlob**.
	- **linkedServiceName** se establece en **AzureStorageLinkedService**. Este servicio vinculado lo había creado en el paso 2.
	- **folderPath** se establece en el contenedor **adftutorial**. También puede especificar el nombre de un blob en la carpeta. Puesto que no se especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.
	- el **tipo** de formato se establece en **TextFormat**
	- Hay dos campos en el archivo de texto: **FirstName** y **LastName** separados por un carácter de coma (**columnDelimiter**)
	- La **disponibilidad** se establece en **cada hora** (**frecuencia** se establece en **hora** e **intervalo** se establece en **1**), por lo que el servicio de la factoría de datos buscará los datos de entrada cada hora en la carpeta raíz del contenedor de blob (**adftutorial**) especificado.
	

	Si no especifica **fileName** para una **tabla** de **entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada.
 
	Si no especifica un valor **fileName** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.&lt;Guid&gt;.txt (ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Haga clic en **Implementar** en la barra de herramientas para implementar la tabla **EmpTableFromBlob**. Confirme que aparece el mensaje **TABLA CREADA CORRECTAMENTE** en la barra de título del Editor.

### Creación del conjunto de datos de salida
En esta parte del paso, creará una tabla de salida denominada **EmpSQLTable** que apunta a una tabla SQL de la base de datos SQL de Azure que está representada por el servicio vinculado **AzureSqlLinkedService**.

1. En el **Editor** para Factoría de datos, haga clic en el botón **Nuevo conjunto de datos** de la barra de herramientas y haga clic en **Tabla SQL de Azure** en el menú desplegable.
2. Reemplace JSON en el panel derecho por el siguiente fragmento JSON:

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Tenga en cuenta lo siguiente:
	
	* **type** de conjunto de datos está establecido en **AzureSqlTable**.
	* **linkedServiceName** está establecido en **AzureSqlLinkedService** (creó este servicio vinculado en el paso 2).
	* **tablename** está establecido en **emp**.
	* Hay tres columnas: **ID**, **FirstName** y **LastName**: en la tabla emp de la base de datos, pero el id. es una columna de identidad, por lo que deberá especificar solo **FirstName** y **LastName** aquí.
	* El elemento **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval**, en **1**). El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.


3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar la tabla **EmpSQLTable**.


## Creación de una canalización
En este paso, creará una canalización con una **actividad de copia** que usa **EmpTableFromBlob** como entrada y **EmpSQLTable** como salida.

1. En el **Editor** de la Factoría de datos, haga clic en **Nueva canalización** en la barra de herramientas. Haga clic en **... (puntos suspensivos)** en la barra de herramientas si no ve el botón. O bien, haga clic con el botón secundario **Canalizaciones** en la vista de árbol y elija **Nueva canalización**.

	![Botón Nueva canalización del editor][image-editor-newpipeline-button]
 
2. Reemplace JSON en el panel derecho por el siguiente fragmento JSON:
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	Tenga en cuenta lo siguiente:

	- En la sección de actividades, solo hay una actividad cuyo **tipo** está establecido en **CopyActivity**.
	- La entrada de la actividad está establecida en **EmpTableFromBlob** y la salida en **EmpSQLTable**.
	- En la sección **transformación**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.

	Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Puede especificar solo la parte de fecha y omitir la parte de hora de la fecha y hora. Por ejemplo, "03-02-2015", que es equivalente a "03-02-2015T00:00:00Z"
	
	Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **end** es opcional, pero se utilizará en este tutorial.
	
	Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**.
	
	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Haga clic en **Implementar** en la barra de herramientas para crear e implementar **ADFTutorialPipeline**. Confirme que aparece el mensaje **LA CANALIZACIÓN SE HA CREADO CORRECTAMENTE**.
5. Ahora, para cerrar la hoja **Editor**, haga clic en **X**. Vuelva a hacer clic en **X** para cerrar la hoja ADFTutorialDataFactory con la barra de herramientas y la vista de árbol. Si ve el mensaje que indica que **se descartarán los cambios no guardados**, haga clic en **Aceptar**.
6. Debe volver a la hoja **FACTORÍA DE DATOS** para **ADFTutorialDataFactory**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.
 
### Visualización de la factoría de datos en una vista de diagrama 
1. En la hoja **FACTORÍA DE DATOS**, haga clic en **Diagrama**.

	![Módulo de la factoría de datos: ventana de diagrama][image-datafactoryblade-diagramtile]

2. Debería ver un diagrama similar al siguiente:

	![Vista Diagrama][image-data-factory-get-started-diagram-blade]

	Puede acercar, alejar, acercar al 100%, ampliar para ajustar, colocar automáticamente canalizaciones y tablas, y mostrar información de linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados). Puede hacer doble clic en un objeto (tabla o canalización de entrada o salida) para ver sus propiedades.
3. Haga clic con el botón secundario en **ADFTutorialPipeline** en la vista Diagrama y haga clic en **Abrir canalización**. Debería ver las actividades de la canalización, junto con los conjuntos de datos de entrada y salida para las actividades. En este tutorial, solo tiene una actividad en la canalización (actividad de copia) con EmpTableBlob como conjunto de datos de entrada y EmpSQLTable como conjunto de datos de salida.

	![Abrir canalización](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. Haga clic en **Factoría de datos** en la ruta de navegación de la esquina superior izquierda para volver a la vista de diagrama. La vista de diagrama muestra todas las canalizaciones. En este ejemplo, solo ha creado una canalización.
 

## Supervisión de canalizaciones
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure.

1. Vaya al [Portal de Azure (vista previa)][azure-portal] si no lo ha abierto.
2. Si la hoja de **ADFTutorialDataFactory** no está abierta, ábrala haciendo clic en **ADFTutorialDataFactory** en el **Panel de inicio**.
3. Deben mostrarse el recuento y los nombres de las tablas y la canalización que creó en esta hoja.

	![página principal con nombres][image-data-factory-get-started-home-page-pipeline-tables]

4. Ahora haga clic en la ventana **Conjunto de datos**.
5. En la hoja **Conjuntos de datos**, haga clic en **EmpTableFromBlob**. Esta es la tabla de entrada para **ADFTutorialPipeline**.

	![Conjuntos de datos con EmpTableFromBlob seleccionado][image-data-factory-get-started-datasets-emptable-selected]
5. Observe que ya se han producido los segmentos de datos hasta la hora actual y que están **listos** porque el archivo **emp.txt** existe todo el tiempo en el contenedor de blobs: **adftutorial\\input**. Confirme que no aparecen segmentos en la sección **Segmentos que han fallado recientemente** de la parte inferior.

	Tanto la lista **Segmentos actualizados recientemente** como la lista **Segmentos que han fallado recientemente** se ordenan por la **HORA DE LA ÚLTIMA ACTUALIZACIÓN**. En las situaciones siguientes, se cambia la hora de actualización de un segmento.
    

	-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureRmDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
	-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos. Haga clic en **Filtro** en la barra de herramientas para filtrar los segmentos.
	
	Para ver los segmentos de datos ordenados por las horas de inicio y finalización, haga clic en la ventana **Segmentos de datos (por hora del segmento)**.

	![Segmentos de datos por tiempo de segmento][DataSlicesBySliceTime]

6. Ahora, en la hoja **Conjuntos de datos**, haga clic en **EmpSQLTable**. Esta es la tabla de salida para **ADFTutorialPipeline**.

	![hoja de conjuntos de datos][image-data-factory-get-started-datasets-blade]



	 
6. Debería ver la hoja **EmpSQLTable** como se muestra a continuación:

	![hoja de tabla][image-data-factory-get-started-table-blade]
 
7. Tenga en cuenta que ya se han producido los segmentos de datos hasta el momento actual y están **listos**. No se muestra ningún segmento en la sección de **segmentos con problemas** de la parte inferior.
8. Haga clic en **… (puntos suspensivos)** para ver todos los segmentos.

	![hoja de segmentos de datos][image-data-factory-get-started-dataslices-blade]

9. Haga clic en cualquier segmento de datos de la lista. Debe aparecer la hoja **SEGMENTO DE DATOS**.

	![hoja de segmento de datos][image-data-factory-get-started-dataslice-blade]
  
	Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.

11. En la hoja **SEGMENTO DE DATOS**, verá todas las ejecuciones de actividades de la lista en la parte inferior. Haga clic en una **ejecución de actividad** para ver la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**.

	![Detalles de ejecución de actividad][image-data-factory-get-started-activity-run-details]

	
12. Haga clic en **X** para cerrar todas las hojas hasta que regrese a la hoja inicial de **ADFTutorialDataFactory**.
14. (opcional) Haga clic en **Canalizaciones** en la página principal de **ADFTutorialDataFactory**, en **ADFTutorialPipeline** en la hoja **Canalizaciones** y obtenga detalles de las tablas de entrada (**Consumido**) o tablas de salida (**Producido**).
15. Inicie **SQL Server Management Studio**, conéctese a la base de datos SQL de Azure y compruebe que las filas se han insertado en la tabla **emp** de la base de datos.

	![resultados de la consulta SQL][image-data-factory-get-started-sql-query-results]


## Resumen 
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Ha usado el Portal de Azure para crear la factoría de datos, los servicios vinculados, los conjuntos de datos y una canalización. Estos son los pasos de alto nivel que realizó en este tutorial:

1.	Ha creado una **factoría de datos** de Azure.
2.	Ha creado **servicios vinculados**:
	1. Un servicio vinculado **Almacenamiento de Azure** para vincular la cuenta de almacenamiento de Azure que contiene datos de entrada.
	2. Un servicio vinculado **SQL Azure** para vincular la base de datos SQL de Azure que contiene los datos de salida.
3.	Ha creado **conjuntos de datos** que describen los datos de entrada y salida para las canalizaciones.
4.	Ha creado una **canalización** con una **actividad de copia** con un origen **BlobSource** y un receptor **SqlSink**.


## Otras referencias
| Tema. | Descripción |
| :---- | :---- |
| [Actividades de movimiento de datos](data-factory-data-movement-activities.md) | En este artículo se proporciona información detallada sobre la actividad de copia que se usa en el tutorial. |
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) | En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Procesos](data-factory-create-pipelines.md) | Este artículo ayuda a comprender las canalizaciones y actividades en Factoría de datos de Azure y cómo aprovecharlas para construir flujos de trabajo de extremo a extremo controlados por datos para su escenario o empresa. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayudará a comprender los conjuntos de datos en Factoría de datos de Azure.
| [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la aplicación de supervisión y administración. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0720_2016-->