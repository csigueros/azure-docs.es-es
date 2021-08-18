---
title: Supervisión de un servicio en la nube de Azure (clásico) | Microsoft Docs
description: Se describe qué conlleva la supervisión de un servicio en la nube de Azure y cuáles son algunas de las opciones.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6148183962249b3dc1d446e62ff6fe8e48e608ac
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093740"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Introducción a la supervisión de servicios en la nube (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

Puede supervisar las métricas de rendimiento claves de cualquier servicio en la nube. Cada rol de servicio en la nube recopila datos mínimos: uso de CPU, uso de red y uso de disco. Si el servicio en la nube tiene la extensión `Microsoft.Azure.Diagnostics` aplicada a un rol, este último puede recopilar puntos de datos adicionales. En este artículo se ofrece una introducción a Azure Diagnostics para Cloud Services.

Con la supervisión básica, los datos del contador de rendimiento de las instancias de rol se muestrean y recopilan en intervalos de tres minutos. Los datos de la supervisión básica no se almacenan en la cuenta de almacenamiento y, por tanto, no tienen ningún costo adicional asociado.

Con la supervisión avanzada, las métricas adicionales se muestrean y recopilan en intervalos de cinco minutos, una hora y doce horas. Los datos agregados se almacenan en una cuenta de almacenamiento, en tablas, y se purgan después de diez días. La cuenta de almacenamiento utilizada se configura en función del rol; puede usar diferentes cuentas de almacenamiento para distintos roles. La configuración se realiza con una cadena de conexión en los archivos [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) y [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).


## <a name="basic-monitoring"></a>Supervisión básica

Como se mencionó en la introducción, un servicio en la nube recopila automáticamente los datos de la supervisión básica de la máquina virtual del host. Estos datos incluyen el porcentaje de CPU, la entrada y salida de red y la escritura/lectura de disco. Los datos de supervisión recopilados se muestran automáticamente en las páginas de información general y métricas del servicio en la nube, en Azure Portal. 

La supervisión básica no requiere una cuenta de almacenamiento. 

![Iconos de supervisión básica de un servicio en la nube](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Supervisión avanzada

La supervisión avanzada conlleva el uso de la extensión de **Azure Diagnostics** y, de forma opcional, del SDK de Application Insights en el rol que se desea supervisar. La extensión de Diagnostics usa un archivo de configuración (por rol) denominado **diagnostics.wadcfgx** para configurar las métricas de diagnóstico supervisadas. La extensión Azure Diagnostic recopila y almacena los datos en una cuenta de Azure Storage. Estos valores se configuran en los archivos **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) y [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg). Esto significa que existe un costo adicional asociado con la supervisión avanzada.

A medida que se crea cada rol, Visual Studio le agrega la extensión de Azure Diagnostics. Esta extensión de diagnósticos puede recopilar los siguientes tipos de información:

* Contadores de rendimiento personalizados
* Registros de aplicación
* Registros de eventos de Windows
* Origen de eventos de .NET
* Registros IIS
* ETW basado en manifiesto
* Volcados de memoria
* Registros de errores personalizados

> [!IMPORTANT]
> Aunque todos estos datos se agregan a la cuenta de almacenamiento, el portal **no** ofrece una forma nativa de crear gráficos de los datos. Se recomienda encarecidamente que integre otro servicio, como Application Insights, en la aplicación.

## <a name="setup-diagnostics-extension"></a>Configuración de la extensión Diagnostics

En primer lugar, si aún no tiene una cuenta de almacenamiento **clásica**, [cree una](../storage/common/storage-account-create.md). Asegúrese de que la cuenta de almacenamiento se crea con el **modelo de implementación clásica** especificado.

Después, vaya al recurso **Cuenta de almacenamiento (clásico)**. Seleccione **Configuración** > **Claves de acceso** y copie el valor de **Cadena de conexión principal**. Necesita este valor para el servicio en la nube. 

Debe modificar dos archivos de configuración para habilitar el diagnóstico avanzado, **ServiceDefinition.csdef** y **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

En el archivo **ServiceDefinition.csdef**, agregue una configuración nueva llamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada rol que usa el diagnóstico avanzado. Visual Studio agrega este valor al archivo al crear un proyecto. Si no existe, puede agregarlo ahora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

De esta forma se define una configuración nueva que debe agregarse a cada archivo **ServiceConfiguration.cscfg**. 

Lo más probable es que tenga dos archivos de configuración **.cscfg**, uno denominado **ServiceConfiguration.cloud.cscfg**, para implementar en Azure, y otro llamado **ServiceConfiguration.local.cscfg**, que se usa para las implementaciones locales en el entorno emulado. Abra y modifique cada archivo **.cscfg**. Agregue una configuración llamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Establezca el valor en la **cadena de conexión principal** de la cuenta de almacenamiento clásica. Si desea usar el almacenamiento local en el equipo de desarrollo, utilice `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Uso de Application Insights

Si publica el servicio en la nube desde Visual Studio, se le ofrece la opción de enviar datos de diagnóstico a Application Insights. Puede crear el recurso de Azure Application Insights en cualquier momento o enviar los datos a un recursos existente de Azure. Application Insights puede supervisar la disponibilidad, el rendimiento, los errores y el uso del servicio en la nube. Se pueden agregar gráficos personalizados a Application Insights, para poder ver los datos que más interesan. Los datos de instancias de rol pueden recopilarse con el SDK de Application Insights en el proyecto del servicio en la nube. Para más información sobre cómo integrar Application Insights, vea [Application Insights para Azure Cloud Services](../azure-monitor/app/cloudservices.md).

Tenga en cuenta que, aunque puede usar Application Insights para mostrar los contadores de rendimiento (y otra configuración) especificados mediante la extensión de Microsoft Azure Diagnostics, solo podrá disfrutar de una experiencia mejorada con la integración del SDK de Application Insights en los roles web y de trabajo.


## <a name="next-steps"></a>Pasos siguientes

- [Información sobre Application Insights con Cloud Services](../azure-monitor/app/cloudservices.md)
- [Configuración de contadores de rendimiento](diagnostics-performance-counters.md)




