---
title: Creación de una aplicación de App Service mediante Azure Bicep
description: Cree su primera aplicación en Azure App Service en segundos mediante Azure Bicep, que es una de las muchas maneras de realizar una implementación en App Service.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.openlocfilehash: ff9136889a475e8e98406476ea503982025d298d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061961"
---
# <a name="create-app-service-app-using-bicep"></a>Creación de una aplicación de App Service mediante Azure Bicep

Empiece a usar [Azure App Service](overview.md) mediante la implementación de una aplicación en la nube con un archivo de [Bicep](/azure/azure-resource-manager/bicep/) y la [CLI de Azure](/cli/azure/get-started-with-azure-cli) en Cloud Shell. Dado que usa un nivel de App Service gratuito, completar este inicio rápido no supone ningún costo.

Bicep es un lenguaje específico del dominio (DSL) que usa sintaxis declarativa para implementar recursos de Azure. Brinda sintaxis concisa, seguridad de tipos confiable y compatibilidad con la reutilización de código. Puede usar Bicep en lugar de JSON para desarrollar las plantillas de Azure Resource Manager ([plantillas de ARM](/azure/azure-resource-manager/templates/overview)). La sintaxis JSON para crear una plantilla de ARM puede ser detallada y exigir expresiones complicadas. La sintaxis de Bicep reduce esa complejidad y mejora la experiencia de desarrollo. Bicep es una abstracción transparente del código JSON de plantillas de ARM, y no pierde ninguna de las funcionalidades de las plantillas JSON. Durante la implementación, la CLI de Bicep transpila un archivo de Bicep en un JSON de plantilla de ARM.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Para crear recursos de forma eficaz con Bicep, deberá configurar un [entorno de desarrollo](/azure/azure-resource-manager/bicep/install) de Bicep. La extensión de Bicep para [Visual Studio Code](https://code.visualstudio.com/) proporciona compatibilidad con el lenguaje y la finalización automática de recursos. La extensión le ayuda a crear y validar archivos de Bicep y es recomendable para los desarrolladores que crearán recursos mediante Bicep después de completar este inicio rápido.

## <a name="review-the-template"></a>Revisión de la plantilla

A continuación, se muestra la plantilla usada en este inicio rápido. Implementa un plan y una aplicación de App Service en Linux y una aplicación "Hola mundo" Node.js de ejemplo desde el repositorio [Ejemplos de Azure](https://github.com/Azure-Samples).

```bicep
param webAppName string = uniqueString(resourceGroup().id) // Generate unique String for web app name
param sku string = 'F1' // The SKU of App Service Plan
param linuxFxVersion string = 'node|14-lts' // The runtime stack of web app
param location string = resourceGroup().location // Location for all resources
param repositoryUrl string = 'https://github.com/Azure-Samples/nodejs-docs-hello-world'
param branch string = 'master'
var appServicePlanName = toLower('AppServicePlan-${webAppName}')
var webSiteName = toLower('wapp-${webAppName}')
resource appServicePlan 'Microsoft.Web/serverfarms@2020-06-01' = {
  name: appServicePlanName
  location: location
  properties: {
    reserved: true
  }
  sku: {
    name: sku
  }
  kind: 'linux'
}
resource appService 'Microsoft.Web/sites@2020-06-01' = {
  name: webSiteName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: linuxFxVersion
    }
  }
}
resource srcControls 'Microsoft.Web/sites/sourcecontrols@2021-01-01' = {
  name: '${appService.name}/web'
  properties: {
    repoUrl: repositoryUrl
    branch: branch
    isManualIntegration: true
  }
}
```

En la plantilla se definen tres recursos de Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): permite crear un plan de App Service.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): permite crear una aplicación de App Service.
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols): permite crear una configuración de implementación de Git externa.

Esta plantilla contiene varios parámetros que están predefinidos para su comodidad. Consulte la tabla siguiente para ver los valores predeterminados de los parámetros y sus descripciones:

| Parámetros | Tipo    | Valor predeterminado                | Descripción |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nombre de la aplicación |
| ubicación   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Región de la aplicación |
| sku        | string  | "F1"                         | Tamaño de instancia  |
| linuxFxVersion   | string  | "NODE&#124;14-LTS"       | "Pila de lenguajes de programación &#124; Versión" |
| repositoryUrl    | string  | "https://github.com/Azure-Samples/nodejs-docs-hello-world"    | Repositorio de Git externo (opcional) |
| branch    | string  | "master"    | Rama predeterminada para el código de ejemplo |

---

## <a name="deploy-the-template"></a>Implementación de la plantilla

Copie y pegue la plantilla en el editor o IDE que prefiera y guarde el archivo en el directorio de trabajo local.

Aquí se usa la CLI de Azure para implementar la plantilla. También puede usar Azure Portal, Azure PowerShell y la API REST. Para obtener información sobre otros métodos de implementación, consulte [Comandos de implementación de Bicep](/azure/azure-resource-manager/bicep/deploy-cli).

El siguiente código crea un grupo de recursos, un plan de App Service y una aplicación web. Se han establecido un grupo de recursos, un plan de App Service y una ubicación predeterminados. Reemplace `<app-name>` por un nombre de aplicación único global (los caracteres válidos son `a-z`, `0-9` y `-`).

Abra un terminal donde esté instalada la CLI de Azure y ejecute el código que se incluye a continuación para crear una aplicación Node.js en Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --template-file <path-to-template>
```

Para implementar una pila de lenguajes diferente, actualice `linuxFxVersion` con los valores adecuados. A continuación se muestran ejemplos. Para mostrar las versiones actuales, ejecute el siguiente comando en Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Idioma    | Ejemplo:                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

## <a name="validate-the-deployment"></a>Validación de la implementación

Vaya a `http://<app_name>.azurewebsites.net/` y compruebe que se ha creado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite, [elimine el grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Documentación de Bicep](/azure/azure-resource-manager/bicep/)
> [!div class="nextstepaction"]
> [Ejemplos de Bicep para Azure App Service](/azure/app-service/samples-bicep)