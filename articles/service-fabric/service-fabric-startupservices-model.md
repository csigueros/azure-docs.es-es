---
title: Definición de la configuración de servicio de StartupServices.xml para una aplicación de Service Fabric
description: Aprenda a usar StartupServices.xml para separar la configuración de nivel de servicio de ApplicationManifest.xml.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 69ec795bab910f8f2b030ab5758698d3fdbae824
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681382"
---
# <a name="introducing-startupservicesxml-in-service-fabric-application"></a>Introducción a StartupServices.xml en una aplicación de Service Fabric
Esta característica presenta el archivo StartupServices.xml en un diseño de aplicación de Service Fabric. Este archivo hospeda la sección DefaultServices de ApplicationManifest.xml. Con esta implementación, los parámetros DefaultServices y relacionados con la definición de servicio se mueven del archivo ApplicationManifest.xml existente a este nuevo archivo denominado StartupServices.xml. Este archivo se usa en cada función (Compilación/Recompilación/F5/Ctrl+F5/Publicación) en Visual Studio.

Nota: StartupServices.xml solo está pensado para implementaciones de Visual Studio y tiene como finalidad garantizar que los paquetes implementados con Visual Studio (con StartupServices.xml) no tengan conflictos con los servicios implementados mediante ARM. StartupServices.xml no forma parte de un paquete de aplicación. No se admite en una canalización de DevOps, y el cliente debe implementar cada servicio en la aplicación, ya sea a través de ARM o mediante cmdlets con la configuración deseada.

## <a name="existing-service-fabric-application-design"></a>Diseño de aplicación de Service Fabric existente
En cada aplicación de Service Fabric, ApplicationManifest.xml es el origen de toda la información relacionada con el servicio de la aplicación. ApplicationManifest.xml consta de todas las secciones: Parameters, ServiceManifestImport y DefaultServices. Los parámetros de configuración se mencionan en Cloud.xml/Local1Node.xml/Local5Node.xml en ApplicationParameters.

Cuando se agrega un nuevo servicio en una aplicación, para este nuevo parámetro de servicio se agregan las secciones Parameters, ServiceManifestImport y DefaultServices dentro de ApplicationManifest.xml. Los parámetros de configuración se agregan Cloud.xml/Local1Node.xml/Local5Node.xml en ApplicationParameters.

Cuando el usuario hace clic en la función Compilar/Recompilar de Visual Studio, la modificación de las secciones ServiceManifestImport, Parameters y DefaultServices se produce en ApplicationManifest.xml. Los parámetros de configuración también se editan en Cloud.xml/Local1Node.xml/Local5Node.xml en ApplicationParameters.

Cuando el usuario desencadena F5/Ctrl+F5/Publicar, la aplicación y los servicios se implementan o se publican en función de la información de ApplictionManifest.xml.  Los parámetros de configuración se usan desde cualquiera de los archivos Cloud.xml/Local1Node.xml/Local5Node.xml en ApplicationParameters.

![Diseño existente para una aplicación de Service Fabric][exisiting-design-diagram]

Archivo ApplicationManifest.xml de ejemplo 

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
    <Parameter Name="Web1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Web1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Web1Type" TargetReplicaSetSize="[Web1_TargetReplicaSetSize]" MinReplicaSetSize="[Web1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Web1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="new-service-fabric-application-design-with-startupservicesxml"></a>Nuevo diseño de aplicación de Service Fabric con StartupServices.xml
En este diseño, hay una distinción clara entre la información de nivel de servicio (por ejemplo, la definición de servicio y los parámetros de servicio) y la información de nivel de aplicación (ServiceManifestImport y ApplicationParameters). StartupServices.xml contiene toda la información de nivel de servicio, mientras que ApplicationManifest.xml contiene toda la información de nivel de aplicación. Otro cambio introducido es la adición de Cloud.xml/Local1Node.xml/Local5Node.xml en StartupServiceParameters, que solo tiene parámetros de configuración de servicio. El archivo Cloud.xml/Local1Node.xml/Local5Node.xml existente en ApplicationParameters contiene solo la configuración de los parámetros de nivel de aplicación.

Cuando se agrega un nuevo servicio en la aplicación, los parámetros de nivel de aplicación y ServiceManifestImport se agregan en ApplicationManifest.xml. Los parámetros de configuración de la aplicación se agregan en Cloud.xml/Local1Node.xml/Local5Node.xml en ApplicationParameters. La información de servicio y los parámetros de servicio se agregan en StartupServices.xml y los parámetros de configuración de servicio se agregan en Cloud.xml/Local1Node.xml/Local5Node.xml en StartupServiceParameters.

Durante la compilación o recompilación del proyecto, tiene lugar la modificación de los parámetros de aplicación de ServiceManifestImport en ApplicationManifest.xml. La configuración de los parámetros de aplicación también se edita en los archivos Cloud.xml/Local1Node.xml/Local5Node.xml en ApplicationParameters. La información relacionada con el servicio se edita en StartupServices.xml y los parámetros de servicio se editan en Cloud.xml/Local1Node.xml/Local5Node.xml en StartupServiceParameters.

Cuando se desencadena F5/Ctrl+F5/Publicar en Visual Studio, la aplicación se implementa o publica en función de la información de ApplictionManifest.xml Cloud.xml y de los parámetros de aplicación de cualquiera de los archivos Cloud.xml/Local1Node.xml/Local5Node.xml de ApplicationParameters. Cada servicio se inicia individualmente con información de servicio de StartupServices.xml y la configuración de los parámetros de servicio de cualquiera de los archivos Cloud.xml/Local1Node.xml/Local5Node.xml en StartupServiceParameters.

![Nuevo diseño para una aplicación de Service Fabric con StartupServices.xml][new-design-diagram]

Estos parámetros de servicio y parámetros de aplicación se pueden editar antes de publicar una aplicación (hacer clic con el botón derecho > Publicar), como se muestra en la imagen.

![Opción Publicar en Nuevo diseño][publish-application]

Archivo ApplicationManifest.xml de ejemplo en el nuevo diseño
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
</ApplicationManifest>
```

Archivo StartupServices.xml ejemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<StartupServicesManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                         xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <Services>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </Services>
</StartupServicesManifest>
```

La característica startupServices.xml está habilitada para todos los proyectos nuevos de la versión 5.0.516.9590 y posteriores del SDK. En el caso de los servicios de actor, se habilita en la versión de NuGet 5.0.516 y posteriores de Microsoft.ServiceFabric.Actors. Los proyectos creados con la versión anterior del SDK son totalmente compatibles con el SDK más reciente. No se admite la migración de proyectos antiguos al nuevo diseño. Si el usuario quiere crear una aplicación de Service Fabric sin StartupServices.xml en la versión más reciente del SDK, debe hacer clic en el vínculo "Help me choose a project template" (Ayudarme a elegir una plantilla de proyecto), tal como se muestra en la imagen siguiente.

![Opción Crear nueva aplicación en Nuevo diseño][create-new-project]


## <a name="next-steps"></a>Pasos siguientes
- Aprenda sobre el [modelo de aplicación de Service Fabric](service-fabric-application-model.md).
- Aprenda sobre [manifiestos de aplicaciones y servicios de Service Fabric](service-fabric-application-and-service-manifests.md).

<!--Image references-->
[exisiting-design-diagram]: ./media/service-fabric-startupservices/existing-design.png
[new-design-diagram]: ./media/service-fabric-startupservices/new-design.png
[publish-application]: ./media/service-fabric-startupservices/publish-application.png
[create-new-project]: ./media/service-fabric-startupservices/create-new-project.png

