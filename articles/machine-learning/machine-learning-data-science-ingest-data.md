<properties 
	pageTitle="Carga de datos en entornos de almacenamiento para el análisis | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="bradsev" />

# Carga de datos en entornos de almacenamiento para el análisis

El proceso de ciencia de datos en equipos requiere que los datos se introduzcan o se cargue en una variedad de entornos de almacenamiento diferentes para que se procesen o analicen de la manera más adecuada en cada fase del proceso. Entre los destinos de datos más usados para el procesamiento se incluyen el almacenamiento de blobs de Azure, las bases de datos SQL Azure, SQL Server en VM de Azure, HDInsight (Hadoop) y Aprendizaje automático de Azure.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Este **menú** vincula a temas en los que se describe cómo introducir datos en estos entornos de destino donde se almacenan y se procesan los datos.

Las necesidades técnicas y empresariales, así como la ubicación inicial, el formato y el tamaño de sus datos determinarán los entornos de destino en los que deben introducirse los datos para lograr los objetivos de su análisis. No es raro que un escenario requiera que los datos se muevan entre varios entornos para lograr la variedad de las tareas necesarias para construir un modelo predictivo. En esta secuencia de tareas se puede incluir, por ejemplo, la exploración de datos, el procesamiento previo, la limpieza, el muestreo inferior y el entrenamiento del modelo.

<!---HONumber=AcomDC_0622_2016-->