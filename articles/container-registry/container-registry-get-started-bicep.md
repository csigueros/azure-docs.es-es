---
title: 'Inicio rápido: Creación de un registro mediante Bicep'
description: Obtenga información sobre cómo crear una instancia de Azure Container Registry mediante un archivo de Bicep.
services: azure-resource-manager
author: mumian
ms.author: jgao
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: azure-resource-manager
ms.openlocfilehash: 689048afd913cf081f993a5238c42f0914119acb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095175"
---
# <a name="quickstart-create-a-container-registry-by-using-a-bicep-file"></a>Inicio rápido: Creación de un registro de contenedor mediante un archivo de Bicep

En este inicio rápido, se muestra cómo crear una instancia de Azure Container Registry mediante un archivo de Bicep.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="review-the-bicep-file"></a>Revisión del archivo de Bicep

Use Visual Studio Code o su editor favorito para crear un archivo con el siguiente contenido y asígnele el nombre **main.bicep**:

```bicep
@minLength(5)
@maxLength(50)
@description('Provide a globally unique name of your Azure Container Registry')
param acrName string = 'acr${uniqueString(resourceGroup().id)}'

@description('Provide a location for the registry.')
param location string = resourceGroup().location

@description('Provide a tier of your Azure Container Registry.')
param acrSku string = 'Basic'

resource acrResource 'Microsoft.ContainerRegistry/registries@2021-06-01-preview' = {
  name: acrName
  location: location
  sku: {
    name: acrSku
  }
  properties: {
    adminUserEnabled: false
  }
}
```

El siguiente recurso se define en el archivo de Bicep:

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : cree una instancia de Azure Container Registry.

Encontrará más ejemplos de plantillas de Azure Container Registry en la [galería de plantillas de inicio rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-bicep-file"></a>Implementación del archivo de Bicep

Para implementar el archivo que creó, abra PowerShell o la CLI de Azure. Si quiere usar el terminal integrado de Visual Studio Code, seleccione la combinación de teclas `ctrl` + ```` ` ````. Cambie el directorio actual a la ubicación actual del archivo Bicep.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name myContainerRegRG --location centralus

az deployment group create --resource-group myContainerRegRG --template-file main.bicep --parameters acrName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name myContainerRegRG -Location centralus

New-AzResourceGroupDeployment -ResourceGroupName myContainerRegRG -TemplateFile ./main.bicep -acrName "{your-unique-name}"
```

---

> [!NOTE]
> Reemplace **{your-unique-name}** , incluidos los corchetes, por un nombre de registro de contenedor único.

Una vez finalizada la implementación, debería mostrarse un mensaje indicando que la implementación se realizó correctamente.

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

Use Azure Portal o una herramienta como la CLI de Azure para examinar las propiedades del registro de contenedor.

1. En el portal, busque **Registros de contenedor** y seleccione el registro de contenedor que creó.

1. En la página **Información general**, observe el valor del campo **Servidor de inicio de sesión** del registro. Use este identificador URI cuando utilice Docker para etiquetar e insertar imágenes en el registro. Para obtener información al respecto, consulte [Inserción de la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-bicep/registry-overview.png" alt-text="Información general del registro":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite los recursos, elimine el grupo de recursos y el registro. Para ello, vaya a Azure Portal, seleccione el grupo de recursos que contiene el registro y, luego, seleccione **Eliminar grupo de recursos**.

Eliminación de un grupo de recursos

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Azure Container Registry con un archivo de Bicep. Siga los tutoriales de Azure Container Registry para información más detallada de ACR.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Para obtener un tutorial paso a paso que le guíe en el proceso de creación de un archivo de Bicep, consulte:

> [!div class="nextstepaction"]
> [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)
