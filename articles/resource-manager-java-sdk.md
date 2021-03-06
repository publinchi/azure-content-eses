<properties
   pageTitle="SDK del Administrador de recursos para .Java| Microsoft Azure"
   description="Información general sobre la autenticación del SDK de Java del Administrador de recursos y ejemplos de uso"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/16/2016"
   ms.author="navale;tomfitz;"/>
   
# SDK del Administrador de recursos de Azure para Java
   
Los SDK de versión preliminar del Administrador de recursos de Azure (ARM) están disponibles para varios idiomas y plataformas. Cada una de estas implementaciones de lenguajes están disponibles a través de sus administradores de paquetes del ecosistema y GitHub.

El código de cada uno de estos SDK se genera en [Azure RESTful API specifications](https://github.com/azure/azure-rest-api-specs) (Especificaciones de la API de RESTful de Azure). Estas especificaciones son de código abierto y están basadas en la especificación de Swagger v2. El código del SDK se genera mediante un proyecto de código abierto denominado [AutoRest](https://github.com/azure/autorest). AutoRest transforma estas especificaciones de la API de RESTful en bibliotecas de clientes en múltiples lenguajes. Si en los SDK no hay ningún aspecto del código generado que quiera mejorar, todo el conjunto de herramientas para crear los SDK está abierto, disponible libremente y se basa en el formato de especificación de API ampliamente adoptado.

El SDK de Java del Administrador de recursos de Azure se hospeda en el [repositorio del SDK de Java de Azure](https://github.com/azure/azure-sdk-for-java) de GitHub. Tenga en cuenta que, en el momento de escribir este artículo, el SDK se encuentra en versión preliminar. Están disponibles los siguientes paquetes:

* Administración de procesos: (azure-mgmt-compute)
* Administración de DNS: (azure-mgmt-dns)
* Administración de red: (azure-mgmt-network)
* Administración de recursos: (azure-mgmt-resources)
* Administración de SQL: (azure-mgmt-sql)
* Administración de almacenamiento: (azure-mgmt-storage)
* Administración del Administrador de tráfico: (azure-mgmt-traffic-manager)
* Utilidades y aplicaciones auxiliares: (azure-mgmt-utility)
* Sitios web y administración de aplicaciones web: (azure-mgmt-websites)

Siga el SDK de Azure para la [página Wiki sobre características de Java](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features) para obtener una lista actualizada.

## Requisitos previos
1. Java v1.6+
2. [Maven](https://maven.apache.org/) si desea desarrollar el SDK

## Obtención del SDK
Los archivos JAR distribuidos por [Maven](https://maven.apache.org/) son la forma recomendada de introducción al SDK de Java de Azure. Puede agregar estas dependencias a muchas de las herramientas de administración de dependencias de Java (Maven, Gradle, Ivy...). Siga este [vínculo](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22) para obtener una lista de las bibliotecas disponibles en Maven.

Como alternativa, puede obtener el sdk directamente desde el origen mediante GIT. Para obtener el código fuente del SDK mediante GIT:

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(Los ejemplos de esta introducción utilizarán Maven como el origen de los paquetes del SDK).

El SDK incluye ejemplos de algunos de los escenarios: autenticación, aprovisionamiento de una máquina virtual y mucho más. Todos se pueden encontrar en el repositorio de GitHub [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples).

## Clases auxiliares

El SDK incluye clases auxiliares para varios de los paquetes principales. Las clases auxiliares se implementan en el paquete azure-mgmt-utility:

* AuthHelper: clase auxiliar de autenticación
* ComputeHelper: clase auxiliar de proceso
* NetworkHelper: clase auxiliar de red
* ResourceHelper: clase auxiliar de implementaciones
* StorageHelper: clase auxiliar de almacenamiento

**Información de dependencia de Maven**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

Usará la versión 0.9.1 del paquete de utilidades.

## Autenticación

La autenticación para ARM se controla mediante Azure Active Directory (AD). Para conectarse a cualquier API, primero debe autenticarse con Azure AD para recibir un token de autenticación que pueda pasar a cada solicitud. Para obtener este token, primero debe crear lo que se denomina una aplicación de Azure AD y una entidad de servicio que se usará para iniciar la sesión. Siga las instrucciones paso a paso que encontrará en [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](./resource-group-create-service-principal-portal.md).

Después de crear la entidad de servicio, debe tener:

* Identificador de cliente (GUID)
* Secreto del cliente (cadena)
* Identificador de inquilino (GUID) o nombre de dominio (cadena)

Cuando tenga estos valores, puede obtener un token de acceso de Active Directory, válido durante una hora.

El SDK de Java incluye una clase auxiliar AuthHelper que crea el token de acceso, una vez que haya proporcionado el identificador de cliente, el secreto y el identificador de inquilino. En el siguiente ejemplo, en la clase [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java), se usa el método *getAccessTokenFromServicePrincipalCredentials* de AuthHelper para obtener el token de acceso:

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## Creación de una máquina virtual 
El paquete de utilidades incluye una clase auxiliar [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) para crear una máquina virtual. Puede encontrar algunos ejemplos para trabajar con máquinas virtuales en el paquete azure-mgmt-samples, en [compute](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute).

A continuación se muestra un flujo simple para crear una máquina virtual. En este ejemplo, la clase auxiliar creará el almacenamiento y la red como parte de la creación de la máquina virtual:

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## Implementación de una plantilla
La clase [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) se creó para facilitar el proceso de implementación de una plantilla ARM con el SDK de Java.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```
## Lista de todas las máquinas virtuales
No hace falta que use las clases auxiliares (aunque pueden facilitarle las cosas), pero puede usar directamente las clases de servicio para cada proveedor de recursos. En este ejemplo se enumeran algunos de los recursos en la suscripción autenticada. Para cada grupo de recursos, busque las máquinas virtuales y las direcciones IP asociadas a ellas.

```java
// authenticate and get access token
Configuration config = createConfiguration();
ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

// list all resource groups     
ArrayList<ResourceGroupExtended> resourceGroups = resourceManagementClient.getResourceGroupsOperations().list(null).getResourceGroups();
for (ResourceGroupExtended resourcesGroup : resourceGroups) {
   String rgName = resourcesGroup.getName();
   System.out.println("Resource Group: " + rgName);
   
   // list all virtual machines
   ArrayList<VirtualMachine> vms = computeManagementClient.getVirtualMachinesOperations().list(rgName).getVirtualMachines();
   for (VirtualMachine vm : vms) {
      System.out.println("    VM: " + vm.getName());
      // list all nics
      ArrayList<NetworkInterfaceReference> nics = vm.getNetworkProfile().getNetworkInterfaces();
      for (NetworkInterfaceReference nicReference : nics) {
         String[] nicURI = nicReference.getReferenceUri().split("/");
         NetworkInterface nic = networkResourceProviderClient.getNetworkInterfacesOperations().get(rgName, nicURI[nicURI.length - 1]).getNetworkInterface();
         System.out.println("        NIC: " + nic.getName());
         System.out.println("        Is primary: " + nic.isPrimary());
         ArrayList<NetworkInterfaceIpConfiguration> ips = nic.getIpConfigurations();

         // find public ip address
         for (NetworkInterfaceIpConfiguration ipConfiguration : ips) {
               System.out.println("        Private IP address: " + ipConfiguration.getPrivateIpAddress());
               String[] pipID = ipConfiguration.getPublicIpAddress().getId().split("/");
               PublicIpAddress pip = networkResourceProviderClient.getPublicIpAddressesOperations().get(rgName, pipID[pipID.length - 1]).getPublicIpAddress();
               System.out.println("        Public IP address: " + pip.getIpAddress());
         }
      }
}  
```

Encontrará más ejemplos en los paquetes de ejemplos en [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments).

## Lecturas adicionales y ayuda
Documentación del SDK de Azure para Java: [documentos de Java](http://azure.github.io/azure-sdk-for-java/)

Si se producen errores con el SDK, envíe el problema a través de [Issues](https://github.com/Azure/azure-sdk-for-java/issues) (Problemas) o consulte [Azure Java SDK en StackOverflow](http://stackoverflow.com/questions/tagged/azure-java-sdk).

<!---HONumber=AcomDC_0323_2016-->