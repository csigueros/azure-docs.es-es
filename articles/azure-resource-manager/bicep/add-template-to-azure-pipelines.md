---
title: CI/CD con Azure Pipelines y archivos Bicep
description: En este artículo de inicio rápido, aprenderá a configurar la integración continua en Azure Pipelines mediante archivos Bicep. Muestra cómo usar una tarea de la CLI de Azure para implementar un archivo Bicep.
author: mumian
ms.topic: quickstart
ms.author: jgao
ms.date: 11/16/2021
ms.openlocfilehash: dbca9d692c96d65fa172e9d810e224427c9f5bfc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717025"
---
# <a name="quickstart-integrate-bicep-with-azure-pipelines"></a>Inicio rápido: Integración de Bicep en Azure Pipelines

En este artículo de inicio rápido se muestra cómo integrar archivos Bicep en Azure Pipelines para la integración continua e implementación continua (CI/CD).

Se brinda una breve introducción a la tarea de canalización que necesita para implementar un archivo Bicep. Si desea pasos más detallados sobre cómo configurar la canalización y el proyecto, consulte [Implementación de recursos de Azure mediante Bicep y Azure Pipelines](/learn/paths/bicep-azure-pipelines/) en **Microsoft Learn**.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

Necesita una organización de Azure DevOps. Si no tiene ninguna, [cree una gratis](/azure/devops/pipelines/get-started/pipelines-sign-up). Si su equipo ya tiene una organización de Azure DevOps, compruebe que es administrador del proyecto de Azure DevOps que quiere utilizar.

Tiene que haber configurado una [conexión al servicio](/azure/devops/pipelines/library/connect-to-azure) en la suscripción de Azure. Las tareas en la canalización se ejecutan con la identidad de la entidad de servicio. Para conocer los pasos para crear la conexión, consulte [Crear un proyecto de DevOps](../templates/deployment-tutorial-pipeline.md#create-a-devops-project).

Necesita un [archivo Bicep](./quickstart-create-bicep-use-visual-studio-code.md) que defina la infraestructura del proyecto. Este archivo está en un repositorio.

## <a name="create-pipeline"></a>Creación de una canalización

1. En la organización de Azure DevOps, seleccione **Canalizaciones** y **Nueva canalización**.

   ![Adición de una nueva canalización](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique dónde está almacenado el código.

   ![Selección del origen del código](./media/add-template-to-azure-pipelines/select-source.png)

1. Seleccione el repositorio que tiene el código para el proyecto.

   ![Selección del repositorio](./media/add-template-to-azure-pipelines/select-repo.png)

1. Seleccione **Canalización inicial** para el tipo de canalización que quiere crear.

   ![Selección de la canalización](./media/add-template-to-azure-pipelines/select-pipeline.png)

## <a name="azure-cli-task"></a>Tarea CLI de Azure

Reemplace la canalización inicial por el siguiente código YAML. Crea un grupo de recursos e implementa un archivo Bicep mediante una [tarea de la CLI de Azure](/azure/devops/pipelines/tasks/deploy/azure-cli):

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: 'exampleRG'
  location: '<your-resource-group-location>'
  templateFile: './main.bicep'
pool:
  vmImage: $(vmImageName)

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: $(azureServiceConnection)
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az group create --name $(resourceGroupName) --location $(location)
      az deployment group create --resource-group $(resourceGroupName) --template-file $(templateFile)
```

La tarea de la CLI de Azure toma las siguientes entradas:

* `azureSubscription`: proporcione el nombre de la conexión de servicio que creó.  Consulte [Requisitos previos](#prerequisites).
* `scriptType`: use **bash**.
* `scriptLocation`: use **inlineScript** o **scriptPath**. Si especifica **scriptPath**, también deberá especificar un parámetro `scriptPath`.
* `inlineScript`: especifique las líneas de script.  El script proporcionado en el ejemplo implementa un archivo Bicep denominado *main.bicep*.

Seleccione **Guardar**. La canalización de compilación se ejecuta automáticamente. Vuelva al resumen de la canalización de compilación y vea el estado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure dejen de ser necesarios, use la CLI de Azure o Azure PowerShell para eliminar el grupo de recursos y el recurso del servidor del artículo de inicio rápido.

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
> [Implementación de archivos de Bicep mediante Acciones de GitHub](deploy-github-actions.md)
