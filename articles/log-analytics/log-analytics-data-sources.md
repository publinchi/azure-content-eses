<properties 
   pageTitle="Orígenes de datos en Log Analytics | Microsoft Azure"
   description="Los orígenes de datos definen los datos que Log Analytics recopila de agentes y otros orígenes conectados. En este artículo se describe el concepto de cómo Log Analytics usa los orígenes de datos, se explican los detalles de cómo configurarlos y se brinda un resumen de los distintos orígenes de datos disponibles."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Orígenes de datos en Log Analytics

Log Analytics recopila datos desde los orígenes conectados en el área de trabajo de OMS y los almacena en el repositorio de OMS. Los orígenes de datos que configura definen los datos que se recopilan de cada uno de ellos. Los datos del repositorio de OMS se almacenan como un conjunto de registros. Cada origen de datos crea registros de un tipo determinado, donde cada tipo tiene su propio conjunto de propiedades.

![Recopilación de datos de Log Analytics](./media/log-analytics-data-sources/overview.png)

Los orígenes de datos son distintos a las soluciones de OMS que también recopilan datos desde los orígenes conectados y crean registros en el repositorio de OMS. Se pueden agregar soluciones al área de trabajo desde la galería de soluciones y normalmente proporcionarán herramientas de análisis adicionales en el portal de OMS.

## Resumen de orígenes de datos

En la tabla siguiente se enumeran los orígenes de datos que actualmente se encuentran disponibles en Log Analytics. Cada uno de ellos tiene un vínculo a un artículo independiente, donde se proporcionan detalles con respecto al origen de datos determinado.

| Origen de datos | Tipo de evento | Descripción |
|:--|:--|:--|
| [Registros personalizados](log-analytics-data-sources-custom-logs.md) | \<NombreRegistro\>_CL | Archivos de texto en agentes de Windows o Linux que contienen información de registro. |
| [Registros de eventos de Windows](log-analytics-data-sources-windows-events.md) | Evento | Eventos recopilados desde el registro de eventos en equipos Windows. |
| [Contadores de rendimiento de Windows](log-analytics-data-sources-performance-counters.md) | Rend | Contadores de rendimiento recopilados de equipos Windows. |
| [Contadores de rendimiento de Linux](log-analytics-data-sources-performance-counters.md) | Rend | Contadores de rendimiento recopilados de equipos Linux. |
| [Registros de IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Registros de Internet Information Services en formato W3C. |
| Syslog | Syslog | Eventos de syslog en equipos Windows o Linux. |

## Configuración de orígenes de datos

Los orígenes de datos se configuran desde el menú **Datos** en la **configuración** de Log Analytics. Cualquier configuración se entrega a todos los orígenes conectados del área de trabajo de OMS. Actualmente, no puede excluir ningún agente de esta configuración.

![Configurar eventos de Windows](./media/log-analytics-data-sources/configure-events.png)

2. En la consola de OMS, seleccione el icono **Configuración**.
3. Seleccione **Datos**.
4. Haga clic en el origen de datos que se va a configurar.
5. Siga el vínculo a la documentación correspondiente a cada origen de datos de la tabla anterior para obtener detalles sobre su configuración.

## Colección de datos

Las configuraciones de orígenes de datos se entregan a agentes directamente conectados con OMS en cuestión de minutos. Los datos especificados se recopilan desde el agente y se entregan directamente a Log Analytics a intervalos específicos a cada origen de datos. Consulte la documentación de cada origen de datos para ver estas especificaciones.

En el caso de los agentes de System Center Operations Manager (SCOM) de un grupo de administración conectado, la configuración de origen de datos se convierte en módulos de administración y se entrega al grupo de administración cada 5 minutos, de manera predeterminada. El agente descarga el módulo de administración como cualquier otro, recopila los datos especificados y los envía a un servidor de administración, el que reenvía los datos al OMS. El agente no requiere comunicación directa con OMS para ninguno de los orígenes de datos. Puede leer detalles sobre cómo conectar SCOM y OMS y modificar la frecuencia con que la configuración se entrega en [Configuración de integración con System Center Operations Manager](log-analytics-om-agents.md).

## Registros de Log Analytics

Todos los datos que recopila Log Analytics se almacenan en el repositorio de OMS como registros. Los registros que recopilan distintos orígenes de datos tendrán su propio conjunto de propiedades y se identificar por su propiedad **Type**. Consulte la documentación de cada origen de datos y solución para obtener detalles sobre cada tipo de registro.


## Pasos siguientes

- Obtenga información sobre las [soluciones](log-analytics-add-solutions.md) que agregan funcionalidad a Log Analytics y que también recopilan datos en el repositorio de OMS.
- Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados desde soluciones y orígenes de datos.  
- Configure alertas que le notifiquen de manera proactiva los datos críticos recopilados de soluciones y orígenes de datos.

<!---HONumber=AcomDC_0504_2016-->
