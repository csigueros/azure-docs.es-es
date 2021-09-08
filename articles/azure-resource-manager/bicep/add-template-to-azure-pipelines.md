---
title: CI/CD con Azure Pipelines y archivos Bicep
description: Describe cómo configurar la integración continua en Azure Pipelines mediante archivos Bicep. Muestra cómo usar una tarea de la CLI de Azure para implementar un archivo Bicep.
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/23/2021
ms.openlocfilehash: 28050d926bf5b4042ceb5b94796550bc517eb977
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289201"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>Integración de Bicep con Azure Pipelines

Los archivos Bicep se pueden integrar con Azure Pipelines para la integración e implementación continuas (CI/CD). En este artículo, aprenderá a usar una tarea de canalización de la CLI de Azure para implementar un archivo Bicep.

## <a name="prepare-your-project"></a>Preparación del proyecto

En este artículo se presupone que su archivo Bicep y la organización de Azure DevOps están listos para crear la canalización. Los pasos siguientes muestran cómo asegurarse de que está listo:

* Tiene una organización de Azure DevOps. Si no tiene ninguna, [cree una gratis](/azure/devops/pipelines/get-started/pipelines-sign-up). Si su equipo ya tiene una organización de Azure DevOps, compruebe que es administrador del proyecto de Azure DevOps que quiere utilizar.

* Ha configurado una [conexión al servicio](/azure/devops/pipelines/library/connect-to-azure) en su suscripción de Azure. Las tareas en la canalización se ejecutan con la identidad de la entidad de servicio. Para conocer los pasos para crear la conexión, consulte [Crear un proyecto de DevOps](../templates/deployment-tutorial-pipeline.md#create-a-devops-project).

* Tiene un [archivo Bicep](./quickstart-create-bicep-use-visual-studio-code.md) que define la estructura del proyecto.

## <a name="create-pipeline"></a>Creación de una canalización

1. Si no ha agregado una canalización anteriormente, tiene que crear una. En la organización de Azure DevOps, seleccione **Canalizaciones** y **Nueva canalización**.

   ![Adición de una nueva canalización](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique dónde está almacenado el código. En la imagen siguiente se muestra la selección de **GIT de Azure Repos**.

   ![Selección del origen del código](./media/add-template-to-azure-pipelines/select-source.png)

1. Desde ese origen, seleccione el repositorio que tiene el código para el proyecto.

   ![Selección del repositorio](./media/add-template-to-azure-pipelines/select-repo.png)

1. Seleccione el tipo de canalización que quiere crear. Puede seleccionar **Canalización inicial**.

   ![Selección de la canalización](./media/add-template-to-azure-pipelines/select-pipeline.png)

Está listo para agregar una tarea de Azure PowerShell o las tareas de copia de archivos y de implementación.

## <a name="azure-cli-task"></a>Tarea CLI de Azure

El siguiente archivo YAML crea un grupo de recursos e implementa un archivo Bicep mediante una [tarea de la CLI de Azure](/azure/devops/pipelines/tasks/deploy/azure-cli):

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: '<your-resource-group-name>'
  location: '<your-resource-group-location>'
  templateFile: './azuredeploy.bicep'
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

Una tarea de la CLI de Azure toma las siguientes entradas:

* `azureSubscription`: proporcione el nombre de la conexión de servicio que creó.  Consulte [Preparación del proyecto](#prepare-your-project).
* `scriptType`: use **bash**.
* `scriptLocation`: use **inlineScript** o **scriptPath**. Si especifica **scriptPath**, también deberá especificar un parámetro `scriptPath`.
* `inlineScript`: especifique las líneas de script.  El script proporcionado en el ejemplo compila un archivo Bicep llamado *azuredeploy.bicep* que existe en la raíz del repositorio.

## <a name="next-steps"></a>Pasos siguientes

* Para usar la operación what-if en una canalización, consulte [Prueba de plantillas de Resource Manager con What-If en una canalización](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
* Para más información sobre el uso del archivo Bicep con Acciones de GitHub, consulte [Implementación de archivos Bicep mediante Acciones de GitHub](./deploy-github-actions.md).