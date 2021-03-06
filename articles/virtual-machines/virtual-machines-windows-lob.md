<properties 
	pageTitle="Aplicación de línea de negocio de Azure | Microsoft Azure" 
	description="Obtenga información sobre el valor de una aplicación de línea de negocio de Azure, configure un entorno de prueba e implemente una configuración de alta disponibilidad." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="josephd"/>

# Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad

Configure la primera o siguiente aplicación de línea de negocio basada en web solo de intranet en Microsoft Azure y aprovéchese de la facilidad de configuración y de la posibilidad de ampliar rápidamente la aplicación para que incluya una nueva capacidad.
 
Con las máquinas virtuales y las características de la Red virtual de los Servicios de infraestructura de Azure, podrá implementar y ejecutar rápidamente una aplicación de línea de negocio que sea accesible para los usuarios de su organización. Por ejemplo, puede configurar lo siguiente.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Dado que Red Virtual de Azure es una extensión de la red local con toda la nomenclatura correcta y el enrutamiento del tráfico establecidos, los usuarios tendrán acceso a los servidores de la aplicación de línea de negocio de la misma manera que si se encontrara en un centro de datos local.

Esta configuración permite ampliar fácilmente la capacidad de la aplicación agregando nuevas máquinas virtuales de Azure en las que los costos en curso tanto de mantenimiento como de hardware son menores que los de ejecutar un conjunto equivalente en el centro de datos.

El siguiente paso es configurar una aplicación de línea de negocio de desarrollo y pruebas hospedada en Azure.

## Creación de una aplicación de línea de negocio de desarrollo y pruebas hospedada en Azure

Una red virtual entre locales está conectada a una red local con una conexión de ExpressRoute o VPN de sitio a sitio. Si quiere crear un entorno de desarrollo y pruebas que imite la configuración final y experimentar con el acceso a la aplicación y realizar la administración remota a través de una conexión VPN, vea [Configuración de una aplicación de línea de negocio (LOB) basada en web en una nube híbrida para pruebas](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md).

![](./media/virtual-machines-windows-lob/CreateLOBAppHybridCloud_3.png)
 
Puede crear este entorno de desarrollo y pruebas de forma gratuita con su [suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una suscripción de Azure.

El siguiente paso es crear una aplicación de línea de negocio de alta disponibilidad en Azure.

## Implementación de una aplicación de línea de negocio hospedada en Azure

La configuración representativa y básica de una aplicación de línea de negocio de alta disponibilidad en Azure tiene el siguiente aspecto.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Consta de:

- Una aplicación de línea de negocio solo de intranet con dos servidores en los niveles web y de base de datos.
- Una configuración SQL Server AlwaysOn con dos máquinas virtuales que ejecutan SQL Server y un equipo de nodos de mayoría en un clúster.
- Servicios de dominio de Active Directory en la red virtual con dos controladores de dominio de réplica.

Para obtener información general sobre aplicaciones de línea de negocio, vea [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664).

Para implementar esta configuración, siga este proceso :

- Fase 1: Configuración de Azure 

	Use Azure PowerShell para crear una red virtual entre locales, conjuntos de disponibilidad y las cuentas de almacenamiento. Para los pasos de configuración detallados, consulte [Fase 1](virtual-machines-windows-ps-lob-ph1.md).

- Fase 2: Configuración de los controladores de dominio.

	Configure dos controladores de dominio de réplica de Active Directory y los valores de DNS para la red virtual. Para los pasos de configuración detallados, consulte [Fase 2](virtual-machines-windows-ps-lob-ph2.md).

- Fase 3: Configuración de la infraestructura de SQL Server.

	Cree las máquinas virtuales que ejecutan SQL Server y el clúster. Para los pasos de configuración detallados, consulte [Fase 3](virtual-machines-windows-ps-lob-ph3.md).

- Fase 4: Configuración de servidores web.

	Cree las máquinas virtuales de servidor web y agregue la aplicación de línea de negocio. Para la información detallada, consulte [Fase 4](virtual-machines-windows-ps-lob-ph4.md).

- Fase 5: Configuración de un grupo de disponibilidad SQL Server AlwaysOn.

	Prepare las bases de datos de la aplicación, cree un grupo de disponibilidad SQL Server AlwaysOn y agréguele las bases de datos de la aplicación. Para los pasos de configuración detallados, consulte [Fase 5](virtual-machines-windows-ps-lob-ph5.md).

Una vez configurada, puede ampliar fácilmente esta aplicación de línea de negocio agregando más servidores web o máquinas virtuales que ejecuten SQL Server al clúster.

## Paso siguiente

- Obtenga [información general](virtual-machines-windows-lob-overview.md) de la carga de trabajo de producción antes de comenzar con la configuración.

<!---HONumber=AcomDC_0601_2016-->