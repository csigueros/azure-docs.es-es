---
title: Migración de un recurso de proceso de Azure desde Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de proceso de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 3662039dad5d85c87c2598fb59b7719ab9251090
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029144"
---
# <a name="migrate-compute-resources-to-global-azure"></a>Migración de recursos de proceso a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos de proceso de Azure desde Azure Alemania a Azure global.

## <a name="compute-iaas"></a>IaaS de proceso

No puede migrar directamente los recursos de infraestructura como servicio de proceso de Azure desde Azure Alemania a Azure global. Sin embargo, hay varias maneras de "duplicar" las máquinas virtuales.

### <a name="duplicate-by-using-site-recovery"></a>Duplicación mediante Site Recovery

Azure Site Recovery puede ayudarle a migrar las máquinas virtuales de Azure Alemania a Azure global. Debido a que el origen y el destino están en diferentes inquilinos en una migración de Azure Alemania a Azure global, no puede usar la opción normal de recuperación de ante desastres de Azure que está disponible para las máquinas virtuales. El truco consiste en configurar un almacén de Site Recovery en el entorno de destino (Azure global) y continuar como si estuviera trasladando un servidor físico a Azure. En Azure Portal, seleccione una ruta de replicación etiquetada como **No virtualizado**. Cuando finalice la replicación, realice una conmutación por error.

> [!NOTE]
> Los siguientes pasos son los mismos que seguiría para migrar un servidor físico que se está ejecutando en el entorno local a Azure.

Si quiere saber más, consulte este [útil tutorial de Site Recovery](../site-recovery/physical-azure-disaster-recovery.md). Para obtener una introducción rápida, esta es una versión más corta y ligeramente adaptada del proceso:

Instale un servidor de configuración o proceso en su entorno de origen para crear las imágenes del servidor. A continuación, replique las imágenes en el almacén de Azure Recovery Services en su entorno de destino. El trabajo lo realiza el servidor de configuración. No necesita tener acceso a los servidores individuales.

1. Inicie sesión en el portal de Azure Alemania.
1. Compare las versiones del sistema operativo de las máquinas virtuales que desea migrar con la [matriz de compatibilidad](../site-recovery/vmware-physical-secondary-support-matrix.md).
1. Configure una nueva máquina virtual en la instancia de Azure Virtual Network de origen para que sirva de servidor de configuración:
   1. Seleccione **DS4v3** o superior (de 4 a 8 núcleos, 16 GB de memoria).
   1. Conecte un disco adicional que tenga al menos 1 TB de espacio disponible (para las imágenes de máquina virtual).
   1. Use Windows Server 2012 R2 o versiones posteriores.
1. Asegúrese de que los puertos 443 y 9443 estén abiertos para la subred en ambas direcciones.
1. Inicie sesión en la nueva máquina virtual (ConfigurationServer).
1. En la sesión de escritorio remoto, inicie sesión en el portal de Azure global con sus credenciales de Azure global.
1. Configure una red virtual en el que se ejecutarán las máquinas virtuales replicadas.
1. Cree una cuenta de Azure Storage.
1. Configure el almacén de Recovery Services.
1. Defina el **Objetivo de protección** (**En Azure** > **No virtualizado/Otro**).
1. Descargue el archivo de instalación unificada de Site Recovery (**Preparar infraestructura** > **Origen**). Al abrir la dirección URL del portal desde dentro de ConfigurationServer, el archivo se descarga en el servidor correcto. Desde fuera de ConfigurationServer, cargue el archivo de instalación en ConfigurationServer.
1. Descargue la clave de registro del almacén (cárguelo en ConfigurationServer como en el paso anterior, si fuera necesario).
1. Ejecute la instalación unificada de Recovery en ConfigurationServer.
1. Configure el entorno de destino (compruebe que sigue conectado al portal de destino).
1. Defina la directiva de replicación.
1. Inicie la replicación.

Después de que la replicación se realice correctamente, pruebe el escenario; para ello, realice una conmutación por error de prueba. Compruebe y elimine la prueba. El paso final es realizar la conmutación por error real.

> [!CAUTION]
> No se produce la sincronización con la máquina virtual de origen. Si desea migrar de nuevo, limpie todo y vuelva a empezar desde el principio.

### <a name="duplicate-by-using-resource-manager-template-exportimport"></a>Duplicación mediante la importación o exportación de plantillas de Resource Manager

Puede exportar la plantilla de Azure Resource Manager que se usa para realizar la implementación en el equipo local. Edite la plantilla para cambiar la ubicación y otros parámetros o variables. Después, vuelva a realizar la implementación en Azure global. 

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Azure Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

Exporte la plantilla de Resource Manager en el portal mediante la selección del grupo de recursos. Seleccione **Implementaciones** y, después, seleccione la implementación más reciente. Seleccione **Plantilla** en el menú de la izquierda y descargue la plantilla.

Se descarga un archivo .zip que contiene varios archivos. Los scripts de PowerShell, CLI de Azure, Ruby o .NET le ayudan a implementar la plantilla. El archivo *parameters.json* tiene toda la información la última implementación. Es probable que necesite cambiar algunos valores de este archivo. Edite el archivo *template.json* si quiere volver a implementar solo un subconjunto de los recursos.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Site Recovery](../site-recovery/index.yml).
- Obtenga información sobre como [exportar plantillas de Resource Manager](../azure-resource-manager/templates/export-template-portal.md) o lea una introducción a [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Obtenga más información sobre la [recuperación ante desastres de un equipo físico a Azure mediante Site Recovery](../site-recovery/physical-azure-disaster-recovery.md).
- Lea la [introducción a las ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Más información acerca de cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="cloud-services"></a>Cloud Services

Para volver a implementar los recursos de Azure Cloud Services, vuelva a proporcionar las definiciones `.cspkg` y `.cscfg`.

### <a name="azure-portal"></a>Azure portal

Para volver a implementar los servicio en la nube en Azure Portal:

1. [Cree un nuevo servicio en la nube](../cloud-services/cloud-services-how-to-create-deploy-portal.md) mediante las definiciones `.cspkg` y `.cscfg`.
1. Actualice el [registro CNAME o A](../cloud-services/cloud-services-custom-domain-name-portal.md) para que apunte el tráfico al nuevo servicio en la nube.
1. Cuando el tráfico apunta al nuevo servicio en la nube, elimine el servicio en la nube anterior de Azure Alemania.

### <a name="powershell"></a>PowerShell

Para volver a implementar los servicios en la nube mediante PowerShell:

1. [Cree un nuevo servicio en la nube](/powershell/module/servicemanagement/azure.service/new-azureservice) mediante las definiciones `.cspkg` y `.cscfg`.

    ```powershell
    New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <westeurope>
    ```

1. [Cree una nueva implementación](/powershell/module/servicemanagement/azure.service/new-azuredeployment) mediante las definiciones `.cspkg` y `.cscfg`.

    ```powershell
    New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>
    ```

1. Actualice el [registro CNAME o A](../cloud-services/cloud-services-custom-domain-name-portal.md) para que apunte el tráfico al nuevo servicio en la nube.
1. Cuando el tráfico apunta al nuevo servicio en la nube, [elimine el servicio en la nube anterior](/powershell/module/servicemanagement/azure.service/remove-azureservice) de Azure Alemania.

    ```powershell
    Remove-AzureService -ServiceName <yourOldServiceName>
    ```

### <a name="rest-api"></a>API DE REST

Para volver a implementar servicios en la nube mediante la API REST:

1. [Cree un nuevo servicio en la nube](/rest/api/compute/cloudservices/rest-create-cloud-service) en el entorno de destino.

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices
    ```

1. Cree una nueva implementación mediante [Create Deployment API](/previous-versions/azure/reference/ee460813(v=azure.100)). Para buscar las definiciones `.cspkg` y `.cscfg`, puede llamar a [Get Package API](/previous-versions/azure/reference/jj154121(v=azure.100)).

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production
    ```

1. Cuando el tráfico apunta al nuevo servicio en la nube, [elimine el servicio en la nube anterior](/rest/api/compute/cloudservices/rest-delete-cloud-service) de Azure Alemania.

    ```http
    https://management.core.cloudapi.de/<subscription-id>/services/hostedservices/<old-cloudservice-name>
    ```

Para obtener más información:

- Revise la [introducción a Cloud Services](../cloud-services/cloud-services-choose-me.md).

## <a name="service-fabric"></a>Service Fabric

No puede migrar recursos de Azure Service Fabric de Azure Alemania a Azure global. Debe volver a implementar los recursos de Service Fabric en el nuevo entorno.

Puede obtener información sobre el entorno actual de Service Fabric mediante los cmdlets de PowerShell. Acceda a todos los cmdlets que están relacionados con Service Fabric especificando `Get-Help *ServiceFabric*` en PowerShell.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Service Fabric](../service-fabric/service-fabric-tutorial-create-dotnet-app.md).
- Obtenga información sobre cómo [crear un nuevo clúster](../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Revise la [introducción a Service Fabric](../service-fabric/service-fabric-overview.md).

## <a name="batch"></a>Batch

No puede migrar datos de la cuenta de Azure Batch desde una región a otra. La cuenta puede tener máquinas virtuales en ejecución asociadas con ella y estar interactuando activamente con datos de cuentas de almacenamiento, bases de datos u otros sistemas de almacenamiento.

Vuelva a implementar los scripts, las plantillas o el código en la nueva región. La reimplementación incluye las siguientes tareas:

1. [Creación de una cuenta de Batch](../batch/batch-account-create-portal.md).
1. [Aumento de la cuota de la cuenta de Batch](../batch/batch-quota-limit.md).
1. Creación de grupos de Batch.
1. Creación de nuevas cuentas de almacenamiento, bases de datos y otros servicios que se usan para conservar los datos de entrada y salida.
1. Actualización de la configuración y el código para que apunte a la nueva cuenta de Batch y usar las nuevas credenciales.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Batch](../batch/tutorial-parallel-dotnet.md).
- Revise la [Introducción a Azure Batch](../batch/batch-technical-overview.md).

## <a name="functions"></a>Functions

En este momento no se admite la migración de los recursos de Azure Functions desde Azure Alemania a Azure global. Se recomienda exportar una plantilla de Resource Manager, cambiar la ubicación y, después, volver a implementarla en la región de destino.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Key Vault, los certificados, configuraciones de aplicación y otros GUID para que sean coherentes con la nueva región.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Functions](../azure-functions/index.yml).
- Aprenda a [exportar plantillas de Resource Manager](../azure-resource-manager/templates/export-template-portal.md) o lea una introducción a [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Revise la [introducción a Azure Functions](../azure-functions/functions-overview.md).
- Consulte una [introducción a las ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Obtenga información sobre cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escalado de máquinas virtuales

Para migrar los conjuntos de escalado de máquinas virtuales a Azure global, exporte la plantilla de Resource Manager, adáptela al nuevo entorno y, a continuación, vuelva a implementarla en la región de destino. Exporte solo la plantilla base y vuelva a implementar la plantilla en el nuevo entorno. Las instancias de los conjuntos de escalado de máquinas virtuales individuales deben ser todas iguales.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales del conjunto de escalado de máquina virtual](../virtual-machine-scale-sets/tutorial-create-and-manage-cli.md).
- Aprenda a [exportar las plantillas de Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md).
- Revise la [introducción a Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Obtenga información general de los [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/overview.md).
- Lea una [introducción a las ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Obtenga información sobre cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="web-apps"></a>Web Apps

En este momento, no se pueden migrar las aplicaciones que ha creado con la característica Web Apps de Azure App Service desde Azure Alemania a Azure global. Se recomienda exportar una aplicación web como una plantilla de Resource Manager y, después, vuelva a implementarla después de cambiar la propiedad de ubicación en la nueva región.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de App Service](../app-service/tutorial-dotnetcore-sqldb-app.md).
- Aprenda a [exportar plantillas de Resource Manager](../azure-resource-manager/templates/export-template-portal.md) o lea una introducción a [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Revise la [introducción a App Service](../app-service/overview.md).
- Lea la [introducción a las ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Obtenga información sobre cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)