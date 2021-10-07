---
title: Automatización de la integración continua
description: Aprenda a automatizar la integración continua en Azure Data Factory con lanzamientos de canalizaciones de Azure Pipelines.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c32b2e986e50fd2679b65b449eccdf5c7629a9a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219360"
---
# <a name="automate-continuous-integration-using-azure-pipelines-releases"></a>Automatización de la integración continua mediante versiones de Azure Pipelines

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A continuación se ofrece una guía para configurar una versión de Azure Pipelines, que automatiza la implementación de una factoría de datos en varios entornos.

## <a name="requirements"></a>Requisitos

-   Una suscripción a Azure vinculada a Visual Studio Team Foundation Server o Azure Repos que use el [punto de conexión de servicio de Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Una factoría de datos configurada con la integración de GIT de Azure Repos.

-   Un [almacén de claves de Azure](https://azure.microsoft.com/services/key-vault/) que contenga los secretos para cada entorno.

## <a name="set-up-an-azure-pipelines-release"></a>Configuración de una versión de Azure Pipelines

1.  En [Azure DevOps](https://dev.azure.com/), abra el proyecto configurado con la factoría de datos.

1.  En el lado izquierdo de la página, seleccione **Canalizaciones** y después **Versiones**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image6.png" alt-text="Selección de Canalizaciones, Versiones":::

1.  Seleccione **Nueva canalización** o, si tiene canalizaciones existentes, seleccione **Nueva** y, luego **Nueva canalización de versión**.

1.  Seleccione la plantilla **Fase vacía**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image13.png" alt-text="Selección de Trabajo vacío":::

1.  En el cuadro **Nombre de la fase**, escriba el nombre del entorno.

1.  Seleccione **Agregar artefacto** y después el mismo repositorio de Git configurado con la factoría de datos de desarrollo. Seleccione la [rama de publicación](source-control.md#configure-publishing-settings) del repositorio en **Rama predeterminada**. De forma predeterminada, esta rama de publicación es `adf_publish`. En **Versión predeterminada**, seleccione **Más reciente de la rama predeterminada**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image7.png" alt-text="Agregar un artefacto":::

1.  Añada una tarea de implementación de Azure Resource Manager:

    a.  En la vista de fase, seleccione **Ver tareas de la fase**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image14.png" alt-text="Vista de fase":::

    b.  Cree una nueva tarea. Busque **Implementación de una plantilla de Resource Manager** y, después, seleccione **Agregar**.

    c.  En la tarea de implementación, seleccione la suscripción, el grupo de recursos y la ubicación de la factoría de datos de destino. Proporcione las credenciales si es necesario.

    d.  En la lista **Acción**, seleccione **Create or update resource group** (Crear o actualizar grupo de recursos).

    e.  Seleccione el botón de puntos suspensivos ( **...** ) situado junto al cuadro **Plantilla**. Busque la plantilla de Azure Resource Manager que se ha generado en la rama de publicación del repositorio de Git configurado. Busque el archivo `ARMTemplateForFactory.json` en la carpeta &lt;FactoryName&gt; de la rama adf_publish.

    f.  Seleccione **…** junto al cuadro **Parámetros de plantilla** para elegir el archivo de parámetros. Busque el archivo `ARMTemplateParametersForFactory.json` en la carpeta &gt;FactoryName&lt; de la rama adf_publish.

    g.  Seleccione **…** junto al cuadro **Reemplazar parámetros de plantilla** y escriba los valores de los parámetros deseados de la factoría de datos de destino. En el caso de las credenciales que proceden de Azure Key Vault, escriba el nombre del secreto entre comillas dobles. Por ejemplo, si el nombre del secreto es cred1, escriba **"$(cred1)"** para este valor.

    h. Seleccione **Incremental** para el **Modo de implementación**.

    > [!WARNING]
    > En el modo de implementación completa, se **eliminarán** aquellos recursos que existan en el grupo de recursos, pero no estén especificados en la nueva plantilla de Resource Manager. Para más información, consulte [Modos de implementación de Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md).

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image9.png" alt-text="Implementación de producción de Data Factory":::

1.  Guarde la canalización de versión.

1. Para desencadenar una versión, seleccione **Crear versión**. Para automatizar la creación de versiones, consulte [Desencadenadores de versión de Azure DevOps](/azure/devops/pipelines/release/triggers).

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image10.png" alt-text="Selección de Crear versión":::

> [!IMPORTANT]
> En escenarios de CI/CD, el tipo de entorno de ejecución de integración (IR) en otros entornos debe ser el mismo. Por ejemplo, si tiene un IR autohospedado en el entorno de desarrollo, el mismo IR también debe ser de tipo autohospedado en otros entornos, como los de prueba y producción. Del mismo modo, si va a compartir los entornos de ejecución de integración entre varias fases, tendrá que configurarlos como autohospedados vinculados en todos los entornos: desarrollo, prueba y producción.

## <a name="get-secrets-from-azure-key-vault"></a>Obtención de secretos de Azure Key Vault

Si tiene secretos para pasar en una plantilla de Azure Resource Manager, se recomienda usar Azure Key Vault con la versión de Azure Pipelines.

Hay dos formas de administrar los secretos:

- Agregue los secretos al archivo de parámetros. Para más información, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](../azure-resource-manager/templates/key-vault-parameter.md).

    Cree una copia del archivo de parámetros que se ha cargado en la rama de publicación. Establezca los valores de los parámetros que quiera obtener del almacén de claves con este formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Al utilizar este método, el secreto se extrae automáticamente del almacén de claves.

    El archivo de parámetros también debe estar en la rama de publicación.

- Agregue una [tarea de Azure Key Vault](/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes de la tarea de implementación de Azure Resource Manager que se ha descrito en la sección anterior:

    1.  En la pestaña **Tareas**, cree una tarea. Busque **Azure Key Vault** y agréguelo.

    1.  En la tarea de Key Vault, seleccione la suscripción en la que haya creado el almacén de claves. Proporcione credenciales si es necesario y, después, seleccione el almacén de claves.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image8.png" alt-text="Adición de una tarea de Key Vault":::

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Concesión de permisos al agente de Azure Pipelines

Es posible que se produzca un error de acceso denegado en la tarea Azure Key Vault si no se han establecido los permisos correctos. Descargue los registros de la versión y busque el archivo .ps1 que contiene el comando para conceder permisos al agente de Azure Pipelines. Puede ejecutar el comando directamente. O bien, puede copiar el identificador de entidad de seguridad del archivo y añadir la directiva de acceso manualmente en Azure Portal. `Get` y `List` son los permisos mínimos necesarios.

## <a name="updating-active-triggers"></a>Actualización de desencadenadores activos

Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-Az-ps).

>[!WARNING]
>Si no usa las versiones más recientes del módulo de PowerShell y Data Factory, puede que se produzcan errores de deserialización al ejecutarse los comandos. 
>

Puede producirse un error en la implementación si intenta actualizar desencadenadores activos. Para actualizar desencadenadores activos, debe detenerlos manualmente e iniciarlos después de la implementación. Puede hacerlo mediante una tarea de Azure PowerShell:

1.  En la pestaña **Tareas** de la versión, agregue una tarea **Azure PowerShell**. Elija la versión de tarea más reciente de Azure PowerShell. 

1.  Seleccione la suscripción en la que se encuentra la factoría.

1.  Seleccione **Ruta de acceso del archivo de script** como tipo de script. Esto requiere guardar el script de PowerShell en el repositorio. El siguiente script de PowerShell sirve para detener desencadenadores:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Puede completar pasos similares (con la función `Start-AzDataFactoryV2Trigger`) para reiniciar los desencadenadores después de la implementación.

El equipo de factoría de datos ha proporcionado un [script de ejemplo anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md). 

## <a name="next-steps"></a>Pasos siguientes

- [Información general de integración y entrega continuas](continuous-integration-delivery.md)
- [Promoción manual de una plantilla de Resource Manager para cada entorno](continuous-integration-delivery-manual-promotion.md)
- [Uso de parámetros personalizados con una plantilla de Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Plantillas vinculadas de Resource Manager](continuous-integration-delivery-linked-templates.md)
- [Uso de un entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md)
- [Script de ejemplo anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)
