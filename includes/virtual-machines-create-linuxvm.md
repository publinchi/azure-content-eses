
1. Conéctese a su suscripción de Azure mediante los pasos enumerados en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../articles/xplat-cli-connect.md).

2. Asegúrese de que está en el modo Administración de servicios, para lo que debe usar:

        azure config mode asm

3. Busque la imagen de Linux que desea cargar entre las imágenes disponibles:

        azure vm image list | grep "Linux"

   En una ventana de símbolo del sistema de Windows, use **find** en lugar de grep.

4. Use `azure vm create` para crear una máquina virtual con la imagen de Linux de la lista anterior. Este paso crea un nuevo servicio én la nube, así como una cuenta de almacenamiento nueva. Esta máquina virtual también se puede conectar a un servicio de nube existente con una opción `-c`. También crea un punto de conexión SSH para iniciar sesión en la máquina virtual de Linux con la opción `-e`.

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-es-ES-30GB "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-es-ES-30GB
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] En el caso de una máquina virtual de Linux, es preciso especificar la opción `-e` en`vm create`; SSH no se puede habilitar una vez creada la máquina virtual. Para obtener más información sobre SSH, vea [Uso de SSH con Linux en Azure](virtual-machines-linux-ssh-from-linux.md).

    Tenga en cuenta que la imagen *b39f27a8b8c64d52b05eac6a62ebad85\_\_Ubuntu-14\_04\_4-LTS-amd64-server-20160516-es-ES-30GB* es la elegida de la lista de imágenes en el paso anterior. *MyTestVM* es el nombre de la nueva máquina virtual, y *adminUser* es el nombre de usuario que se utilizará para SSH en la máquina virtual. Estás variables se pueden reemplazar en función de los requisitos. Para obtener más información sobre este comando, vaya a [Uso de la CLI de Azure con administración de servicios de Azure](virtual-machines-command-line-tools.md).

5. La máquina virtual con Linux recién creada aparecerá en la lista proporcionada por:

        azure vm list

6. Los atributos de la máquina virtual se pueden comprobar con el comando:

        azure vm show MyTestVM

7. La máquina virtual recién creada está lista para iniciarse con el comando `azure vm start`.

## Pasos siguientes
Para obtener más información sobre estos comandos de la máquina virtual de la CLI de Azure, vea [Uso de la CLI de Azure con la API de administración de servicios](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0608_2016-->