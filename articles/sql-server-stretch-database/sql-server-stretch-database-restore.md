<properties
	pageTitle="Restauración de bases de datos habilitadas para Stretch | Microsoft Azure"
	description="Obtenga información sobre cómo restaurar bases de datos habilitadas para Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="douglasl"/>

# Restauración de bases de datos habilitadas para Stretch

Restaure una copia de seguridad de la base de datos cuando sea necesario para recuperarla de muchos tipos de errores y desastres.

Para obtener más información sobre la copia de seguridad, consulte [Copia de seguridad de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] La copia de seguridad solo es uno de los componentes que conforman una solución de continuidad empresarial y de alta disponibilidad. Para obtener más información sobre la alta disponibilidad, consulte [Soluciones de alta disponibilidad (SQL Server)](https://msdn.microsoft.com/library/ms190202.aspx).

## Restauración de los datos de SQL Server
Para recuperarse de un error de hardware o daños, restaure la base de datos de SQL Server habilitada para Stretch a partir de una copia de seguridad. Aún puede seguir utilizando los métodos de restauración de SQL Server que utiliza actualmente. Para obtener más información, consulte [Información general sobre restauración y recuperación (SQL Server)](https://msdn.microsoft.com/library/ms191253.aspx).

Después de restaurar la base de datos de SQL Server, tendrá que ejecutar el procedimiento almacenado **sys.sp\_rda\_reauthorize\_db** para volver a establecer la conexión entre la base de datos de SQL Server habilitada para Stretch y la de Azure remota. Para obtener más información, consulte [Restauración de la conexión entre la base de datos de SQL Server y la de Azure remota](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database).

## Restauración de los datos de Azure remotos

### Recuperación de una base de datos de Azure activa
El servicio SQL Server Stretch Database de Azure crea instantáneas de todos los datos activos cada 8 horas como mínimo mediante instantáneas de Almacenamiento de Azure. Estas instantáneas se mantienen durante 7 días. Esto permite restaurar los datos a uno de los 21 puntos anteriores como mínimo de los últimos 7 días hasta la hora en que se tomó la última instantánea.

Para restaurar una base de datos de Azure activa a un estado anterior mediante el Portal de Azure, siga estos pasos.

1. Inicie sesión en el Portal de Azure.
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la base de datos y selecciónela.
4. En la parte superior de la hoja de la base de datos, haga clic en **Restaurar**.
5. Especifique un nuevo **nombre de base de datos**, seleccione un **punto de restauración** y, después, haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

### Recuperación de una base de datos de Azure eliminada
El servicio SQL Server Stretch Database de Azure toma una instantánea de la base de datos antes de eliminarla y la conserva durante 7 días. Cuando esto pasa, ya no conserva las instantáneas de la base de datos activa. De este modo, podrá restaurar una base de datos eliminada al momento en que se suprimió.

Para restaurar una base de datos de Azure eliminada al momento en que se suprimió mediante el Portal de Azure, siga estos pasos.

1. Inicie sesión en el Portal de Azure.
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Servidores SQL**.
3. Vaya al servidor y selecciónelo.
4. Desplácese hacia abajo hasta la opción Operaciones de la hoja del servidor y haga clic en el icono de **Bases de datos eliminadas**.
5. Seleccione la base de datos eliminada que desee restaurar.
5. Especifique un nuevo **nombre de base de datos** y haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**.

## Restauración de la conexión entre la base de datos de SQL Server y la de Azure remota

1.  Si va a conectarse a una base de datos de Azure restaurada con un nombre diferente o en una región diferente, ejecute el procedimiento almacenado [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx) para desconectarse de la base de datos de Azure anterior.

2.  Ejecute el procedimiento almacenado [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx) para volver a conectar la base de datos local habilitada para Stretch a la de Azure.

	-   Proporcione la credencial existente con ámbito de base de datos como un valor sysname o varchar (128). (No se varchar(max).) Puede buscar el nombre de la credencial en la vista **sys.database\_scoped\_credentials**.

	-   Especifique si desea hacer una copia de los datos remotos y conectarse a ella (recomendado).

    ```tsql  
    USE <Stretch-enabled database name>;
	GO
	EXEC sp_rda_reauthorize_db
	    @credential = N'<existing_database_scoped_credential_name>',
		@with_copy = 1 ;  
	GO
	```  

## Consulte también

[Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Copia de seguridad y restauración de bases de datos de SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0706_2016-->