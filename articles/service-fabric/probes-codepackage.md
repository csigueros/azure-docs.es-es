---
title: Sondeos de Azure Service Fabric
description: Aprenda a modelar sondeos de ejecución y preparación en Azure Service Fabric mediante archivos de manifiesto de servicio y aplicación.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 1f78499d6be8ee68011540abfba00a404b8c6ec5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059721"
---
# <a name="service-fabric-probes"></a>Sondeos de Service Fabric
Antes de continuar con este artículo, familiarícese con el [modelo de aplicación de Service Fabric][application-model-link] y con el [modelo de hospedaje de Service Fabric][hosting-model-link]. En este artículo se proporciona información general sobre cómo definir un sondeo de ejecución y preparación mediante archivos de manifiesto.

## <a name="liveness-probe"></a>Sondeo de ejecución
A partir de la versión 7.1, Azure Service Fabric admite un mecanismo de sondeo de ejecución para aplicaciones contenedorizadas y no contenedorizadas. Un sondeo de ejecución ayuda a notificar la ejecución de un paquete de código, que se reiniciará si no responde rápidamente.

## <a name="readiness-probe"></a>Sondeo de preparación
A partir de la versión 8.2, también se admite el sondeo de preparación. Un sondeo de preparación se usa para decidir si un paquete de código está listo para aceptar tráfico. Por ejemplo, si el contenedor tarda mucho tiempo en procesar la solicitud o si la cola de solicitudes está llena, el paquete de código no puede aceptar más tráfico y, por tanto, se eliminarán los puntos de conexión para llegar al paquete de código. 

El comportamiento del sondeo de preparación es:
1.  Se inicia la instancia del paquete de código o contenedor.
2.  Los puntos de conexión se publican inmediatamente.
3.  El sondeo de preparación comienza a ejecutarse.
4.  El sondeo de preparación finalmente alcanza el umbral de error y se elimina el punto de conexión, lo que hace que no esté disponible.
5.  La instancia finalmente está lista.
6.  El sondeo de preparación detecta que la instancia está lista y vuelve a publicar el punto de conexión.
7.  Las solicitudes se enrutan de nuevo correctamente, ya que estaba lista para atender las solicitudes.

> [!NOTE] 
> Para el sondeo de preparación, el paquete de código no se reinicia, solo los puntos de conexión no se publican, por lo que las réplicas o particiones establecidas no se verán afectadas.
>

## <a name="semantics"></a>Semántica
Solo puede especificar un sondeo de ejecución y uno de preparación por paquete de código y puede controlar su comportamiento con los campos siguientes:

* `type`: se usa para especificar si el tipo de sondeo es de ejecución o de preparación. Los valores admitidos son **Ejecución** o **Preparación**

* `initialDelaySeconds`: Retraso inicial en segundos para comenzar la ejecución del sondeo una vez que se ha iniciado el contenedor. El valor admitido es **int**. El valor predeterminado es 0 y el mínimo, 0.

* `timeoutSeconds`: Período en segundos tras el cual se considera que el sondeo es erróneo si no se ha completado correctamente. El valor admitido es **int**. El valor predeterminado es 1 y el mínimo, 1.

* `periodSeconds`: Período en segundos para especificar la frecuencia del sondeo. El valor admitido es **int**. El valor predeterminado es 10 y el mínimo, 1.

* `failureThreshold`: Cuando se alcance este valor, el contenedor se reiniciará. El valor admitido es **int**. El valor predeterminado es 3 y el mínimo, 1.

* `successThreshold`: En caso de error, para que el sondeo se considere correcto, debe ejecutarse correctamente para este valor. El valor admitido es **int**. El valor predeterminado es 1 y el mínimo, 1.

Puede haber, como máximo, un sondeo en un contenedor en cualquier momento. Si el sondeo no finaliza en el tiempo establecido en **timeoutSeconds**, espere y cuente el tiempo hasta **failureThreshold**. 

Además, Service Fabric generará los siguientes [informes de mantenimiento][health-introduction-link] de sondeo en **DeployedServicePackage**:

* `OK`:El sondeo se realiza correctamente para el valor establecido en **successThreshold**.

* `Error`: El sondeo **failureCount** ==  **failureThreshold** antes de que se reinicie el contenedor.

* `Warning`: 
    * Se produce un error en el sondeo y **failureCount** < **failureThreshold**. Este informe de mantenimiento se conserva hasta que **failureCount** alcanza el valor definido en **failureThreshold** o **successThreshold**.
    * Si se ejecuta correctamente después de un error, la advertencia se conserva, pero con los éxitos consecutivos actualizados.

## <a name="specifying-a-probe"></a>Especificar un sondeo

Puede especificar un sondeo en el archivo ApplicationManifest.xml en **ServiceManifestImport**.

El sondeo se puede realizar para cualquiera de estas opciones:

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>Sondeo HTTP

En el caso de un sondeo HTTP, Service Fabric enviará una solicitud HTTP al puerto y la ruta de acceso que especifique. Un código de retorno mayor o igual que 200 y menor que 400 indica que se ha completado correctamente.

Este es un ejemplo de cómo especificar un sondeo de ejecución de HTTP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

El sondeo HTTP tiene propiedades adicionales que puede establecer:

* `path`: Ruta de acceso que se usará en la solicitud HTTP.

* `port`: Puerto que se usará para los sondeos. Esta propiedad es obligatoria. El rango es de 1 a 65535.

* `scheme`: Esquema que se usará para la conexión al paquete de código. Si esta propiedad se establece en HTTPS, se omite la verificación de certificados. El valor predeterminado es HTTP.

* `httpHeader`: Encabezados que se definirán en la solicitud. Puede especificar varios encabezados.

* `host`: Dirección IP del host al que se conectará.

### <a name="tcp-probe"></a>Sondeo TCP

En el caso de un sondeo TCP, Service Fabric intentará abrir un socket en el contenedor mediante el puerto especificado. Si puede establecer una conexión, el sondeo se considera correcto. Este es un ejemplo de cómo especificar el sondeo que usa un socket TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Sondeo exec

Este sondeo emitirá un comando **exec** en el contenedor y esperará a que finalice.

> [!NOTE]
> El comando **exec** toma una cadena separada por comas. El comando del siguiente ejemplo funcionaría para un contenedor Linux.
> Si está intentando un sondeo en un contenedor Windows, use **cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener información relacionada, consulte el artículo siguiente:
* [Service Fabric y contenedores][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

