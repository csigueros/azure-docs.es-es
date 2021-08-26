---
title: Especificación de los puntos de conexión de servicio de Service Fabric
description: Cómo describir los recursos de punto de conexión en un manifiesto de servicio, incluida la configuración de puntos de conexión HTTPS
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 66474543834f97c409d7572d54c7850ddc57faa0
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778652"
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificación de los recursos en un manifiesto de servicio
## <a name="overview"></a>Información general
Las aplicaciones y servicios de Service Fabric se definen y permiten la creación de versiones mediante archivos de manifiesto. Para obtener información general de nivel superior de ServiceManifest.xml y ApplicationManifest.xml, consulte [Manifiestos de servicio y de aplicación de Service Fabric](service-fabric-application-and-service-manifests.md).

El manifiesto de servicio permite que los recursos que utilizará el servicio se declaren o modifiquen sin cambiar el código compilado. Service Fabric admite la configuración de recursos de puntos de conexión para el servicio. El acceso a los recursos especificados en el manifiesto de servicio puede controlarse a través de SecurityGroup en el manifiesto de aplicación. La declaración de recursos permite cambiar estos recursos durante la implementación, lo que significa que no es necesario que el servicio introduzca un nuevo mecanismo de configuración. La definición de esquema para el archivo ServiceManifest.xml se instala con el SDK y las herramientas de Service Fabric en *C:\Archivos de programa\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* y está documentado en la [documentación del esquema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Puntos de conexión
Cuando se define un recurso de punto de conexión en el manifiesto de servicio, Service Fabric asigna puertos desde el intervalo de puertos reservados de aplicación cuando un puerto no se especifica expresamente. Por ejemplo, analice el punto de conexión *ServiceEndpoint1* especificado en el fragmento de manifiesto que encontrará después de este párrafo. Además, los servicios también pueden solicitar un puerto específico en un recurso. Es posible asignar números de puerto diferentes a réplicas de servicio que se ejecutan en nodos de clúster, mientras que las réplicas del mismo servicio que se ejecuta en el mismo nodo comparten el mismo puerto. Las réplicas de servicio pueden usar estos puertos según sea necesario para la replicación y procesar solicitudes de cliente.

Al activar un servicio que especifica un punto de conexión HTTPS, Service Fabric establecerá la entrada del control de acceso para el puerto, enlazará el certificado del servidor especificado al puerto y concederá la identidad que el servicio ejecuta como permisos para la clave privada del certificado. El flujo de activación se invocará cada vez que se inicie Service Fabric o cuando se cambie la declaración de certificado de la aplicación mediante una actualización. También se supervisará el certificado del punto de conexión en busca de cambios o renovaciones, y los permisos se volverán a aplicar periódicamente según sea necesario.

Tras la finalización del servicio, Service Fabric borrará la entrada de control de acceso del punto de conexión y eliminará el enlace de certificado. Sin embargo, no se borrarán los permisos aplicados a la clave privada del certificado.

> [!WARNING] 
> Por naturaleza, los puertos estáticos no deben superponerse con el intervalo de puertos de la aplicación especificado en ClusterManifest. Si especifica un puerto estático, asígnelo fuera de este intervalo o se producirán conflictos entre los puertos. Con la versión 6.5CU2, emitiremos una **advertencia de estado** cuando detectemos este tipo de conflicto, pero dejaremos que la implementación siga sincronizándose con el comportamiento de 6.5 incluido. Sin embargo, podemos evitar la implementación de la aplicación con las siguientes versiones principales.
>
> Con la versión 7.0, se emitirá una **advertencia de mantenimiento** cuando se detecte que el uso de un intervalo de puertos de aplicación va más allá de HostingConfig::ApplicationPortExhaustThresholdPercentage(default 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Si hay varios paquetes de código en un solo paquete de servicio, también necesita hacer referencia al paquete de código en la sección **Puntos de conexión**.  Por ejemplo, si **ServiceEndpoint2a** y **ServiceEndpoint2b** son los puntos de conexión del mismo paquete de servicio y hacen referencia a paquetes de código diferentes, el paquete de código correspondiente a cada punto de conexión se indica de la siguiente manera:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Vea [Configuración de Reliable Services con estado](service-fabric-reliable-services-configuration.md) para obtener más información sobre cómo hacer referencia a los puntos de conexión del archivo de configuración del paquete de configuración (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Ejemplo: Especificación de un punto de conexión HTTP para el servicio
El siguiente manifiesto de servicio define un recurso de punto de conexión TCP y dos recursos de punto de conexión HTTP en el elemento &lt;Recursos&gt;.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Ejemplo: Especificación de un punto de conexión HTTPS para el servicio
El protocolo HTTPS ofrece autenticación de servidor y también se usa para cifrar la comunicación entre cliente y servidor. Para habilitar HTTPS en el servicio Service Fabric, especifique el protocolo en la sección *Recursos -> Puntos de conexión -> Punto de conexión* del manifiesto de servicio, como se mostró anteriormente para el punto de conexión *ServiceEndpoint3*.

> [!NOTE]
> No se puede cambiar el protocolo de un servicio durante la actualización de la aplicación. Si se modifica en el transcurso, se tratará de un cambio brusco.
> 

> [!WARNING] 
> Si usa HTTPS, no utilice el mismo puerto y certificado para distintas instancias de servicio (independientes de la aplicación) implementadas en el mismo nodo. Al actualizar dos servicios diferentes que usan el mismo puerto en diferentes instancias de aplicación, se producirá un error de actualización. Para más información, consulte [Actualización de varias aplicaciones con puntos de conexión HTTPS](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Este es un ejemplo de ApplicationManifest que muestra la configuración necesaria para un punto de conexión HTTPS. El certificado del servidor o del punto de conexión se puede declarar mediante huella digital o el nombre común del firmante, y se debe proporcionar un valor. EndpointRef es una referencia a EndpointResource en ServiceManifest, cuyo protocolo se debe haber establecido en el protocolo "https". Puede agregar más de un certificado EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

En los clústeres de Linux, el valor predeterminado del almacén **MY** es la carpeta **/var/lib/sfcerts**.

Para ver un ejemplo de una aplicación completa que utiliza un punto de conexión HTTPS, consulte [Incorporación de un punto de conexión HTTPS a un servicio de front-end de ASP.NET Core Web API mediante Kestrel](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>Realización de ACL en el puerto para puntos de conexión HTTP
Service Fabric agregará automáticamente los puntos de conexión ACL HTTP(S) especificados de forma predeterminada. **No** realizará un ACL de forma automática si un punto de conexión no tiene una propiedad [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) asociada y Service Fabric está configurado para ejecutarse con una cuenta con privilegios de administrador.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Invalidación de Endpoints en ServiceManifest.xml

En ApplicationManifest, agregue una sección denominada ResourceOverrides, que será un elemento del mismo nivel que la sección ConfigOverrides. En esta sección puede especificar la invalidación de la sección Endpoints en la sección de recursos especificada en el manifiesto del servicio. Se admite el reemplazo de puntos de conexión en el entorno en tiempo de ejecución 5.7.217/SDK 2.7.217 y versiones posteriores.

Para invalidar punto de conexión en ServiceManifest mediante ApplicationParameters, cambie ApplicationManifest como se indica a continuación:

En la sección ServiceManifestImport, agregue una nueva sección "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

En Parameters, agregue a continuación:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Al implementar la aplicación, estos valores se pueden pasar como ApplicationParameters.  Por ejemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: Si algún valor proporcionado para un ApplicationParameter está vacío, se vuelve al valor predeterminado proporcionado en ServiceManifest para el EndPointName correspondiente.

Por ejemplo:

Si en ServiceManifest ha especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Supongamos que el valor de Port1 y Protocol1 en ApplicationParameters es nulo o está vacío. El puerto lo decidirá ServiceFabric y el protocolo será TCP.

Imagine que especifica un valor incorrecto. Supongamos que para el puerto se ha especificado un valor de cadena "Foo" en lugar de un valor entero.  El comando New-ServiceFabricApplication generará un error: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Pasos siguientes

En este artículo se explica cómo definir puntos de conexión en el manifiesto de servicio de Service Fabric. Para obtener ejemplos detallados, consulte:

> [!div class="nextstepaction"]
> [Ejemplos de manifiestos de aplicación y servicio](service-fabric-manifest-examples.md)

Para ver un tutorial sobre cómo empaquetar e implementar una aplicación existente en un clúster de Service Fabric, consulte:

> [!div class="nextstepaction"]
> [Empaquetado e implementación de un ejecutable existente en Service Fabric](service-fabric-deploy-existing-app.md)
