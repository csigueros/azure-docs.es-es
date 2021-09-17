---
title: 'Inicio rápido: Aprovisionamiento de Azure Spring Cloud con la CLI de Azure'
description: En este inicio rápido se muestra cómo usar la CLI de Azure para implementar un clúster de Spring Cloud en una red virtual existente.
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java
ms.author: vramasubbu
ms.date: 06/15/2021
ms.openlocfilehash: a8dd727b0f0f999f1b0b8e6c9b6ed5977779f5ff
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271379"
---
# <a name="quickstart-provision-azure-spring-cloud-using-azure-cli"></a>Inicio rápido: Aprovisionamiento de Azure Spring Cloud con la CLI de Azure

En este inicio rápido se describe cómo usar la CLI de Azure para implementar un clúster de Azure Spring Cloud en una red virtual existente.

Azure Spring Cloud facilita la implementación de aplicaciones de microservicio de Spring Boot en Azure sin necesidad de realizar cambios en el código. El servicio administra la infraestructura de las aplicaciones de Spring Cloud, con el fin de que los desarrolladores puedan centrarse en el código. Azure Spring Cloud proporciona administración del ciclo de vida mediante el uso de una supervisión y un diagnóstico completos, administración de la configuración, detección de servicios, integración de CI/CD e implementaciones blue-green, entre otros.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción, cree [una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* Dos subredes dedicadas para el clúster de Azure Spring Cloud, una para el entorno de ejecución del servicio y otra para las aplicaciones de microservicios de Spring Boot. Puede encontrar los requisitos de la subred y la red virtual en la sección [Requisitos de red virtual](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* Un área de trabajo de Log Analytics existente para la configuración de diagnóstico de Azure Spring Cloud y un recurso de Application Insights basado en el área de trabajo. Para más información, consulte [Análisis de registros y métricas con la configuración de diagnóstico](diagnostic-services.md) y [Agente In-Process de Java de Application Insights en Azure Spring Cloud (versión preliminar)](how-to-application-insights.md).
* Tres intervalos internos de enrutamiento de interdominios sin clases (CIDR) (al menos */16* cada uno) que ha identificado para su uso por parte del clúster de Azure Spring Cloud. Estos intervalos CIDR no se podrán enrutar directamente y solo los usará internamente el clúster de Azure Spring Cloud. Los clústeres no pueden usar *169.254.0.0/16*, *172.30.0.0/16,* *172.31.0.0/16* o  *192.0.2.0/24* en los intervalos internos CIDR de Spring Cloud ni los intervalos IP incluidos dentro del intervalo de direcciones de red virtual del clúster.
* Permiso de servicio concedido a la red virtual. El proveedor de recursos de Azure Spring Cloud requiere permisos de propietario en la red virtual para conceder permisos a una entidad de servicio dinámica y dedicada en la red virtual para realizar tareas de implementación y mantenimiento adicionales. Puede encontrar instrucciones y más información en la sección [Concesión de permisos de servicio a la red virtual](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* Si usa Azure Firewall o una aplicación virtual de red (NVA), también deberá cumplir los siguientes requisitos previos:
   * Reglas de nombre de dominio completo (FQDN) y de red. Para más información, consulte [Requisitos de red virtual](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Aplicación de una ruta definida por el usuario (UDR) única a cada uno de los entornos de ejecución del servicio y subredes de aplicación de microservicios de Spring Boot. Para más información sobre las UDR, consulte [Enrutamiento del tráfico de redes virtuales](../virtual-network/virtual-networks-udr-overview.md). La UDR debe configurarse con una ruta de *0.0.0.0/0* con un destino de la NVA antes de implementar el clúster de Spring Cloud. Para más información, consulte la sección [Traer su propia tabla de rutas](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* [CLI de Azure](/cli/azure/install-azure-cli)

## <a name="review-the-azure-cli-deployment-script"></a>Revisión del script de implementación de la CLI de Azure

El script de implementación que se usa en este inicio rápido proviene de la [arquitectura de referencia de Azure Spring Cloud](reference-architecture.md).

```azurecli
#!/bin/bash

echo "Enter Azure Subscription ID: "
read subscription
subscription=$subscription

echo "Enter Azure region for resource deployment: "
read region
location=$region

echo "Enter Azure Spring cloud Resource Group Name: "
read azurespringcloudrg
azurespringcloud_resource_group_name=$azurespringcloudrg

echo "Enter Azure Spring cloud VNet Resource Group Name: "
read azurespringcloudvnetrg
azurespringcloud_vnet_resource_group_name=$azurespringcloudvnetrg

echo "Enter Azure Spring cloud Spoke VNet : "
read azurespringcloudappspokevnet
azurespringcloudappspokevnet=$azurespringcloudappspokevnet

echo "Enter Azure Spring cloud App SubNet : "
read azurespringcloudappsubnet
azurespringcloud_app_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudappsubnet

echo "Enter Azure Spring cloud Service SubNet : "
read azurespringcloudservicesubnet
azurespringcloud_service_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudservicesubnet

echo "Enter Azure Log Analytics Workspace Resource Group Name: "
read loganalyticsrg
loganalyticsrg=$loganalyticsrg

echo "Enter Log Analytics Workspace Resource ID: "
read workspace
workspaceID='/subscriptions/'$subscription'/resourcegroups/'$loganalyticsrg'/providers/microsoft.operationalinsights/workspaces/'$workspace

echo "Enter Reserved CIDR Ranges for Azure Spring Cloud: "
read reservedcidrrange
reservedcidrrange=$reservedcidrrange

echo "Enter key=value pair used for tagging Azure Resources (space separated for multiple tags): "
read tag
tags=$tag

randomstring=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 13 | head -n 1)
azurespringcloud_service='spring-'$randomstring #Name of unique Spring Cloud resource
azurespringcloud_appinsights=$azurespringcloud_service
azurespringcloud_resourceid='/subscriptions/'$subscription'/resourceGroups/'$azurespringcloud_resource_group_name'/providers/Microsoft.AppPlatform/Spring/'$azurespringcloud_service

# Create Application Insights
az monitor app-insights component create \
    --app ${azurespringcloud_service} \
    --location ${location} \
    --kind web \
    -g ${azurespringcloudrg} \
    --application-type web \
    --workspace ${workspaceID}

# Create Azure Spring Cloud Instance
az spring-cloud create \
   -n ${azurespringcloud_service} \
   -g ${azurespringcloudrg} \
   -l ${location} \
   --enable-java-agent true \
   --app-insights ${azurespringcloud_service} \
   --sku Standard \
   --app-subnet ${azurespringcloud_app_subnet_name} \
   --service-runtime-subnet ${azurespringcloud_service_subnet_name} \
   --reserved-cidr-range ${reservedcidrrange} \
   --tags ${tags}

# Update diagnostic setting for Azure Spring Cloud instance
az monitor diagnostic-settings create  \
   --name monitoring \
   --resource ${azurespringcloud_resourceid} \
   --logs    '[{"category": "ApplicationConsole","enabled": true}]' \
   --workspace  ${workspaceID}
```

## <a name="deploy-the-cluster"></a>Implementación del clúster

Para implementar el clúster de Azure Spring Cloud mediante el script de la CLI de Azure, siga estos pasos:

1. Inicie sesión en Azure con el siguiente comando:

   ```azurecli
   az login
   ```

   Después de iniciar sesión, este comando mostrará información sobre todas las suscripciones a las que tiene acceso. Anote el nombre y el id. de la suscripción que quiera utilizar.

1. Establezca la suscripción de destino.

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. Registre el proveedor de recursos de Azure Spring Cloud.

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. Agregue las extensiones necesarias a la CLI de Azure.

   ```azurecli
   az extension add --name spring-cloud
   ```

1. Elija una ubicación de implementación entre las regiones en las que Azure Spring Cloud está disponible, como se muestra en [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all).

1. Use el siguiente comando para generar una lista de ubicaciones de Azure. Anote el valor del **nombre** corto de la región que seleccionó en el paso anterior.

   ```azurecli
   az account list-locations --output table
   ```

1. Cree un grupo de recursos en el que implementar el recurso.

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. Guarde el script de Bash [deploySpringCloud.sh](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/deploySpringCloud.sh) localmente y ejecútelo desde el símbolo del sistema de Bash.

   ```azurecli
   ./deploySpringCloud.sh
   ```

1. Escriba los siguientes valores cuando el script se lo solicite:

   - El id. de suscripción de Azure que guardó anteriormente.
   - El nombre de la ubicación de Azure que guardó anteriormente.
   - El nombre del grupo de recursos que creó anteriormente.
   - El nombre del grupo de recursos de red virtual donde se implementarán los recursos.
   - El nombre de la red virtual de radio (por ejemplo, *vnet-spoke*).
   - El nombre de la subred que va a usar Spring Cloud App Service (por ejemplo, *snet-app*).
   - El nombre de la subred que va a usar Spring Cloud App Service (por ejemplo, *snet-runtime*).
   - El nombre del grupo de recursos del área de trabajo de Azure Log Analytics que se usará para almacenar los registros de diagnóstico.
   - El nombre del área de trabajo de Azure Log Analytics (por ejemplo, *la-cb5sqq6574o2a*).
   - Los rangos CIDR de la red virtual que va a usar Azure Spring Cloud (por ejemplo, *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*).
   - Los pares clave-valor que se aplicarán como etiquetas en todos los recursos que admiten etiquetas. Para obtener más información, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md). Use una lista separada por espacios para aplicar varias etiquetas (por ejemplo, *environment=Dev BusinessUnit=finance*).

Después de proporcionar esta información, el script creará e implementará los recursos de Azure.

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

Puede usar Azure Portal para comprobar los recursos implementados, o bien usar la CLI de Azure para enumerar los recursos implementados.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea seguir trabajando en otros inicios rápidos y tutoriales, considere la posibilidad de dejar estos recursos activos. Cuando ya no lo necesite, elimine el grupo de recursos, que elimina los recursos que contiene. Para eliminar el grupo de recursos mediante la CLI de Azure, use estos comandos:

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado una instancia de Azure Spring Cloud en una red virtual existente con la CLI de Azure y, luego, ha validado la implementación. Para obtener más información sobre Azure Spring Cloud, continúe con los artículos siguientes.

- Implemente una de las siguientes aplicaciones de ejemplo desde las ubicaciones siguientes:
   - [Aplicación Pet Clinic con integración de MySQL](https://github.com/azure-samples/spring-petclinic-microservices) (microservicios con back-end de MySQL).
   - [Simple Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Use [dominios personalizados](tutorial-custom-domain.md) con Azure Spring Cloud.
- Exponga aplicaciones de Azure Spring Cloud en Internet mediante [Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Vea la [arquitectura de referencia de Azure Spring Cloud](reference-architecture.md) completa, que se basa en [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
