---
title: 'Inicio rápido: Aprovisionamiento de Azure Spring Cloud con Bicep'
description: En este inicio rápido se muestra cómo usar Bicep para implementar un clúster de Spring Cloud en una red virtual existente.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ssarwa
ms.date: 08/06/2021
ms.openlocfilehash: eb9bf7de3b89dc39a110a87796a1704780e0d270
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272360"
---
# <a name="quickstart-provision-azure-spring-cloud-using-bicep"></a>Inicio rápido: Aprovisionamiento de Azure Spring Cloud con Bicep

En este inicio rápido se describe cómo usar Bicep para implementar un clúster de Azure Spring Cloud en una red virtual existente.

Azure Spring Cloud facilita la implementación de aplicaciones de microservicio de Spring Boot en Azure sin necesidad de realizar cambios en el código. El servicio administra la infraestructura de las aplicaciones de Spring Cloud, con el fin de que los desarrolladores puedan centrarse en el código. Azure Spring Cloud proporciona administración del ciclo de vida mediante el uso de una supervisión y un diagnóstico completos, administración de la configuración, detección de servicios, integración de CI/CD e implementaciones blue-green, entre otros.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción, cree [una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* Dos subredes dedicadas para el clúster de Azure Spring Cloud, una para el entorno de ejecución del servicio y otra para las aplicaciones de microservicios de Spring Boot. Puede encontrar los requisitos de la subred y la red virtual en la sección [Requisitos de red virtual](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* Un área de trabajo de Log Analytics existente para la configuración de diagnóstico de Azure Spring Cloud. Para obtener más información, consulte [Análisis de registros y métricas con la configuración de diagnóstico](diagnostic-services.md).
* Tres intervalos internos de enrutamiento de interdominios sin clases (CIDR) (al menos */16* cada uno) que ha identificado para su uso por parte del clúster de Azure Spring Cloud. Estos intervalos CIDR no se podrán enrutar directamente y solo los usará internamente el clúster de Azure Spring Cloud. Los clústeres no pueden usar *169.254.0.0/16*, *172.30.0.0/16,* *172.31.0.0/16* o  *192.0.2.0/24* en los intervalos internos CIDR de Spring Cloud ni los intervalos IP incluidos dentro del intervalo de direcciones de red virtual del clúster.
* Permiso de servicio concedido a la red virtual. El proveedor de recursos de Azure Spring Cloud requiere permisos de propietario en la red virtual para conceder permisos a una entidad de servicio dinámica y dedicada en la red virtual para realizar tareas de implementación y mantenimiento adicionales. Puede encontrar instrucciones y más información en la sección [Concesión de permisos de servicio a la red virtual](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* Si usa Azure Firewall o una aplicación virtual de red (NVA), también deberá cumplir los siguientes requisitos previos:
   * Reglas de nombre de dominio completo (FQDN) y de red. Para más información, consulte [Requisitos de red virtual](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Aplicación de una ruta definida por el usuario (UDR) única a cada uno de los entornos de ejecución del servicio y subredes de aplicación de microservicios de Spring Boot. Para más información sobre las UDR, consulte [Enrutamiento del tráfico de redes virtuales](../virtual-network/virtual-networks-udr-overview.md). La UDR debe configurarse con una ruta de *0.0.0.0/0* con un destino de la NVA antes de implementar el clúster de Spring Cloud. Para más información, consulte la sección [Traer su propia tabla de rutas](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* [CLI de Azure](/cli/azure/install-azure-cli)

## <a name="deploy-using-bicep"></a>Implementación con Bicep

Para implementar el clúster, siga estos pasos:

1. Cree un archivo *azuredeploy.bicep* con el siguiente contenido:

   ```Bicep
   @description('The instance name of the Azure Spring Cloud resource')
   param springCloudInstanceName string

   @description('The name of the Application Insights instance for Azure Spring Cloud')
   param appInsightsName string

   @description('The resource ID of the existing Log Analytics workspace. This will be used for both diagnostics logs and Application    Insights')
   param laWorkspaceResourceId string

   @description('The resourceID of the Azure Spring Cloud App Subnet')
   param springCloudAppSubnetID string

   @description('The resourceID of the Azure Spring Cloud Runtime Subnet')
   param springCloudRuntimeSubnetID string

   @description('Comma-separated list of IP address ranges in CIDR format. The IP ranges are reserved to host underlying Azure Spring Cloud    infrastructure, which should be 3 at least /16 unused IP ranges, must not overlap with any Subnet IP ranges')
   param springCloudServiceCidrs string = '10.0.0.0/16,10.2.0.0/16,10.3.0.1/16'

   @description('The tags that will be associated to the Resources')
   param tags object = {
     environment: 'lab'
   }

   var springCloudSkuName = 'S0'
   var springCloudSkuTier = 'Standard'
   var location = resourceGroup().location

   resource appInsights 'Microsoft.Insights/components@2020-02-02-preview' = {
     name: appInsightsName
     location: location
     kind: 'web'
     tags: tags
     properties: {
       Application_Type: 'web'
       Flow_Type: 'Bluefield'
       Request_Source: 'rest'
       WorkspaceResourceId: laWorkspaceResourceId
     }
   }

   resource springCloudInstance 'Microsoft.AppPlatform/Spring@2020-07-01' = {
     name: springCloudInstanceName
     location: location
     tags: tags
     sku: {
       name: springCloudSkuName
       tier: springCloudSkuTier
     }
     properties: {
       networkProfile: {
         serviceCidr: springCloudServiceCidrs
         serviceRuntimeSubnetId: springCloudRuntimeSubnetID
         appSubnetId: springCloudAppSubnetID
       }
     }
   }

   resource springCloudMonitoringSettings 'Microsoft.AppPlatform/Spring/monitoringSettings@2020-07-01' = {
     name: '${springCloudInstance.name}/default'
     properties: {
       traceEnabled: true
       appInsightsInstrumentationKey: appInsights.properties.InstrumentationKey
     }
   }

   resource springCloudDiagnostics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
     name: 'monitoring'
     scope: springCloudInstance
     properties: {
       workspaceId: laWorkspaceResourceId
       logs: [
         {
           category: 'ApplicationConsole'
           enabled: true
           retentionPolicy: {
             days: 30
             enabled: false
           }
         }
       ]
     }
   }
   ```

1. Abra una ventana de Bash y ejecute el comando de la CLI de Azure siguiente, reemplazando los marcadores de posición *\<value>* por los valores siguientes:

   - **resource-group:** nombre del grupo de recursos para implementar la instancia de Spring Cloud.
   - **springCloudInstanceName:** el nombre del recurso de Azure Spring Cloud.
   - **appInsightsName:** el nombre de la instancia de Application Insights para Azure Spring Cloud.
   - **laWorkspaceResourceId:** el identificador de recurso del área de trabajo de Log Analytics existente (por ejemplo, */   subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/   Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* ).
   - **springCloudAppSubnetID:** el identificador de recurso de la subred de aplicación de Azure Spring Cloud.
   - **springCloudRuntimeSubnetID:** el identificador de recurso de la subred del entorno de ejecución de Azure Spring Cloud.
   - **springCloudServiceCidrs:** una lista separada por comas de intervalos de direcciones IP (3 en total) en formato CIDR. Los intervalos IP se reservan para hospedar la infraestructura de Azure Spring Cloud subyacente. Estos tres intervalos deben ser al menos */16* intervalos IP sin usar y no deben superponerse con los intervalos IP de subred enrutables usados dentro de la red.

   ```azurecli
   az deployment group create \
       --resource-group <value> \
       --name initial \
       --template-file azuredeploy.bicep \
       --parameters \
           springCloudInstanceName=<value> \
           appInsightsName=<value> \
           laWorkspaceResourceId=<value> \
           springCloudAppSubnetID=<value> \
           springCloudRuntimeSubnetID=<value> \
           springCloudServiceCidrs=<value>
   ```

   Este comando usa la plantilla de Bicep para crear una instancia de Azure Spring Cloud en una red virtual existente. El comando también crea una instancia de Application Insights basada en área de trabajo en un área de trabajo de Azure Monitor Log Analytics.

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

Puede usar Azure Portal para comprobar los recursos implementados, o bien usar un script de la CLI de Azure o Azure PowerShell script para enumerar los recursos implementados.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea seguir trabajando en otros inicios rápidos y tutoriales, considere la posibilidad de dejar estos recursos activos. Cuando ya no lo necesite, elimine el grupo de recursos, que elimina los recursos que contiene. Para eliminar el grupo de recursos mediante la CLI de Azure, use estos comandos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado una instancia de Azure Spring Cloud en una red virtual existente con Bicep y, luego, ha validado la implementación. Para obtener más información sobre Azure Spring Cloud, continúe con los artículos siguientes.

- Implemente una de las siguientes aplicaciones de ejemplo desde las ubicaciones siguientes:
   - [Aplicación Pet Clinic con integración de MySQL](https://github.com/azure-samples/spring-petclinic-microservices) (microservicios con back-end de MySQL).
   - [Simple Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Use [dominios personalizados](tutorial-custom-domain.md) con Azure Spring Cloud.
- Exponga aplicaciones de Azure Spring Cloud en Internet mediante [Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Vea la [arquitectura de referencia de Azure Spring Cloud](reference-architecture.md) completa, que se basa en [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
