---
title: Conexión a un clúster administrado de Service Fabric
description: Aprenda cómo conectarse a un clúster administrado de Service Fabric
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aa168a283be909678a199eee887d69bb3eb2024a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090770"
---
# <a name="connect-to-a-service-fabric-managed-cluster"></a>Conexión a un clúster administrado de Service Fabric

Una vez que se ha implementado un clúster por medio del [portal, una plantilla de Azure Resource Manager](quickstart-managed-cluster-template.md) o [PowerShell](tutorial-managed-cluster-deploy.md), hay varias maneras de conectarse al clúster administrado y de verlo. 

## <a name="use-azure-portal"></a>Usar Azure Portal

Vaya al recurso de clúster administrado:

 1) Vaya a https://portal.azure.com/

 2) Vaya al recurso de clúster, busque Service Fabric y seleccione "clústeres administrados de Service Fabric"

 3) Selección del clúster

 4) En esta experiencia puede ver y modificar determinados parámetros. Para obtener más información, vea las [opciones de configuración de clúster](how-to-managed-cluster-configuration.md) disponibles.

## <a name="use-service-fabric-explorer"></a>Uso de Service Fabric Explorer

[Service Fabric Explorer](https://github.com/Microsoft/service-fabric-explorer) (SFX) es una aplicación para inspeccionar y administrar el estado de aplicaciones y clústeres de un clúster de Microsoft Azure Service Fabric. 

Para ir a SFX para el clúster administrado:
 
 1) Vaya a https://portal.azure.com/
 
 2) Vaya al recurso de clúster, busque Service Fabric y seleccione "clústeres administrados de Service Fabric"

 3) Selección del clúster

 4) Busque el elemento `SF Explorer` que se encuentra en la esquina superior derecha, por ejemplo: https://mycluster.region.cloudapp.azure.com:19080/Explorer

## <a name="use-powershell-modules"></a>Uso de módulos de PowerShell

Los siguientes módulos de PowerShell están disponibles para conectarse, ver y modificar configuraciones del clúster. 

* Instalación de [Service Fabric SDK y del módulo de PowerShell](service-fabric-get-started.md).

* Instale [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (o posterior).

### <a name="using-the-service-fabric-powershell-module"></a>Uso del módulo de PowerShell de Service Fabric
Para usar este módulo, necesita la huella digital del certificado del clúster. Puede encontrar este valor en la salida de las propiedades del clúster de la implementación de recursos o consultando las propiedades del clúster en un recurso existente.

El siguiente comando se puede utilizar para consultar la huella digital del certificado del clúster en el recurso del clúster.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprints
```

Con esta huella, ya está preparado para conectarse al clúster.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="using-the-azure-service-fabric-powershell-module"></a>Uso del módulo de PowerShell de Azure Service Fabric

El módulo de Azure Service Fabric permite realizar operaciones como la creación de un clúster administrado, el escalado de un tipo de nodo y la visualización de información de un recurso de clúster administrado. Los cmdlets específicos admitidos para los clústeres administrados se denominan `AzServiceFabricManagedCluster*`, y se pueden consultar en la documentación del [módulo Az.ServiceFabric de PowerShell](/powershell/module/az.servicefabric/).


En el ejemplo siguiente se usa uno de los cmdlets para ver los detalles de un clúster administrado.

```powershell
$rgName = "testResourceGroup"
$clusterName = "mycluster"
Get-AzServiceFabricManagedCluster -ResourceGroupName $rgName -Name $clusterName
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una aplicación en un clúster administrado mediante Azure Resource Manager](how-to-managed-cluster-app-deployment-template.md)
* [Opciones de configuración del clúster administrado](how-to-managed-cluster-configuration.md)



