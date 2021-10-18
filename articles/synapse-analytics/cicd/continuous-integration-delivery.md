---
title: Integración y entrega continuas en Azure Synapse Analytics
description: Aprenda a usar la integración continua y entrega continua (CI/CD) para implementar cambios entre entornos en un área de trabajo de Azure Synapse Analytics.
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3269bfed98264574d5b44759cceae6e1613eb5f8
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729394"
---
# <a name="continuous-integration-and-delivery-for-an-azure-synapse-analytics-workspace"></a>Integración y entrega continuas en un área de trabajo de Azure Synapse Analytics

La integración continua (CI) es el proceso de automatización de la compilación y las pruebas de código cada vez que un miembro del equipo confirma un cambio en el control de versiones. La entrega continua (CD) es el proceso de compilación, pruebas, configuración e implementación desde varios entornos de prueba o ensayo en un entorno de producción.

En un área de trabajo de Azure Synapse Analytics, CI/CD mueve todas las entidades de un entorno (desarrollo, prueba, producción) a otro. La promoción del área de trabajo a otra área de trabajo es un proceso de dos partes. En primer lugar, use una [plantilla de Azure Resource Manager](../../azure-resource-manager/templates/overview.md) para crear o actualizar recursos del área de trabajo (los grupos y el área de trabajo). Luego migre artefactos, como scripts de SQL y cuadernos, definiciones de trabajos de Spark, canalizaciones, conjuntos de datos y flujos de datos, mediante las herramientas de CI/CD de Azure Synapse en Azure DevOps o en GitHub. 

En este artículo se explica cómo usar una canalización de versión de Azure DevOps y Acciones de GitHub para automatizar la implementación de un área de trabajo de Azure Synapse en varios entornos.

## <a name="prerequisites"></a>Prerrequisitos

Para automatizar la implementación de un área de trabajo de Azure Synapse en varios entornos, deben haberse aplicado los siguientes requisitos previos y configuraciones.

### <a name="azure-devops"></a>Azure DevOps

- Prepare un proyecto de Azure DevOps para ejecutar la canalización de versión.
- [Conceda a los usuarios que vayan a registrar código acceso Básico en el nivel de organización](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true) para que puedan ver el repositorio.
- Conceda permiso de propietario al repositorio de Azure Synapse.
- Asegúrese de que ha creado un agente de máquina virtual de Azure DevOps auto-hospedado o use un agente hospedado de Azure DevOps.
- Conceda permisos para [ crear una conexión del servicio Azure Resource Manager para el grupo de recursos](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true).
- Los administradores de Azure Active Directory (Azure AD) deben [instalar la extensión Azure DevOps Synapse Workspace Deployment Agent en la organización de Azure DevOps](/azure/devops/marketplace/install-extension).
- Cree o designe una cuenta de servicio existente para que se ejecute la canalización. Puede usar un token de acceso personal, en lugar de una cuenta de servicio, pero las canalizaciones no funcionarán después de que se elimine la cuenta de usuario.

### <a name="github"></a>GitHub

- Cree un repositorio de GitHub que contenga los artefactos del área de trabajo de Azure Synapse y la plantilla del área de trabajo. 
- Asegúrese de haber creado un ejecutor auto-hospedado o use uno hospedado en GitHub.

### <a name="azure-active-directory"></a>Azure Active Directory

- En Azure AD, si va a usar una entidad de servicio, cree una para usarla en la implementación. 
- Si va a usar una identidad administrada, habilite la identidad administrada asignada por el sistema en la máquina virtual de Azure como agente o ejecutor y luego agréguela a Azure Synapse Studio como administrador de Synapse.
- Use el rol de administrador de Azure AD para llevar a cabo estas acciones.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Estos requisitos previos se pueden automatizar e implementar mediante la misma canalización, una plantilla de ARM o la CLI de Azure, aunque estos procesos no se explican en este artículo.

- El área de trabajo "de origen" que se usa para el desarrollo se debe configurar con un repositorio de Git en Azure Synapse Studio. Para obtener más información, vea [Control de código fuente en Azure Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Configure un área de trabajo en blanco para implementarla:

  1. Cree una nueva área de trabajo de Azure Synapse.
  1. Conceda al agente de máquina virtual y a la entidad de servicio permiso de colaborador en el grupo de recursos en el que está hospedada la nueva área de trabajo.
  1. En el área de trabajo, no configure la conexión del repositorio de Git.
  1. En Azure Portal, busque la nueva área de trabajo de Azure Synapse y luego concédase a sí mismo y al usuario que va a ejecutar la canalización de Azure DevOps del área de trabajo de Azure Synapse permiso de propietario. 
  1. Agregue el agente de máquina virtual de Azure DevOps y la entidad de servicio al rol de colaborador del área de trabajo. (El rol debe haberse heredado, pero compruebe que es así).
  1. En el área de trabajo de Azure Synapse, vaya a **Studio** > **Administrar** > **Control de acceso**. Agregue el agente de máquina virtual de Azure DevOps y la entidad de servicio al grupo de administradores del área de trabajo.
  1. Abra la cuenta de almacenamiento que se usa para el área de trabajo. En el panel **Administración de identidad y acceso**, agregue el agente de máquina virtual y la entidad de servicio al rol de colaborador de datos de blob de Storage.
  1. Cree un almacén de claves en la suscripción de soporte técnico y asegúrese de que tanto el área de trabajo existente como la nueva tienen, al menos, permisos GET y LIST en el almacén.
  1. Para que la implementación automatizada funcione, asegúrese de que las cadenas de conexión especificadas en los servicios vinculados están en el almacén de claves.

### <a name="other-prerequisites"></a>Otros requisitos previos
 
- Los grupos de Spark y los entornos de ejecución de integración autohospedados no se crean en una tarea de implementación del área de trabajo. Si tiene un servicio vinculado que use un entorno de ejecución de integración autohospedado, cree este último manualmente en la nueva área de trabajo.
- Si los elementos del área de trabajo de desarrollo están asociados a los grupos concretos, asegúrese de crear o parametrizar los mismos nombres para los grupos del área de trabajo de destino en el archivo de parámetros.  
- Si los grupos de SQL aprovisionados se pausan al intentar realizar la implementación, esta podría no realizarse.

Para obtener más información, vea [CI/CD en Azure Synapse Analytics, parte 4: la canalización de versión](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>Configuración de una canalización de versión en Azure DevOps

En esta sección va a aprender a implementar un área de trabajo de Azure Synapse en Azure DevOps. 

1. En [Azure DevOps](https://dev.azure.com/), abra el proyecto creado para la versión.

1. En el menú izquierdo,seleccione **Canalizaciones** > **Versiones**.

    :::image type="content" source="media/create-release-pipeline.png" alt-text="Captura de pantalla que muestra la selección de Canalizaciones y luego de Versiones en el menú de Azure DevOps.":::        
 
1. Selecciona **Nueva canalización**. Si tiene canalizaciones existentes, seleccione **Nueva** > **Nueva canalización de versión**.

1. Seleccione la plantilla **Fase vacía**.

    :::image type="content" source="media/create-release-select-empty.png" alt-text="Captura de pantalla que muestra la selección de la plantilla Trabajo vacío.":::

1. En **Nombre de la fase**, escriba el nombre del entorno.

1. Seleccione **Agregar artefacto** y luego seleccione el repositorio de Git configurado con Azure Synapse Studio en el entorno de desarrollo. Seleccione el repositorio de Git en el que administra los grupos y la plantilla de ARM del área de trabajo. Si usa GitHub como origen, cree una conexión de servicio para la cuenta de GitHub y los repositorios de incorporación de cambios. Para obtener más información, vea [Conexiones de servicio](/azure/devops/pipelines/library/service-endpoints).

    :::image type="content" source="media/release-creation-github.png" alt-text="Captura de pantalla que muestra la selección de GitHub para agregar una rama de publicación para un nuevo artefacto.":::

1. Seleccione la rama de plantilla de ARM del recurso. En **Versión predeterminada**, seleccione **Más reciente de la rama predeterminada**.

    :::image type="content" source="media/release-creation-arm-template-branch.png" lightbox="media/release-creation-arm-template-branch.png" alt-text="Captura de pantalla que muestra la configuración de la rama de plantilla de ARM del recurso.":::

1. En la **Rama predeterminada** de los artefactos, seleccione la [rama de publicación](source-control.md#configure-publishing-settings) del repositorio. De manera predeterminada, la rama de publicación es `workspace_publish`. En **Versión predeterminada**, seleccione **Más reciente de la rama predeterminada**.

    :::image type="content" source="media/release-creation-publish-branch.png" alt-text="Captura de pantalla que muestra el establecimiento de la rama de artefactos.":::

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-a-resource"></a>Configuración de una tarea de fase para una plantilla de ARM a fin de crear y actualizar un recurso 

Si tiene una plantilla de ARM que implementa un recurso, como un área de trabajo de Azure Synapse, un grupo de Spark y de SQL o un almacén de claves, agregue una tarea de implementación de Azure Resource Manager para crear o actualizar esos recursos:

1. En la vista de fase, seleccione **Ver tareas de la fase**.

    :::image type="content" source="media/release-creation-stage-view.png" alt-text="Captura de pantalla que muestra el establecimiento de la vista de la fase.":::

1. Cree una nueva tarea. Busque **Implementación de una plantilla de Resource Manager** y, después, seleccione **Agregar**.

1. En la pestaña **Tareas** de la implementación, seleccione la suscripción, el grupo de recursos y la ubicación del área de trabajo. Proporcione las credenciales si es necesario.

1. En **Acción**, seleccione **Creación o actualización del grupo de recursos**.

1. En **Plantilla**, seleccione el botón de puntos suspensivos ( **…** ). Vaya a la plantilla de ARM del área de trabajo.

1. En **Parámetros de la plantilla**, seleccione **…** para elegir el archivo de parámetros.

    :::image type="content" source="media/pools-resource-deploy.png" lightbox="media/pools-resource-deploy.png" alt-text="Captura de pantalla que muestra la implementación del área de trabajo y los grupos.":::

1. En **Reemplazar parámetros de plantilla**, seleccione **…** y escriba los valores de parámetro que quiere usar para el área de trabajo. 

1. En **Modo de implementación**, seleccione **Incremental**.

1. (Opcional) Agregue **Azure PowerShell** para la concesión y actualice la asignación de roles del área de trabajo. Si usa una canalización de versión para crear un área de trabajo de Azure Synapse, la entidad de servicio de la canalización se agrega como administrador predeterminado del área de trabajo. Puede ejecutar PowerShell para conceder a otras cuentas acceso al área de trabajo. 

    :::image type="content" source="media/release-creation-grant-permission.png" lightbox="media/release-creation-grant-permission.png" alt-text="Captura de pantalla que muestra la ejecución de un script de PowerShell para conceder permisos.":::

> [!WARNING]
> En el modo de implementación completa, se *eliminan* los recursos del grupo de recursos que no están especificados en la nueva plantilla de ARM. Para más información, consulte [Modos de implementación de Azure Resource Manager](../../azure-resource-manager/templates/deployment-modes.md).

### <a name="set-up-a-stage-task-for-azure-synapse-artifacts-deployment"></a>Configuración de una tarea de fase para la implementación de artefactos de Azure Synapse 

Use la extensión [Implementación de área de trabajo de Synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) para implementar otros elementos en el área de trabajo de Azure Synapse. Los elementos que puede implementar incluyen conjuntos de datos, scripts de SQL y cuadernos, una definición de trabajo de Spark, un flujo de datos, una canalización, un servicio vinculado, credenciales y un entorno de ejecución de integración.  

1. Busque y obtenga la extensión en [Visual Studio Marketplace](https://marketplace.visualstudio.com/azuredevops).

    :::image type="content" source="media/get-extension-marketplace.png" alt-text="Captura de pantalla que muestra la extensión Implementación de área de trabajo de Synapse como aparece en Visual Studio Marketplace.":::

1. Seleccione la organización de Azure DevOps en la que quiere instalar la extensión. 

    :::image type="content" source="media/install-extension.png" alt-text="Captura de pantalla que muestra la selección de una organización en la que instalar la extensión Implementación de área de trabajo de Synapse.":::

1. Asegúrese de que se haya concedido a la entidad de servicio de la canalización de Azure DevOps el permiso de suscripción y de que esté asignada como administrador del área de trabajo de Synapse. 

1. Para crear una nueva tarea, busque **Implementación de área de trabajo de Synapse** y seleccione **Agregar**.

    :::image type="content" source="media/add-extension-task.png" alt-text="Captura de pantalla que muestra la búsqueda de Implementación de área de trabajo de Synapse para crear una tarea.":::

1. En la tarea, junto a **Plantilla**, seleccione **…** para elegir el archivo de plantilla.

1. Junto a **Parámetros de la plantilla**, seleccione **…**  para elegir el archivo de parámetros.

1. Seleccione una conexión, un grupo de recursos y un nombre para el área de trabajo. 

1. Junto a **Reemplazar parámetros de plantilla**, seleccione **…** . Escriba los valores de parámetro que quiere usar para el área de trabajo, incluidas las cadenas de conexión y las claves de cuenta que se usan en los servicios vinculados. Para obtener más información, consulte [CI/CD en Azure Synapse Analytics](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434).

    :::image type="content" source="media/create-release-artifacts-deployment.png" lightbox="media/create-release-artifacts-deployment.png" alt-text="Captura de pantalla que muestra la configuración de la tarea de implementación de Synapse para el área de trabajo.":::

> [!IMPORTANT]
> En escenarios de CI/CD, el tipo de entorno de ejecución de integración de otros entornos debe ser el mismo. Por ejemplo, si tiene un entorno de ejecución de integración auto-hospedado en el entorno de desarrollo, el mismo entorno de ejecución de integración debe ser auto-hospedado en otros entornos, como los de prueba y producción. Del mismo modo, si va a compartir entornos de ejecución de integración entre varias fases, tiene que vincularlos y auto-hospedarlos en todos los entornos, como los de desarrollo, prueba y producción.

### <a name="create-a-release-for-deployment"></a>Creación de una versión para implementación 

Después de guardar todos los cambios, puede seleccionar **Crear versión** para crear manualmente una versión. Para aprender a automatizar la creación de versiones, vea [Desencadenadores de versión de Azure DevOps](/azure/devops/pipelines/release/triggers).

:::image type="content" source="media/release-creation-manually.png" lightbox="media/release-creation-manually.png" alt-text="Captura de pantalla que muestra el panel Nueva canalización de versión, con Crear versión resaltado.":::

## <a name="set-up-a-release-in-github-actions"></a>Configuración de una versión en Acciones de GitHub 

En esta sección va a aprender a crear flujos de trabajo de GitHub mediante Acciones de GitHub para la implementación del área de trabajo de Azure Synapse.

Puede usar la [Acción de GitHub para la plantilla de Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) con el fin de automatizar la implementación de una plantilla de ARM en Azure para el área de trabajo y los grupos de proceso.

### <a name="workflow-file"></a>Archivo de flujo de trabajo

Defina un flujo de trabajo de Acciones de GitHub en un archivo YAML (.yml) en la ruta de acceso */.github/workflows/* del repositorio. La definición contiene los distintos pasos y parámetros que componen el flujo de trabajo.

El archivo .yml tiene dos secciones:

|Sección  |Tareas  |
|---------|---------|
|**Autenticación** | 1. Defina una entidad de servicio. <br /> 2. Cree un secreto de GitHub. |
|**Implementar** | Implemente los artefactos del área de trabajo. |

### <a name="configure-github-actions-secrets"></a>Configuración de secretos de Acciones de GitHub

Los secretos de Acciones de GitHub son variables de entorno cifradas. Cualquier persona que tenga permiso de colaborador en este repositorio puede usar estos secretos para interactuar con Acciones en el repositorio.

1. En el repositorio de GitHub, seleccione la pestaña **Configuración** y luego **Secretos** > **Nuevo secreto del repositorio**.

    :::image type="content" source="media/create-secret-new.png" lightbox="media/create-secret-new.png" alt-text="Captura de pantalla que muestra los elementos de GitHub que se van a seleccionar para crear un nuevo secreto del repositorio.":::

1. Agregue un nuevo secreto para el identificador de cliente, y un nuevo secreto de cliente si usa la entidad de servicio para la implementación. También puede guardar el identificador de suscripción y el identificador de inquilino como secretos. 

### <a name="add-your-workflow"></a>Agregar el flujo de trabajo

En el repositorio de GitHub, vaya a **Acciones**. 

1. Seleccione **Set up your workflow yourself** (Configure el flujo de trabajo usted mismo).
1. En el archivo de flujo de trabajo, elimine todo lo que aparezca después de la sección `on:`. Por ejemplo, el flujo de trabajo restante puede tener el aspecto de este ejemplo: 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Cambie el nombre del flujo de trabajo. En la pestaña **Marketplace**, busque la acción Implementación de área de trabajo de Synapse y agregue la acción. 

    :::image type="content" source="media/search-action.png" lightbox="media/search-action.png" alt-text="Captura de pantalla que muestra la búsqueda de la tarea Implementación de área de trabajo de Synapse en la pestaña Marketplace.":::

1. Establezca los valores necesarios y la plantilla de área de trabajo:

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You also can use the self-hosted runners.
            runs-on: windows-latest
            steps:
            # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it.
            - uses: actions/checkout@v2
            - uses: azure/synapse-workspace-deployment@release-1.0
            with:
              TargetWorkspaceName: 'target workspace name'
              TemplateFile: './path of the TemplateForWorkspace.json'
              ParametersFile: './path of the TemplateParametersForWorkspace.json'
              OverrideArmParameters: './path of the parameters.yaml'
              environment: 'Azure Public'
              resourceGroup: 'target workspace resource group'
              clientId: ${{secrets.CLIENTID}}
              clientSecret:  ${{secrets.CLIENTSECRET}}
              subscriptionId: 'subscriptionId of the target workspace'
              tenantId: 'tenantId'
              DeleteArtifactsNotInTemplate: 'true'
              managedIdentity: 'False'
    ``` 

1. Ya está listo para confirmar los cambios. Seleccione **Iniciar confirmación**, escriba el título y agregue una descripción (opcional). Luego seleccione **Confirmar nuevo archivo**.

    :::image type="content" source="media/commit-workflow.png" lightbox="media/commit-workflow.png" alt-text="Captura de pantalla que muestra la confirmación del flujo de trabajo en GitHub.":::

   El archivo aparece en la carpeta *.github/workflows* del repositorio.

   > [!NOTE]
   > La identidad administrada solo se admite en máquinas virtuales auto-hospedadas de Azure. Asegúrese de establecer el ejecutor en auto-hospedado. Habilite la identidad administrada asignada por el sistema de la máquina virtual y agréguela a Azure Synapse Studio como administrador de Synapse.

### <a name="review-your-deployment"></a>Revisar la implementación

1. En el repositorio de GitHub, vaya a **Acciones**.
1. Para ver registros detallados de la ejecución del flujo de trabajo,abra el primer resultado:

    :::image type="content" source="media/review-deploy-status.png" lightbox="media/review-deploy-status.png" alt-text="Captura de pantalla que muestra la selección del registro de implementación del área de trabajo en el repositorio Acciones de GitHub.":::

## <a name="create-custom-parameters-in-the-workspace-template"></a>Creación de parámetros personalizados en la plantilla del área de trabajo 

Si usa CI/CD automatizadas y quiere cambiar algunas propiedades durante la implementación pero estas no están parametrizadas de manera predeterminada, puede reemplazar la plantilla de parámetros predeterminada.

Para reemplazar la plantilla de parámetros predeterminada, cree una plantilla de parámetros personalizada de nombre *template-parameters-definition.json* en la carpeta raíz de la rama de colaboración de Git. Debe usar este nombre de archivo exacto. Cuando el área de trabajo de Azure Synapse publica desde la rama de colaboración, lee este archivo y usa su configuración para generar los parámetros. Si el área de trabajo de Azure Synapse no encuentra ese archivo, usa la plantilla de parámetros predeterminada.

### <a name="custom-parameter-syntax"></a>Sintaxis de los parámetros personalizados

Puede usar las siguientes instrucciones para crear un archivo de parámetros personalizado:

* Escriba la ruta de acceso de la propiedad en el tipo de entidad correspondiente.
* El establecimiento de un nombre de propiedad en `*` indica que quiere parametrizar todas las propiedades que incluye (solo hasta el primer nivel, no de forma recursiva). Puede establecer excepciones a esta configuración.
* El establecimiento del valor de una propiedad como una cadena indica que quiere parametrizar la propiedad. Use el formato `<action>:<name>:<stype>`.
   *  `<action>` puede ser uno de estos caracteres:
      * `=` significa que el valor actual se debe conservar como el predeterminado para el parámetro.
      * `-` significa que no se debe conservar el valor predeterminado para el parámetro.
      * `|` es un caso especial para los secretos de Azure Key Vault para cadenas de conexión o claves.
   * `<name>` es el nombre del parámetro. Si está en blanco, toma el nombre de la propiedad. Si el valor empieza por un carácter `-`, el nombre está abreviado. Por ejemplo, `AzureStorage1_properties_typeProperties_connectionString` se abreviará a `AzureStorage1_connectionString`.
   * `<stype>` es el tipo del parámetro. Si `<stype>` está en blanco, el tipo predeterminado es `string`. Valores admitidos: `string`, `securestring`, `int`, `bool`, `object`, `secureobject` y `array`.
* Si se ha especificado una matriz en el archivo, significa que la propiedad coincidente de la plantilla es una matriz. Azure Synapse recorre en iteración todos los objetos de la matriz mediante la definición que se ha especificado. El segundo objeto, una cadena, se convierte en el nombre de la propiedad, que se utiliza como el nombre del parámetro para cada iteración.
* Una definición no puede ser específica de una instancia de recurso. Cualquier definición se aplica a todos los recursos de ese tipo.
* De manera predeterminada, todas las cadenas seguras (como los secretos de Key Vault) y las cadenas seguras (como las cadenas de conexión, las claves y los tokens) están parametrizadas.

### <a name="parameter-template-definition-example"></a>Ejemplo de definición de plantilla de parámetros 

Este es un ejemplo del aspecto de la definición de una plantilla de parámetros:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Esta es una explicación de cómo se ha construido la plantilla anterior, por tipo de recurso.

**`notebooks`**

- Cualquier propiedad de la ruta de acceso `properties/bigDataPool/referenceName` está parametrizada con su valor predeterminado. Puede parametrizar un grupo de Spark asociado para cada archivo del cuaderno. 

**`sqlscripts`**

- En la ruta de acceso `properties/content/currentConnection`, las propiedades `poolName` y `databaseName` están parametrizadas como cadenas sin los valores predeterminados en la plantilla. 

**`pipelines`**

- Cualquier propiedad de la ruta de acceso `activities/typeProperties/waitTimeInSeconds` está parametrizada. Cualquier actividad en una canalización que tiene una propiedad de nivel de código denominada `waitTimeInSeconds` (por ejemplo, la actividad `Wait`) está parametrizada como un número, con un nombre predeterminado. La propiedad no tiene un valor predeterminado en la plantilla de Resource Manager, sino que necesita entrada durante la implementación de Resource Manager.
- La propiedad `headers` (por ejemplo, en una actividad `Web`) está parametrizada con el tipo `object` (objeto). La propiedad `headers` tiene un valor predeterminado que es el mismo que el de la factoría de origen.

**`integrationRuntimes`**

- Todas las propiedades de la ruta de acceso `typeProperties` están parametrizadas con sus valores predeterminados correspondientes. Por ejemplo, hay dos propiedades bajo las propiedades de tipo `IntegrationRuntimes`: `computeProperties` y `ssisProperties`. Ambos tipos de propiedades se crean con sus valores predeterminados y tipos (objeto) respectivos.

**`triggers`**

- En `typeProperties` hay dos propiedades parametrizadas:
  - La propiedad `maxConcurrency` tiene un valor predeterminado y es de tipo `string`. El nombre de parámetro predeterminado de la propiedad `maxConcurrency` es `<entityName>_properties_typeProperties_maxConcurrency`.
  - La propiedad `recurrence` también está parametrizada. Todas las propiedades bajo la propiedad `recurrence` están establecidas para parametrizarse como cadenas, con valores predeterminados y nombres de parámetro. Una excepción es la propiedad `interval`, que está parametrizada como tipo `int`. El sufijo del nombre del parámetro es `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. De forma similar, la propiedad `freq` es una cadena y está parametrizada como una cadena. Sin embargo, la propiedad `freq` está parametrizada sin un valor predeterminado. El nombre está abreviado y con un sufijo, como `<entityName>_freq`.

**`linkedServices`**

- Los servicios vinculados son únicos. Dado que los servicios vinculados y los conjuntos de datos tienen una gran variedad de tipos, puede proporcionar una personalización específica de tipo. En el ejemplo anterior, para todos los servicios vinculados de tipo `AzureDataLakeStore`, se aplica una plantilla específica. Para todos los demás (identificados mediante el carácter `*`), se aplica otra plantilla.
- La propiedad `connectionString` está parametrizada como un valor `securestring`. No tiene un valor predeterminado. El nombre de parámetro está abreviado y tiene un sufijo `connectionString`.
- La propiedad `secretAccessKey` está parametrizada como un valor `AzureKeyVaultSecret` (por ejemplo, en un servicio vinculado de Amazon S3). La propiedad se parametriza automáticamente como un secreto de Azure Key Vault y se captura desde el almacén de claves configurado. También puede parametrizar el propio almacén de claves.

**`datasets`**

- Aunque puede personalizar tipos de conjuntos de datos, no se requiere una configuración explícita de nivel \*. En el ejemplo anterior, todas las propiedades del conjunto de datos de `typeProperties` están parametrizadas.

## <a name="best-practices-for-cicd"></a>Procedimientos recomendados para CI/CD

Si usa la integración de Git con el área de trabajo de Azure Synapse y tiene una canalización de CI/CD que mueve los cambios de desarrollo a pruebas y luego a producción, es aconsejable realizar estos procedimientos recomendados:

-   **Integre solo el área de trabajo de desarrollo con Git**. Si usa la integración de Git, integre solo el área de trabajo de Azure Synapse de *implementación* con Git. Los cambios en las áreas de trabajo de prueba y producción se implementan a través de CI/CD y no se necesita la integración de Git.
-   **Prepare los grupos antes de migrar los artefactos**. Si tiene un script de SQL o un cuaderno asociados a grupos del área de trabajo de desarrollo, use el mismo nombre para los grupos de los distintos entornos. 
-   **Sincronice el control de versiones de la infraestructura como escenarios de código**. Para administrar la infraestructura (redes, máquinas virtuales, equilibradores de carga y topología de conexión) de un modelo descriptivo, use el mismo control de versiones que emplea el equipo de DevOps para el código fuente. 
-   **Revise los procedimientos recomendados de Azure Data Factory**. Si usa Data Factory, vea los [procedimientos recomendados para artefactos de Data Factory](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd).

## <a name="troubleshoot-artifacts-deployment"></a>Solución de problemas de implementación de artefactos 

### <a name="use-the-synapse-workspace-deployment-task"></a>Uso de la tarea de implementación del área de trabajo de Synapse

En Azure Synapse, a diferencia de Data Factory, algunos artefactos no son recursos de Resource Manager. No se puede usar la tarea de implementación de plantilla de ARM para implementar artefactos de Azure Synapse. En su lugar, use la tarea de implementación de área de trabajo de Synapse.
 
### <a name="unexpected-token-error-in-release"></a>Error de token inesperado en la versión

Si el archivo de parámetros tiene valores de parámetro sin escape, la canalización de versión no puede analizar el archivo y genera un error `unexpected token`. Se sugiere reemplazar los parámetros o usar Key Vault para recuperar los valores de parámetro. También puede usar caracteres de escape doble para resolver el problema.
