<properties 
   pageTitle="Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos en el sitio local | Microsoft Azure"
   description="Aprenda a conmutar por recuperación al sitio local después de conmutar por error las máquinas virtuales de VMware y los servidores físicos a Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="07/08/2016"
   ms.author="ruturajd"/>

# Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos al sitio local

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-failback-azure-to-vmware.md)
- [Portal de Azure clásico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal de Azure clásico (heredado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure al sitio local. Siga las instrucciones que se describen en este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que han conmutado por error desde el sitio local a Azure mediante este [tutorial](site-recovery-vmware-to-azure-classic.md).



## Información general

Este diagrama muestra la arquitectura de conmutación por recuperación en este escenario.

Use esta arquitectura cuando el servidor de procesos sea local y se esté usando ExpressRoute.

![Diagrama de la arquitectura de ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Use esta arquitectura cuando el servidor de procesos esté en Azure y tenga una VPN o una conexión de ExpressRoute.

![Diagrama de la arquitectura de VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.PNG)

Para ver la lista completa de puertos y el diagrama de la arquitectura de la conmutación por recuperación, consulte la imagen siguiente.

![Conmutación por error/recuperación de todos los puertos](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Así es cómo funciona la conmutación por recuperación:

- Después de conmutar por error a Azure, conmuta por recuperación a su sitio local en unas cuantas fases:
	- **Fase 1**: proteja de nuevo las máquinas virtuales de Azure de modo que comiencen a replicarse otra vez en las de VMware que se ejecutan en el sitio local.
	- **Fase 2**: una vez que las máquinas virtuales de Azure se estén replicando en el sitio local, ejecute una conmutación por error para conmutar por recuperación desde Azure.
	- **Fase 3**: después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales a las que conmutó por recuperación, para que comiencen a replicarse en Azure.


### Conmutación por recuperación a la ubicación original o alternativa

Si ha conmutado por error una máquina virtual de VMware, puede conmutar por recuperación a la misma máquina virtual de origen si aún existe en el entorno local. En este escenario solo se conmutarán por recuperación los cambios diferenciales. Observe lo siguiente:

- Si ha conmutado por error servidores físicos, la conmutación por recuperación siempre es a una nueva máquina virtual de VMware.
	- Antes de conmutar por recuperación una máquina física, tenga en cuenta lo siguiente:
	- La máquina física protegida volverá como una virtual cuando se conmute por recuperación de Azure a VMware.
	- Asegúrese detectar al menos un servidor de destino maestro junto con los hosts ESX/ESXi necesarios para la conmutación por recuperación.
- Si conmuta por recuperación a la máquina virtual original, se necesita lo siguiente:
	- Si la máquina virtual está administrada por un servidor vCenter, el host ESX del destino principal debe tener acceso al almacén de datos de máquinas virtuales.
	- Si la máquina virtual está en un host ESX, pero no está administrada por vCenter, el disco duro de la máquina virtual debe estar en un almacén de datos accesible para el host de MT.
	- Si la máquina virtual está en un host ESX y no usa vCenter, debe completar la detección del host ESX de MT antes de volver a protegerla. Lo mismo se aplica si también conmuta por recuperación a servidores físicos.
	- Otra opción (si existe la máquina virtual local) es eliminarla antes de realizar una conmutación por recuperación. En este caso, la conmutación por recuperación crea una nueva máquina virtual en el mismo host que el host ESX de destino maestro.
	
- Cuando se conmuta por recuperación a una ubicación alternativa, los datos se recuperan en el mismo almacén de datos y el mismo host ESX que los usados por el servidor de destino maestro local.


## Requisitos previos

- Necesitará un entorno de VMware para conmutar por recuperación máquinas virtuales de VMware y servidores físicos. No se admite la conmutación por recuperación a un servidor físico.
- Para realizar la conmutación por recuperación , debe haber creado una red de Azure cuando configuró inicialmente la protección. La conmutación por recuperación necesita una conexión VPN o ExpressRoute desde la red de Azure, en la que se encuentran las máquinas virtuales de Azure, hasta el sitio local.
- Si las máquinas virtuales que desea conmutar por recuperación las administra un servidor vCenter, deberá asegurarse de tener los permisos necesarios para la detección de máquinas virtuales en servidores vCenter. [Más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Si existen instantáneas en una máquina virtual, la reprotección dará error. Puede eliminar las instantáneas o los discos.
- Antes de conmutar por recuperación, deberá crear una serie de componentes:
	- **Cree un servidor de procesos en Azure**. Es una máquina virtual de Azure que necesitará crear y mantener en ejecución durante la conmutación por recuperación. Puede eliminar la máquina una vez completada la operación.
	- **Cree un servidor de destino maestro**: este servidor envía y recibe datos de conmutación por recuperación. El servidor de administración que creó de forma local tiene instalado de forma predeterminada un servidor de destino maestro. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino maestro distinto para esta operación.
	- Si desea crear un servidor de destino maestro adicional que se ejecute en Linux, debe configurar la máquina virtual de Linux antes de instalar al servidor de destino maestro, como se describe a continuación.
- El servidor de configuración es necesario localmente cuando realiza una conmutación por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. De lo contrario, la conmutación por recuperación no se realizará correctamente. Por lo tanto, asegúrese de realizar una copia de seguridad programada periódica del servidor. En caso de desastre, tendrá que restaurarla con la misma dirección IP para que funcione la conmutación por recuperación.
- Asegúrese de establecer la opción disk.enableUUID=true de los parámetros de configuración de la máquina virtual de destino maestra en VMware. Si la fila no existe, agréguela. Este paso es necesario a fin de proporcionar un UUID uniforme al VMDK, para que se monte correctamente.
- **El servidor de destino maestro no puede migrarse con Storage vMotion**. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se iniciará puesto que los discos no estarán disponibles.

## Directiva de conmutación por recuperación
Para volver a replicar en el entorno local, necesitará una directiva de conmutación por recuperación. Esta directiva se crea automáticamente al crear una directiva de avance. Observe lo siguiente:

1. Esta directiva se asocia automáticamente con el servidor de configuración durante la creación.
2. Esta directiva no es editable.
3. Los valores establecidos de la directiva son (Umbral de RPO = 15 min, Retención de punto de recuperación = 24 horas, Frecuencia coherente de instantáneas de la aplicación = 60 min). ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## Configuración del servidor de procesos en Azure

Para que las máquinas virtuales de Azure puedan devolver los datos a un servidor de destino maestro local, es preciso instalar un servidor de procesos en Azure.

Si ha protegido las máquinas como recursos clásicos (es decir, la máquina virtual recuperada en Azure es una máquina virtual clásica), necesitará un servidor de procesos clásico en Azure. Si ha recuperado las máquinas como administrador de recursos como tipo de implementación, necesita un servidor de procesos de un tipo de implementación de Resource Manager. El tipo se selecciona mediante la red virtual de Azure en la que se implementa el servidor de procesos.

1.  En Almacén > Configuración > Manage Site Recovery Infrastructure (Administrar infraestructura de Site Recovery) > **Servidores de configuración**, en el encabezado For VMware and Physical Machines (Para VMware y máquinas físicas), seleccione el servidor de configuración. Haga clic en + Servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware-new/add-processserver.PNG)

2. Elija la opción para implementar el servidor de procesos como "Deploy a failback process server in Azure" (Implementación de un servidor de procesos de conmutación por recuperación en Azure).

3. Seleccione la suscripción en la que se han recuperado las máquinas.

4. Luego seleccione la red de Azure en que ha recuperado las máquinas. El servidor de procesos debe estar en la misma red para que las máquinas virtuales recuperadas y el servidor de procesos puedan comunicarse.

5. Si ha seleccionado una red de *implementación clásica*, se le pedirá que cree una máquina virtual nueva a través de la galería de Azure y que instale en ella el servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware-new/add-classic.PNG)
	
	1. El nombre de la imagen es *Microsoft Azure Site Recovery Process Server V2* (Servidor de procesos de Microsoft Azure Site Recovery, versión 2). Asegúrese de seleccionar *Clásico* como el modelo de implementación.
	
		![](./media/site-recovery-failback-azure-to-vmware-new/templateName.PNG)
	
	2. Instale el servidor de procesos según los pasos [especificados aquí](./site-recovery-vmware-to-azure-classicz.md#step-5-install-the-management-server).
	
6. Si selecciona la red de Azure *Resource Manager*, tendrá que proporcionar las entradas siguientes para implementar el servidor.

    1. El grupo de recursos en que desea implementar el servidor.
	
	2. Asigne un nombre al servidor.
	
	3. Asígnele un nombre de usuario y una contraseña para poder iniciar sesión.
	
	4. Elija la cuenta de almacenamiento en la que desea implementar el servidor.
	
	5. Elija la subred específica y la interfaz de red para conectarse a ella. Nota: Debe crear su propia tarjeta de [interfaz de red](../virtual-network/virtual-networks-multiple-nics.md) (NIC) y seleccionarla durante la implementación.
	
		![](./media/site-recovery-failback-azure-to-vmware-new/PSinputsadd.PNG)
	
	6. Haga clic en Aceptar. Esto desencadenará un trabajo que creará una máquina virtual con el tipo de implementación de Resource Manager con la instalación del servidor de procesos. Debe ejecutar la instalación dentro de la máquina virtual para registrar el servidor en el servidor de configuración. Para ello, siga [estos pasos](./site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server).

	7. Se desencadenará un trabajo para implementar el servidor de procesos.

7. Al final, el servidor de procesos debe aparecer en la página de servidores de configuración, en la sección de servidores asociados, en la pestaña Servidores de procesos. ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

		
	>[AZURE.NOTE] El servidor no estará visible en las **propiedades de la máquina virtual**. Solo aparecerá en la pestaña **Servidores** del servidor de administración en el que se ha registrado. Puede tardar entre 10 y 15 minutos en aparecer el servidor de procesos.


## Configuración del servidor de destino maestro local

El servidor de destino maestro recibe los datos de conmutación por recuperación. Un servidor de destino maestro se instala automáticamente en el servidor de administración local, pero si va a conmutar por recuperación muchos datos puede que tenga que configurar otro adicional. Para ello, realice lo siguiente:

>[AZURE.NOTE] Si desea instalar un servidor de destino maestro en Linux, siga las instrucciones del procedimiento siguiente.

1. Si va a instalar al servidor de destino maestro en Windows, abra la página de inicio rápido de la máquina virtual en la que va a instalarlo y descargue el archivo de instalación para el Asistente de configuración unificada de Azure Site Recovery.
2. Ejecute el programa de instalación y, en **Before you begin** (Antes de comenzar), seleccione **Add additional process servers to scale out deployment** (Agregar servidores de procesos adicionales para el escalado horizontal de la implementación).
3. Complete el asistente tal y como hizo cuando [instaló el servidor de administración](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). En la página **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP de este servidor de destino maestro y una frase de contraseña para acceder a la máquina virtual.

### Configuración de una máquina virtual de Linux como servidor de destino maestro
Para configurar el servidor de administración que ejecuta el servidor de destino maestro como una máquina virtual de Linux, será necesario instalar el sistema operativo mínimo CentOS 6.6, recuperar los identificadores SCSI de cada disco duro SCSI, instalar algunos paquetes adicionales y aplicar algunos cambios personalizados.

#### Instalación de CentOS 6.6

1.	Instale el sistema operativo mínimo CentOS 6.6 en la máquina virtual del servidor de administración. Mantenga la imagen ISO en una unidad de DVD y arranque el sistema. Omita la comprobación de medios, seleccione inglés de Estados Unidos como el idioma, seleccione **Basic Storage Devices** (Dispositivos de almacenamiento básico), compruebe que el disco duro no tenga ningún dato importante y haga clic en **Sí** para descartar los datos. Escriba el nombre de host del servidor de administración y seleccione el adaptador de red del servidor. En el cuadro de diálogo **Editing System** (Sistema de edición), seleccione **Conectar automáticamente** y agregue una configuración de dirección IP estática, red y DNS. Especifique una zona horaria y una contraseña raíz para acceder al servidor de administración.
2.	Cuando se le pregunte por el tipo de instalación que quiere, seleccione **Create Custom Layout** (Crear diseño personalizado) como partición. Tras hacer clic en **Siguiente**, seleccione **Gratis** y haga clic en Crear. Cree **/**, **/var/crash** y **/home partitions** con **FS Type:** **ext4** (Tipo FS: ext4). Cree la partición de intercambio como **FS Type: swap** (Tipo FS: intercambio).
3.	Si se encuentran dispositivos ya existentes, aparecerá un mensaje de advertencia. Haga clic en **Formato** para formatear la unidad con la configuración de partición. Haga clic en **Write change to disk** (Escribir cambios en el disco) para aplicar los cambios de partición.
4.	Seleccione **Install boot loader** (Instalar cargador de arranque) > **Next** (Siguiente) para instalar el cargador de arranque en la partición raíz.
5.	Cuando la instalación se haya completado, haga clic en **Reiniciar**.


#### Recuperación de los id. SCSI

1. Después de la instalación, recupere los id. SCSI de cada disco duro SCSI de la máquina virtual. Para ello, cierre la máquina virtual del servidor de administración y, en las propiedades de la máquina virtual en VMware, haga clic con el botón derecho en la entrada de la máquina virtual > **Edit Settings** (Editar configuración) > **Options** (Opciones).
2. Seleccione **Advanced** (Avanzadas) > **General item** (Elemento general) y haga clic en **Configuration Parameters** (Parámetros de configuración). Esta opción estará desactivada cuando se ejecuta la máquina. Para que se active, la máquina debe estar apagada.
3. Si existe la fila **disk.EnableUUID**, asegúrese de que el valor esté establecido en **True** (Verdadero) (distingue mayúsculas de minúsculas). En este caso, puede cancelar y probar el comando SCSI en un sistema operativo invitado después de arrancar.
4.	Si la fila no existe, haga clic en **Add Row** (Agregar fila) y agréguela con el valor **True** (Verdadero). No utilice comillas dobles.

#### Instalación de paquetes adicionales

Debe descargar e instalar algunos paquetes adicionales.

1.	Asegúrese de que el servidor de destino maestro esté conectado a Internet.
2.	Ejecute este comando para descargar e instalar 15 paquetes desde el repositorio de CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.	Si las máquinas de origen que va a proteger ejecutan Linux con el sistema de archivos Reiser o XFS en el dispositivo raíz o de arranque, debe descargar e instalar paquetes adicionales de la manera siguiente:

	- # # cd /usr/local
	- # wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
	- # wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
	- # rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
	- # wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
	- # rpm –ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicación de cambios personalizados

Lleve a cabo los siguientes pasos para aplicar cambios personalizados después de haber completado los pasos posteriores a la instalación y haber instalado los paquetes:

1.	Copie al binario de RHEL 6-64 Unified Agent en la máquina virtual. Ejecute este comando para descomprimir el binario: **tar - zxvf <nombre de archivo>**
2.	Ejecute este comando para conceder permisos: **# chmod 755 ./ApplyCustomChanges.sh**
3.	Ejecute el script: **# ./ApplyCustomChanges.sh**. Solo debe ejecutar el script una vez. Después de que el script se haya ejecutado correctamente, reinicie el servidor.


## Ejecución de la conmutación por recuperación

### Reprotección de las máquinas virtuales de Azure

1.	En el Almacén > Elementos replicados, seleccione la máquina virtual de la que se ha realizado una conmutación por error y haga clic con el botón derecho en **Reproteger**. También puede hacer clic en la máquina y seleccionar la opción para reproteger con los botones de comando.
2.	En la hoja, puede ver que la dirección de protección "De Azure a local" ya está seleccionada.
3.  En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos de la máquina virtual de Azure.
4.  Seleccione el **almacén de datos** en el que desea recuperar los discos locales. Esta opción se utiliza cuando se elimina la máquina virtual local y es necesario crear discos nuevos. Esta opción se omite si los discos ya existen, pero debe especificar un valor.
5.	La unidad de retención se utiliza para detener los puntos en el tiempo cuando la máquina virtual se replica en local. A continuación se indican algunos de los criterios de una unidad de retención, sin los cuales la unidad no aparecerá para el servidor de destino maestro. a. El volumen no puede utilizarse para ningún otro propósito (como destino de replicación) b. El volumen no debe estar en modo de bloqueo. c. El volumen no debe ser el volumen de memoria caché. (La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizada del servidor de procesos más el destino maestro no es apto para el volumen de retención. Aquí el volumen del servidor de procesos más el destino maestro es el volumen de memoria caché del destino maestro). d. El tipo de sistema de archivos de volumen no debe ser FAT ni FAT32. e. La capacidad del volumen debe ser distinta de cero. e. Volumen de retención predeterminado para Windows es R. f. El volumen de retención predeterminado para Linux es /mnt/retention.

6.  La directiva de conmutación por recuperación será seleccionada automáticamente.

7.	Tras hacer clic en **Aceptar** para comenzar la reprotección, comienza un trabajo para replicar la máquina virtual de Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos**.

Si desea recuperar en una ubicación alternativa, seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino maestro. Cuando se conmuta por recuperación al sitio local, las máquinas virtuales de VMware del plan de protección de conmutación por recuperación utilizarán el mismo almacén de datos que el servidor de destino maestro. Si desea recuperar la réplica de la máquina virtual de Azure en la misma máquina virtual local, la máquina virtual local ya debe estar en el mismo almacén de datos que el servidor de destino maestro. Si no hay ninguna máquina virtual local, se creará una nueva durante la reprotección. ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)



También puede volver a proteger a nivel de un plan de recuperación. Si tiene un grupo de replicación, este solo se puede volver a proteger con un plan de recuperación. Al volver a proteger con un plan de recuperación, debe proporcionar los valores anteriores para cada máquina protegida.

>[AZURE.NOTE] Un grupo de replicación debe volver a protegerse con el mismo destino maestro. Si se ha vuelto a proteger con un servidor de destino maestro distinto, no se puede obtener para él un momento dado común.

### Ejecución de una conmutación por error en el sitio local

Después de volver a proteger una máquina virtual, puede iniciar una conmutación por error desde Azure a un sitio local.

1.	En la página de elementos duplicados, seleccione la máquina virtual que contiene el clic derecho a **Conmutación por error no planeada**.
2.	En **Confirmar conmutación por error**, compruebe la dirección de conmutación por error (de Azure) y seleccione el punto de recuperación que quiere usar para la conmutación por error (el último o el último coherente con la aplicación). El punto coherente con la aplicación estaría detrás del último momento dado y causará algunas pérdidas de datos.
3.	Durante la conmutación por error, se cerrarán las máquinas virtuales de Azure en Site Recovery. Después de comprobar que la conmutación por recuperación se ha completado como se esperaba, puede comprobar que las máquinas virtuales de Azure se han cerrado según lo previsto.

### Reprotección del sitio local

Una vez finalizada la conmutación por recuperación, debe confirmar la máquina virtual para asegurarse de que se eliminen las máquinas virtuales en Azure.

1. Haga clic con el botón derecho en el elemento protegido y haga clic en Confirmar. Se desencadenará un trabajo que quitará las anteriores máquinas virtuales recuperadas en Azure.

Una vez completada la confirmación, los datos vuelven al sitio local, pero no están protegidos. Para volver a iniciar la replicación en Azure, vuelva a hacer lo siguiente:

1.	En el Almacén > Configuración > Elementos replicados, seleccione las máquinas virtuales que se han conmutado por recuperación y haga clic en **Reproteger**.
2.  Asigne el valor de servidor de procesos que debe utilizarse para volver a enviar datos a Azure.
3.	Haga clic en Aceptar para volver a proteger el trabajo.

Al finalizar el proceso por el que se vuelve a proteger, la máquina virtual se volverá a replicar en Azure y puede realizar una conmutación por error.


### Problemas comunes en la conmutación por recuperación

1. Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, se ejecutará correctamente y la conmutación por error funcionará. En el momento de la reprotección, generará un error, ya que no se podrán detectar los almacenes de datos. Como síntoma, no verá los almacenes de datos enumerados mientras se esté volviendo a proteger. Para resolver este problema, puede actualizar las credenciales de vCenter con la cuenta adecuada que tenga permisos y tratar de realizar el trabajo de nuevo. [Más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Cuando conmuta por recuperación una máquina virtual de Linux y la ejecuta localmente, verá que se ha desinstalado el paquete del administrador de red de la máquina. Esto se debe a que cuando se recupera la VM en Azure, se elimina el paquete del administrador de red.
3. Cuando una máquina virtual se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP mediante DHCP. Al conmutar por recuperación a un entorno local, la VM seguirá utilizando DHCP para obtener la dirección IP. Tendrá que iniciar sesión manualmente en la máquina y configurar de nuevo la dirección IP como una estática, si así se requiere.
4. Si utiliza las ediciones gratuitas de ESXi 5.5 o vSphere Hypervisor 6, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Tendrá que actualizar el software con una licencia de evaluación para habilitar la conmutación por recuperación.

## Conmutación por recuperación con ExpressRoute

Puede conmutar por recuperación a través de una conexión VPN o de Azure ExpressRoute. Si desea usar ExpressRoute, tenga en cuenta lo siguiente:

- ExpressRoute se debe configurar en la red virtual de Azure a la que conmutarán por error las máquinas de origen, y en la que se encuentran las máquinas virtuales de Azure después de que tiene lugar este proceso.
- Los datos se replican en una cuenta de almacenamiento de Azure en un punto de conexión público. Para usar ExpressRoute, debe realizar la configuración entre pares públicos en ExpressRoute con el centro de datos de destino para la replicación de Site Recovery.

<!---HONumber=AcomDC_0713_2016-->