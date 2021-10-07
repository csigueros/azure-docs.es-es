---
title: Creación de una aplicación de App Service mediante una plantilla de Terraform
description: Cree su primera aplicación en Azure App Service en segundos mediante una plantilla de Terraform, que es una de las muchas maneras de realizar una implementación en App Service.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.custom: subject-terraform
ms.openlocfilehash: 33aea508bd636ebb1b37cd2c9120d9fdb863460d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061959"
---
# <a name="create-app-service-app-using-a-terraform-template"></a>Creación de una aplicación de App Service mediante una plantilla de Terraform

Empiece a usar [Azure App Service](overview.md) mediante la implementación de una aplicación en la nube con [Terraform](/azure/developer/terraform/). Dado que usa un nivel de App Service gratuito, completar este inicio rápido no supone ningún costo.

Terraform permite definir y crear implementaciones completas de infraestructura de Azure. Se crean plantillas de Terraform en un formato legible para el usuario para crear y configurar recursos de Azure de forma coherente y reproducible. En este artículo se muestra cómo crear una aplicación de Windows con Terraform.

## <a name="prerequisites"></a>Requisitos previos

Suscripción de Azure: Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

Configuración de Terraform: si aún no lo ha hecho, configure Terraform con una de las siguientes opciones:

* [Configuración de Terraform en Azure Cloud Shell con Bash](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)
* [Configuración de Terraform en Azure Cloud Shell con PowerShell](/azure/developer/terraform/get-started-cloud-shell-powershell?tabs=bash)
* [Configuración de Terraform en Windows con Bash](/azure/developer/terraform/get-started-windows-bash?tabs=bash)
* [Configuración de Terraform en Windows con PowerShell](/azure/developer/terraform/get-started-windows-powershell?tabs=bash)

La extensión de Visual Studio Code para Azure Terraform permite trabajar con Terraform desde el editor. Con esta extensión, puede crear, probar y ejecutar configuraciones de Terraform. La extensión también admite la visualización de gráficos de recursos. Consulte [esta guía](/azure/developer/terraform/configure-vs-code-extension-for-terraform) para configurar la extensión de Visual Studio Code para Azure Terraform.

## <a name="review-the-template"></a>Revisión de la plantilla

A continuación, se muestra la plantilla usada en este inicio rápido. Implementa un plan y una aplicación de App Service en Windows y una aplicación "Hola mundo" Node.js de ejemplo desde el repositorio [Ejemplos de Azure](https://github.com/Azure-Samples).

```hcl
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.65"
    }
  }
  required_version = ">= 0.14.9"
}
provider "azurerm" {
  features {}
}
# Generate a random integer to create a globally unique name
resource "random_integer" "ri" {
  min = 10000
  max = 99999
}
# Create the resource group
resource "azurerm_resource_group" "rg" {
  name     = "myResourceGroup-${random_integer.ri.result}"
  location = "eastus"
}
# Create the Linux App Service Plan
resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "webapp-asp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Free"
    size = "F1"
  }
}
# Create the web app, pass in the App Service Plan ID, and deploy code from a public GitHub repo
resource "azurerm_app_service" "webapp" {
  name                = "webapp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
  source_control {
    repo_url           = "https://github.com/Azure-Samples/nodejs-docs-hello-world"
    branch             = "master"
    manual_integration = true
    use_mercurial      = false
  }
}
```

En la plantilla se definen tres recursos y un subrecurso de Azure. A continuación, se incluyen vínculos al [registro de Terraform del proveedor de Azure](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs) para obtener más información sobre el uso y los detalles:

* [**Microsoft.Resources/resourcegroups**](/azure/templates/microsoft.resources/resourcegroups?tabs=json): permite crear un grupo de recursos si aún no existe uno.
  * [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) 
* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): permite crear un plan de App Service.
  * [azurerm_app_service_plan](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service_plan)
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): permite crear una aplicación de App Service.
  * [azurerm_app_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service)
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols): permite crear una configuración de implementación de Git externa.
  * [source_control](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#source_control)

Para obtener más información sobre cómo crear plantillas de Terraform, consulte la [documentación de Terraform](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS).

## <a name="implement-the-terraform-code"></a>Implementación del código de Terraform

Terraform proporciona muchas características para administrar, compilar, implementar y actualizar la infraestructura. Los pasos siguientes le guiarán a través de la implementación y destrucción de los recursos. En la [documentación de Terraform](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) y la [documentación de Terraform en Azure](/azure/developer/terraform/) se ofrece información más detallada que se debe revisar si Terraform forma parte de la estrategia de infraestructura de Azure.

1. Cree un directorio en el que probar y ejecutar el código de ejemplo de Terraform y conviértalo en el directorio actual.

    ```bash
    mkdir appservice_tf_quickstart
    cd appservice_tf_quickstart
    ```

1. Cree un archivo llamado `main.tf` e inserte el código anterior.

    ```bash
    code main.tf
    ```

1. Inicialice Terraform.

    ```bash
    terraform init
    ```

1. Cree el plan de Terraform.

    ```bash
    terraform plan
    ```

1. Aprovisione los recursos definidos en el archivo de configuración `main.tf` (para confirmar la acción, introduzca `yes` en la solicitud).

    ```bash
    terraform apply
    ```

## <a name="validate-the-deployment"></a>Validación de la implementación

1. En el menú principal de Azure Portal, seleccione **Grupos de recursos** y vaya al grupo de recursos que creó con la plantilla anterior. Se denominará "myResourceGroup-" seguido de una cadena de enteros aleatorios.

1. Ahora verá todos los recursos que Terraform ha creado (una aplicación y un plan de App Service).

1. Seleccione la **aplicación de App Service** y vaya a la dirección URL para comprobar si el sitio se ha creado correctamente. También puede navegar a `http://<app_name>.azurewebsites.net/`, donde "app name" es "webapp-" seguido de esa misma cadena de enteros aleatorios del grupo de recursos.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, [elimine el grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group) o vuelva al terminal o línea de comandos y ejecute el comando `terraform destroy` para eliminar todos los recursos asociados a este inicio rápido.

> [!NOTE]
> Puede encontrar más ejemplos de Terraform de Azure App Service [aquí](/azure/app-service/samples-terraform). Puede encontrar todavía más ejemplos de Terraform de todos los servicios de Azure [aquí](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples).
## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"] 
> [Más información sobre el uso de Terraform en Azure](/azure/terraform)
> [!div class="nextstepaction"] 
> [Ejemplos de Terraform para Azure App Service](/azure/app-service/samples-terraform)