---
title: Implementación de archivos de Bicep mediante Acciones de GitHub
description: En esta guía de inicio rápido, aprenderá a implementar archivos Bicep mediante Acciones de GitHub.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/16/2021
ms.custom: github-actions-azure
ms.openlocfilehash: 548688e2359043485df7b60eb51cdaea243c3540
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716875"
---
# <a name="quickstart-deploy-bicep-files-by-using-github-actions"></a>Guía de inicio rápido: implementación de archivos de Bicep mediante Acciones de GitHub

[Acciones de GitHub](https://docs.github.com/en/actions) es un conjunto de características de GitHub que automatizan los flujos de trabajo de desarrollo de software.

En esta guía de inicio rápido, puede usar la [acción de GitHub para la implementación de Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) y así automatizar la implementación de un archivo de Bicep en Azure.

Proporciona una breve introducción a las acciones de GitHub y los archivos Bicep. Si quiere ver pasos más detallados sobre cómo configurar las acciones y el proyecto de GitHub, consulte [Ruta de aprendizaje: Implementación de recursos de Azure mediante acciones de Bicep y GitHub](/learn/paths/bicep-github-actions).

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de GitHub. Si no tiene ninguna, regístrese [gratis](https://github.com/join).
- Un repositorio de GitHub para almacenar los archivos de Bicep y los archivos del flujo de trabajo. Para crear uno, vea [Creación de un repositorio](https://docs.github.com/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos. Más adelante en esta guía de inicio rápido, implementará el archivo Bicep en este grupo de recursos.

```azurecli-interactive
az group create -n exampleRG -l westus
```

## <a name="generate-deployment-credentials"></a>Genere las credenciales de implementación.

La acción de GitHub se ejecuta en una identidad. Use el comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) para crear una [entidad de servicio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) para la identidad.

Sustituya el marcador de posición `myApp` por el nombre de aplicación. Reemplace `{subscription-id}` con el Id. de suscripción.

```azurecli-interactive
az ad sp create-for-rbac --name myApp --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/exampleRG --sdk-auth
```

> [!IMPORTANT]
> El ámbito del ejemplo anterior se limita al grupo de recursos. Se recomienda conceder el acceso mínimo necesario.

La salida es un objeto JSON con las credenciales de asignación de roles que proporcionan acceso a la aplicación App Service similar al siguiente. Copie este objeto JSON para más adelante. Solo necesitará las secciones con los valores `clientId`, `clientSecret`, `subscriptionId` y `tenantId`.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secrets"></a>Configuración de los secretos de GitHub

Cree secretos para las credenciales de Azure, el grupo de recursos y las suscripciones.

1. Vaya al repositorio en [GitHub](https://github.com/).

1. Seleccione **Settings > Secrets > New secret** (Configuración > Secretos > Secreto nuevo).

1. Pegue la salida JSON completa del comando de la CLI de Azure en el campo de valor del secreto. Asigne al secreto el siguiente nombre: `AZURE_CREDENTIALS`.

1. Cree otro secreto denominado `AZURE_RG`. Agregue el nombre del grupo de recursos al campo de valor del secreto (`exampleRG`).

1. Cree otro secreto denominado `AZURE_SUBSCRIPTION`. Agregue el identificador de la suscripción al campo de valor del secreto (ejemplo: `90fd3f9d-4c61-432d-99ba-1273f236afa2`).

## <a name="add-a-bicep-file"></a>Incorporación de un archivo de Bicep

Agregue un archivo de Bicep al repositorio de GitHub. El siguiente archivo de Bicep crea una cuenta de almacenamiento:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep" :::

El archivo de Bicep requiere un parámetro denominado **storagePrefix** que tiene entre tres y once caracteres.

Puede colocar el archivo en cualquier parte del repositorio. En el ejemplo de flujo de trabajo de la sección siguiente, se supone que el archivo de Bicep se denomina **main.bicep** y se almacena en la raíz del repositorio.

## <a name="create-workflow"></a>Creación del flujo de trabajo

Un flujo de trabajo define los pasos que se ejecutarán cuando se desencadene. Es un archivo YAML (.yml) en la ruta de acceso **.github/workflows/** del repositorio. La extensión de archivo de flujo de trabajo puede ser **.yml** o **.yaml**.

Para crear un flujo de trabajo, siga estos pasos:

1. En el repositorio de GitHub, seleccione **Actions** (Acciones) en el menú superior.
1. Seleccione **New workflow** (Nuevo flujo de trabajo).
1. Seleccione **Set up a workflow yourself** (Configurar un flujo de trabajo personalmente).
1. Cambie el nombre del archivo de flujo de trabajo si prefiere otro distinto a **main.yml**. Por ejemplo: **deployBicepFile.yml**.
1. Reemplace el contenido del archivo .yml por el código siguiente:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy Bicep file
        - name: deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./main.bicep
            parameters: storagePrefix=mystore
            failOnStdErr: false
    ```

    Reemplace `mystore` por su propio prefijo de nombre de cuenta de almacenamiento.

    > [!NOTE]
    > En su lugar, puede especificar un archivo de parámetros de formato JSON en la acción de implementación de ARM (ejemplo: `.azuredeploy.parameters.json`).

    La primera sección del archivo de flujo de trabajo incluye:

    - **name**: El nombre del flujo de trabajo.
    - **on**: el nombre de los eventos de GitHub que desencadenan el flujo de trabajo. El flujo de trabajo se desencadena cuando hay un evento de inserción en la rama principal.

1. Seleccione **Start commit** (Iniciar confirmación).
1. Seleccione **Commit directly to the main branch** (Confirmar directamente en la rama principal).
1. Seleccione **Commit new file** (Confirmar nuevo archivo) (o bien **Commit changes** (Confirmar cambios)).

La actualización del archivo de flujo de trabajo o del archivo de Bicep desencadena el flujo de trabajo. El flujo de trabajo se inicia justo después de confirmar los cambios.

## <a name="check-workflow-status"></a>Comprobación del estado del flujo de trabajo

1. Seleccione la pestaña **Actions** (Acciones). Debería ver un flujo de trabajo **Create deployStorageAccount.yml** (Crear deployStorageAccount.yml) en la lista. El flujo de trabajo tarda un par de minutos en ejecutarse.
1. Seleccione el flujo de trabajo para abrirlo.
1. Seleccione **Run ARM deploy** (Ejecutar implementación de ARM) en el menú para comprobar la implementación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando el repositorio y el grupo de recursos ya no sean necesarios, limpie los recursos que implementó eliminando el grupo de recursos y el repositorio de GitHub.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Estructura y sintaxis de un archivo Bicep](file.md)
