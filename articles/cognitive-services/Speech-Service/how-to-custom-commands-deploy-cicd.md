---
title: Implementación continua con Azure DevOps (versión preliminar)
titleSuffix: Azure Cognitive Services
description: En este artículo, aprenderá a configurar la implementación continua para sus aplicaciones de Comandos personalizados. Creará scripts para admitir los flujos de trabajo de la implementación continua.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: c83c52efb8ff6eb59d49fb930ae4edf0b2f9c335
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011852"
---
# <a name="continuous-deployment-with-azure-devops"></a>Implementación continua con Azure DevOps

En este artículo, aprenderá a configurar la implementación continua para sus aplicaciones de Comandos personalizados. Se le proporcionarán los scripts para admitir el flujo de trabajo de CI/CD.

## <a name="prerequisite"></a>Requisito previo
> [!div class = "checklist"]
> * Una aplicación de Comandos personalizados para desarrollo (DEV)
> * Una aplicación de Comandos personalizados para producción (PROD)
> * Suscribirse a [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Exportación, importación, publicación

Los scripts están hospedados en la carpeta [Asistente de voz de Cognitive Services: comandos personalizados](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clone los scripts del directorio de Bash de su repositorio. Asegúrese de conservar la misma ruta de acceso.

### <a name="set-up-a-pipeline"></a>Configuración de una canalización 

1. Vaya a **Azure DevOps: Pipelines** y haga clic en "Nueva canalización".
1. En la sección **Conectar**, seleccione la ubicación del repositorio en el que se encuentran estos scripts.
1. En la sección **Seleccionar**, seleccione el repositorio.
1. En la sección **Configurar**, seleccione "Canalización inicial".
1. A continuación, se mostrará un editor con un archivo YAML, y deberá reemplazar la sección "steps" por este script.

    ```yaml
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Tenga en cuenta que estos scripts suponen que usa la región `westus2`; en caso contrario, actualice los argumentos de las tareas según corresponda.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que resalta el valor de la región en los argumentos.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. En el botón "Guardar y ejecutar", abra la lista desplegable y haga clic en "Guardar".

### <a name="hook-up-the-pipeline-with-your-application"></a>Establecimiento de un enlace entre la canalización y la aplicación

1. Diríjase a la página principal de la canalización.
1. En la lista desplegable de la esquina superior derecha, seleccione **Editar la canalización**. Se mostrará un editor de YAML. 
1. En la esquina superior derecha, junto al botón "Ejecutar", seleccione **Variables**. Haga clic en **Nueva variable**.
1. Agregue estas variables:
    
    | Variable | Descripción |
    | ------- | --------------- | ----------- |
    | SourceAppId | Id. de la aplicación de desarrollo |
    | TargetAppId | Id. de la aplicación de producción |
    | SubscriptionKey | Clave de suscripción que se usa para ambas aplicaciones |
    | Referencia cultural | Referencia cultural de las aplicaciones (es decir, en-us) |

    > [!div class="mx-imgBorder"]
    > ![Carga de Enviar actividad](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Haga clic en "Ejecutar" y, a continuación, haga clic en el "Trabajo" en ejecución. 

    Debería mostrarse una lista de tareas en ejecución que contenga: "exportar aplicación de origen", "importar a la aplicación de destino" y "entrenar y publicar aplicación de destino".

## <a name="deploy-from-source-code"></a>Implementación desde el código fuente

En caso de que quiera conservar la definición de la aplicación en un repositorio, le proporcionaremos los scripts para las implementaciones desde el código fuente. Dado que los scripts están en Bash, si usa Windows, deberá instalar el [subsistema de Linux](/windows/wsl/install-win10).

Los scripts están hospedados en la carpeta [Asistente de voz de Cognitive Services: comandos personalizados](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clone los scripts del directorio de Bash de su repositorio. Asegúrese de conservar la misma ruta de acceso.

### <a name="prepare-your-repository"></a>Preparación del repositorio

1. Cree un directorio para la aplicación; en nuestro ejemplo, cree uno denominado "apps".
1. Actualice los argumentos en el siguiente script de Bash y ejecútelo. Se importará el modelo de diálogo de la aplicación en el archivo myapp.json.
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumentos | Descripción |
    | ------- | --------------- | ----------- |
    | region | región de la aplicación; es decir, westus2. |
    | subscriptionkey | clave de suscripción del recurso de voz. |
    | appid | el id. de la aplicación de Comandos personalizados que quiere exportar. |

1. Inserte estos cambios en el repositorio.

### <a name="set-up-a-pipeline"></a>Configuración de una canalización 

1. Vaya a **Azure DevOps: Pipelines** y haga clic en "Nueva canalización".
1. En la sección **Conectar**, seleccione la ubicación del repositorio en el que se encuentran estos scripts.
1. En la sección **Seleccionar**, seleccione el repositorio.
1. En la sección **Configurar**, seleccione "Canalización inicial".
1. A continuación, se mostrará un editor con un archivo YAML, y deberá reemplazar la sección "steps" por este script.

    ```yaml
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > Estos scripts suponen que usa la región westus2; en caso contrario, actualice los argumentos de las tareas según corresponda.

1. En el botón "Guardar y ejecutar", abra la lista desplegable y haga clic en "Guardar".

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Establecimiento de un enlace entre la canalización y las aplicaciones de destino

1. Diríjase a la página principal de la canalización.
1. En la lista desplegable de la esquina superior derecha, seleccione **Editar la canalización**. Se mostrará un editor de YAML. 
1. En la esquina superior derecha, junto al botón "Ejecutar", seleccione **Variables**. Haga clic en **Nueva variable**.
1. Agregue estas variables:

    | Variable | Descripción |
    | ------- | --------------- | ----------- |
    | TargetAppId | Id. de la aplicación de producción |
    | SubscriptionKey | Clave de suscripción que se usa para ambas aplicaciones |
    | Referencia cultural | Referencia cultural de las aplicaciones (es decir, en-us) |

1. Haga clic en "Ejecutar" y, a continuación, haga clic en el "Trabajo" en ejecución.
    Debería mostrarse una lista de tareas en ejecución que contenga: "importar aplicación" y "entrenar y publicar aplicación".

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Consulte los ejemplos en GitHub](https://aka.ms/speech/cc-samples)
