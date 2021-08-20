---
title: 'Inicio rápido: Aprovisionamiento de Azure Spring Cloud con Terraform'
description: En este inicio rápido se muestra cómo usar Terraform para implementar un clúster de Spring Cloud en una red virtual existente.
author: aluna033
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ariel
ms.date: 06/15/2021
ms.openlocfilehash: d099e86f5a28aae145723b728e79ce3ee55f8250
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287564"
---
# <a name="quickstart-provision-azure-spring-cloud-using-terraform"></a>Inicio rápido: Aprovisionamiento de Azure Spring Cloud con Terraform

En este inicio rápido se describe cómo usar Terraform para implementar un clúster de Azure Spring Cloud en una red virtual existente.

Azure Spring Cloud facilita la implementación de aplicaciones de microservicio de Spring Boot en Azure sin necesidad de realizar cambios en el código. El servicio administra la infraestructura de las aplicaciones de Spring Cloud, con el fin de que los desarrolladores puedan centrarse en el código. Azure Spring Cloud proporciona administración del ciclo de vida mediante el uso de una supervisión y un diagnóstico completos, administración de la configuración, detección de servicios, integración de CI/CD e implementaciones blue-green, entre otros.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción, cree [una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* [Hashicorp Terraform](https://www.terraform.io/downloads.html)
* Dos subredes dedicadas para el clúster de Azure Spring Cloud, una para el entorno de ejecución del servicio y otra para las aplicaciones de microservicios de Spring Boot. Puede encontrar los requisitos de la subred y la red virtual en la sección [Requisitos de red virtual](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* Un área de trabajo de Log Analytics existente para la configuración de diagnóstico de Azure Spring Cloud y un recurso de Application Insights basado en el área de trabajo. Para más información, consulte [Análisis de registros y métricas con la configuración de diagnóstico](diagnostic-services.md) y [Agente In-Process de Java de Application Insights en Azure Spring Cloud (versión preliminar)](how-to-application-insights.md).
* Tres intervalos internos de enrutamiento de interdominios sin clases (CIDR) (al menos */16* cada uno) que ha identificado para su uso por parte del clúster de Azure Spring Cloud. Estos intervalos CIDR no se podrán enrutar directamente y solo los usará internamente el clúster de Azure Spring Cloud. Los clústeres no pueden usar *169.254.0.0/16*, *172.30.0.0/16,* *172.31.0.0/16* o  *192.0.2.0/24* en los intervalos internos CIDR de Spring Cloud ni los intervalos IP incluidos dentro del intervalo de direcciones de red virtual del clúster.
* Permiso de servicio concedido a la red virtual. El proveedor de recursos de Azure Spring Cloud requiere permisos de propietario en la red virtual para conceder permisos a una entidad de servicio dinámica y dedicada en la red virtual para realizar tareas de implementación y mantenimiento adicionales. Puede encontrar instrucciones y más información en la sección [Concesión de permisos de servicio a la red virtual](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).
* Si usa Azure Firewall o una aplicación virtual de red (NVA), también deberá cumplir los siguientes requisitos previos:
   * Reglas de nombre de dominio completo (FQDN) y de red. Para más información, consulte [Requisitos de red virtual](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Aplicación de una ruta definida por el usuario (UDR) única a cada uno de los entornos de ejecución del servicio y subredes de aplicación de microservicios de Spring Boot. Para más información sobre las UDR, consulte [Enrutamiento del tráfico de redes virtuales](../virtual-network/virtual-networks-udr-overview.md). La UDR debe configurarse con una ruta de *0.0.0.0/0* con un destino de la NVA antes de implementar el clúster de Spring Cloud. Para más información, consulte la sección [Traer su propia tabla de rutas](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de [Implementación de Azure Spring Cloud en una red virtual](how-to-deploy-in-azure-virtual-network.md).

## <a name="review-the-configuration-file"></a>Revisión del archivo de configuración

El archivo de configuración que se usa en este inicio rápido procede de la [arquitectura de referencia de Azure Spring Cloud](reference-architecture.md).

```hcl
provider "azurerm" {
    features {} 
}

resource "azurerm_resource_group" "sc_corp_rg" {
    name      = var.resource_group_name
    location  = var.location
}

resource "azurerm_application_insights" "sc_app_insights" {
  name                = var.app_insights_name
  location            = var.location
  resource_group_name = var.resource_group_name
  application_type    = "web"
  depends_on = [azurerm_resource_group.sc_corp_rg]
}

resource "azurerm_spring_cloud_service" "sc" {
  name                = var.sc_service_name 
  resource_group_name = var.resource_group_name
  location            = var.location
  
  network {
    app_subnet_id                   = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.app_subnet_id}"
    service_runtime_subnet_id       = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.service_runtime_subnet_id}"
    cidr_ranges                     = var.sc_cidr
  }
  
  timeouts {
      create = "60m"
      delete = "2h"
  }
  
  depends_on = [azurerm_resource_group.sc_corp_rg]
  tags = var.tags
  
}

resource "azurerm_monitor_diagnostic_setting" "sc_diag" {
  name                        = "monitoring"
  target_resource_id          = azurerm_spring_cloud_service.sc.id
  log_analytics_workspace_id = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.OperationalInsights/workspaces/${var.sc_law_id}"

  log {
    category = "ApplicationConsole"
    enabled  = true

    retention_policy {
      enabled = false
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = false
    }
  }
}
```

## <a name="apply-the-configuration"></a>Aplicación de la configuración

Para aplicar la configuración, siga estos pasos:

1. Guarde el archivo [variables.tf](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/terraform/brownfield-deployment/variable.tf) localmente y ábralo en un editor.

1. Edite el archivo para agregar los siguientes valores:

   - El identificador de suscripción de la cuenta de Azure en la que se va a realizar la implementación.

   - Una ubicación de implementación entre las regiones en las que Azure Spring Cloud está disponible, como se muestra en [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all). Necesitará el formato corto del nombre de la ubicación. Para obtener este valor, use el siguiente comando para generar una lista de ubicaciones de Azure y, a continuación, busque el valor **Nombre** de la región seleccionada.

      ```azurecli
      az account list-locations --output table
      ```

   - El nombre del grupo de recursos en el que se va a realizar la implementación.

   - Un nombre de su elección para la implementación de Spring Cloud.

   - El nombre del grupo de recursos de red virtual donde se implementarán los recursos.

   - El nombre de la red virtual de radio (por ejemplo, *vnet-spoke*).

   - El nombre de la subred que va a usar Spring Cloud App Service (por ejemplo, *snet-app*).

   - El nombre de la subred que va a usar Spring Cloud App Service (por ejemplo, *snet-runtime*).

   - El nombre del área de trabajo de Azure Log Analytics.

   - Los rangos CIDR de la red virtual que va a usar Azure Spring Cloud (por ejemplo, *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*).

   - Los pares clave-valor que se aplicarán como etiquetas en todos los recursos que admiten etiquetas. Para obtener más información, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración](../azure-resource-manager/management/tag-resources.md). 

1. Ejecute el comando siguiente para inicializar los módulos de Terraform.

   ```bash
   terraform init
   ```

1. Ejecute el siguiente comando para crear el plan de implementación de Terraform:

   ```bash
   terraform plan -out=springcloud.plan
   ```

1. Ejecute el siguiente comando para aplicar el plan de implementación de Terraform:

   ```bash
   terraform apply springcloud.plan
   ```

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

Puede usar Azure Portal para comprobar los recursos implementados, o bien usar un script de la CLI de Azure o Azure PowerShell script para enumerar los recursos implementados.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si planea seguir trabajando en otros inicios rápidos y tutoriales, considere la posibilidad de dejar estos recursos activos. Cuando ya no los necesite, elimine los recursos creados en este artículo mediante el comando siguiente.

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado una instancia de Azure Spring Cloud en una red virtual existente con Terraform y, luego, ha validado la implementación. Para obtener más información sobre Azure Spring Cloud, continúe con los artículos siguientes.

- Implemente una de las siguientes aplicaciones de ejemplo desde las ubicaciones siguientes:
   - [Aplicación Pet Clinic con integración de MySQL](https://github.com/azure-samples/spring-petclinic-microservices) (microservicios con back-end de MySQL).
   - [Simple Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Use [dominios personalizados](tutorial-custom-domain.md) con Azure Spring Cloud.
- Exponga aplicaciones de Azure Spring Cloud en Internet mediante [Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Vea la [arquitectura de referencia de Azure Spring Cloud](reference-architecture.md) completa, que se basa en [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).