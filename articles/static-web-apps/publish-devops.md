---
title: 'Tutorial: Publicación de aplicaciones web estáticas de Azure con Azure DevOps'
description: Aprenda a usar Azure DevOps para publicar aplicaciones web estáticas de Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: apedward
ms.openlocfilehash: bc1e6c5ad5423f48eed50c65237b0d42a4e61d7a
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236225"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Tutorial: Publicación de aplicaciones web estáticas de Azure con Azure DevOps

En este artículo se muestra cómo implementar [aplicaciones web estáticas de Azure](./overview.md) mediante [Azure DevOps](https://dev.azure.com/).

En este tutorial, aprenderá a:

- Configurar un sitio de Azure Static Web Apps
- Crear una canalización de Azure Pipeline para compilar y publicar una aplicación web estática

## <a name="prerequisites"></a>Prerrequisitos

- Una **cuenta activa de Azure**: en caso de no tener una, puede [crear una cuenta gratuita](https://azure.microsoft.com/free/).
- Un **proyecto de Azure DevOps:** si no tiene ninguno, puede [crear un proyecto de forma gratuita](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
  - Azure DevOps incluye **Azure Pipelines**. Si necesita ayuda para comenzar a usar Azure Pipelines, consulte [Creación de la primera canalización](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops).
  - Actualmente, la tarea de canalización de aplicaciones web estáticas solo funciona en máquinas **Linux**. Al ejecutar la canalización mencionada a continuación, asegúrese de que se ejecuta en una máquina virtual Linux.

## <a name="create-a-static-web-app-in-an-azure-devops"></a>Creación de una aplicación web estática en Azure DevOps

  > [!NOTE]
  > Si tiene una aplicación existente en el repositorio, puede ir directamente a la sección siguiente.

1. Vaya al repositorio en Azure Repos.

1. Seleccione **Importar** para iniciar la importación de una aplicación de ejemplo.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Repositorio de DevOps":::

1. En **Dirección URL de clonación**, escriba `https://github.com/staticwebdev/vanilla-api.git`.

1. Seleccione **Import** (Importar).

## <a name="create-a-static-web-app"></a>Creación de una aplicación web estática

1. Acceda a [Azure Portal](https://portal.azure.com).

1. Seleccione **Crear un recurso**.

1. Busque **Static Web Apps**.

1. Seleccione **Static Web Apps**.

1. Seleccione **Crear**.

1. Cree una nueva aplicación web estática con los valores siguientes.

    :::image type="content" source="media/publish-devops/azure-portal-static-web-apps-devops.png" alt-text="Detalles de la implementación - Otro":::

    | Parámetro | Valor |
    |---|---|
    | Suscripción | El nombre de la suscripción de Azure. |
    | Grupo de recursos | Seleccione un nombre de grupo existente o cree uno nuevo. |
    | Nombre | Escriba **myDevOpsApp**. |
    | Tipo de plan de hospedaje | Seleccione **Gratis**. |
    | Region | Seleccione la región más cercana a la suya. |
    | Source | Seleccione **Otros**. |

1. Seleccionar **Revisar y crear**.

1. Seleccione **Crear**.

1. Una vez que la implementación sea correcta, seleccione **Ir al grupo de recursos**.

1. Seleccione **Manage deployment token** (Administrar token de implementación).

1. Copie el **token de implementación** y pegue el valor del token de implementación en un editor de texto para usarlo en otra pantalla.

    > [!NOTE]
    > Este valor se reserva ahora porque copiará y pegará más valores en los pasos siguientes.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token de implementación":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Creación de la tarea de canalización de Azure DevOps

1. Vaya al repositorio de Azure Repos que se creó anteriormente.

2. Seleccione **Configurar la compilación**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Canalización de compilación":::

3. En la pantalla *Configurar la canalización*, seleccione **Canalización inicial**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configurar canalización":::

4. Copie el siguiente código YAML y reemplace la configuración generada en la canalización por este código.

    ```yaml
    trigger:
      - main

    pool:
      vmImage: ubuntu-latest

    steps:
      - checkout: self
        submodules: true
      - task: AzureStaticWebApp@0
        inputs:
          app_location: '/src'
          api_location: 'api'
          output_location: '/src'
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Si no va a usar la aplicación de ejemplo, los valores de `app_location`, `api_location` y `output_location` deben cambiar para que coincidan con los valores de la aplicación.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    El valor `azure_static_web_apps_api_token` es autoadministrado y se configura manualmente.

5. Seleccione **Variables**.

6. Seleccione **Nueva variable**.

7. Asigne a la variable el nombre **deployment_token** (que coincide con el nombre del flujo de trabajo).

8. Copie el token de implementación que pegó anteriormente en un editor de texto.

9. Pegue el token de implementación en el cuadro _Valor_.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token de variable":::

10. Seleccione **Mantener este valor como secreto**.

11. Seleccione **Aceptar**.

12. Seleccione **Guardar** para volver al archivo YAML de la canalización.

13. Seleccione **Guardar y ejecutar** para que se abra el cuadro de diálogo del mismo nombre.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Canalización":::

14. Seleccione **Guardar y ejecutar** para ejecutar la canalización.

15. Una vez que la implementación se haya realizado correctamente, vaya a la página de **información general** de Azure Static Web Apps, que incluye vínculos a la configuración de implementación. Observe cómo el vínculo de _origen_ apunta ahora a la rama y ubicación del repositorio de Azure DevOps.

16. Seleccione la **dirección URL** para ver el sitio web recién implementado.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Ubicación de implementación":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Limpie los recursos que implementó eliminando el grupo de recursos.

1. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda.
2. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.
3. Seleccione el nombre del grupo de recursos que ha utilizado en este tutorial.
4. Seleccione **Eliminar grupo de recursos** del menú superior.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de Azure Static Web Apps](./configuration.md)
