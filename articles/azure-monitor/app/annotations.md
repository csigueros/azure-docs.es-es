---
title: Anotaciones de la versión de Application Insights | Microsoft Docs
description: Aprenda a crear anotaciones para realizar un seguimiento de la implementación u otros eventos importantes con Application Insights.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: cfd1c9b28a79d68983e49ef5d6dfd70dd357ab47
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071036"
---
# <a name="release-annotations-for-application-insights"></a>Anotaciones de versión para Application Insights

Las anotaciones muestran dónde ha implementado una nueva compilación u otros eventos importantes. Las anotaciones permiten ver fácilmente si los cambios tuvieron algún efecto en el rendimiento de la aplicación. Se pueden crear automáticamente con el sistema de compilación de [Azure Pipelines](/azure/devops/pipelines/tasks/). También puede crear anotaciones para marcar los eventos que prefiera si los crea desde PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Anotaciones de la versión con una compilación de Azure Pipelines

Las anotaciones de la versión son una característica del servicio Azure Pipelines basado en la nube de Azure DevOps.

Si se cumplen todos los criterios siguientes, la tarea de implementación crea automáticamente la anotación de versión:

- El recurso en el que va a realizar la implementación está vinculado a Application Insights (a través de la opción de configuración `APPINSIGHTS_INSTRUMENTATIONKEY` de la aplicación).
- El recurso de Application Insights está en la misma suscripción que el recurso en el que se va a realizar la implementación.
- Está usando una de las siguientes tareas de canalización de Azure DevOps:

    | Código de tarea                 | Nombre de la tarea                     | Versiones     |
    |---------------------------|-------------------------------|--------------|
    | AzureAppServiceSettings   | Configuración de Azure App Service    | Any          |
    | AzureRmWebAppDeployment   | Implementación de Azure App Service      | V3 y posteriores |
    | AzureFunctionApp          | Azure Functions               | Any          |
    | AzureFunctionAppContainer | Azure Functions para contenedor | Any          |
    | AzureWebAppContainer      | Azure Web App for Containers  | Any          |
    | AzureWebApp               | Aplicación web de Azure                 | Any          |

> [!NOTE]
> Si sigue usando la tarea de implementación de anotación de Application Insights, debe eliminarla.

### <a name="configure-release-annotations"></a>Configurar anotaciones de versión

Si no puede usar una de las tareas de implementación de la sección anterior, deberá agregar una tarea de script en línea en la canalización de implementación.

1. Vaya a una canalización nueva o existente y seleccione una tarea.
    :::image type="content" source="./media/annotations/task.png" alt-text="Captura de pantalla de la tarea en las fases seleccionadas." lightbox="./media/annotations/task.png":::
1. Agregue una nueva tarea y seleccione **CLI de Azure**.
    :::image type="content" source="./media/annotations/add-azure-cli.png" alt-text="Captura de pantalla de la adición de una nueva tarea y selección de la CLI de Azure." lightbox="./media/annotations/add-azure-cli.png":::
1. Especifique la suscripción de Azure correspondiente.  Cambie **Tipo de script** a *PowerShell* y **Ubicación del script** a *En línea*.
1. Agregue el [script de PowerShell del paso 2 de la sección siguiente](#create-release-annotations-with-azure-cli) a **Script en línea**.
1. Agregue los argumentos siguientes, reemplazando los marcadores de posición entre corchetes angulares por sus valores de **Argumentos de script**. Los valores de -releaseProperties son opcionales.

    ```powershell
        -aiResourceId "<aiResourceId>" `
        -releaseName "<releaseName>" `
        -releaseProperties @{"ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
    ```

    :::image type="content" source="./media/annotations/inline-script.png" alt-text="Captura de pantalla de la configuración de la tarea de la CLI de Azure con Tipo de script, Ubicación del script, Script en línea y Argumentos de script resaltados." lightbox="./media/annotations/inline-script.png":::

1. Haga clic en Save (Guardar).

## <a name="create-release-annotations-with-azure-cli"></a>Creación de anotaciones de versión con la CLI de Azure

Puede usar el script de PowerShell CreateReleaseAnnotation para crear anotaciones desde cualquier proceso que desee, sin usar Azure DevOps.

1. Inicie sesión en la [CLI de Azure](/cli/azure/authenticate-azure-cli).

2. Haga una copia local del script siguiente y llámelo CreateReleaseAnnotation.ps1.

    ```powershell
    param(
        [parameter(Mandatory = $true)][string]$aiResourceId,
        [parameter(Mandatory = $true)][string]$releaseName,
        [parameter(Mandatory = $false)]$releaseProperties = @()
    )
    
    $annotation = @{
        Id = [GUID]::NewGuid();
        AnnotationName = $releaseName;
        EventTime = (Get-Date).ToUniversalTime().GetDateTimeFormats("s")[0];
        Category = "Deployment";
        Properties = ConvertTo-Json $releaseProperties -Compress
    }
    
    $body = (ConvertTo-Json $annotation -Compress) -replace '(\\+)"', '$1$1"' -replace "`"", "`"`""

    az rest --method put --uri "$($aiResourceId)/Annotations?api-version=2015-05-01" --body "$($body) "
    ```

3. Llame al script de PowerShell con el código siguiente, reemplazando los marcadores de posición entre corchetes angulares por sus valores. Los valores de -releaseProperties son opcionales.

    ```powershell
         .\CreateReleaseAnnotation.ps1 `
          -aiResourceId "<aiResourceId>" `
          -releaseName "<releaseName>" `
          -releaseProperties @{"ReleaseDescription"="<a description>";
              "TriggerBy"="<Your name>" }
    ```

|Argumento | Definición | Nota|
|--------------|-----------------------|--------------------|
|aiResourceId | Identificador del recurso de Application Insights de destino. | Ejemplo:<br> /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRGName/providers/microsoft.insights/components/MyResourceName|
|releaseName | Nombre que se dará a la anotación de versión creada. | | 
|releaseProperties | Se usa para adjuntar metadatos personalizados a la anotación. | Opcionales|

## <a name="view-annotations"></a>Ver anotaciones

> [!NOTE]
> Las anotaciones de versión no están disponibles actualmente en el panel de métricas de Application Insights

Ahora, cuando utilice la plantilla de versión para implementar una nueva versión, se enviará una anotación a Application Insights. Las anotaciones se pueden ver en las siguientes ubicaciones:

- Rendimiento

    :::image type="content" source="./media/annotations/performance.png" alt-text="Captura de pantalla de la pestaña Rendimiento con una anotación de versión seleccionada (flecha azul) para mostrar la pestaña Propiedades de la versión." lightbox="./media/annotations/performance.png":::

- Errores

    :::image type="content" source="./media/annotations/failures.png" alt-text="Captura de pantalla de la pestaña Errores con una anotación de versión seleccionada (flecha azul) para mostrar la pestaña Propiedades de la versión." lightbox="./media/annotations/failures.png":::
- Uso

    :::image type="content" source="./media/annotations/usage-pane.png" alt-text="Captura de pantalla del gráfico de barras en la pestaña Usuarios con anotaciones de versión seleccionadas. Las anotaciones de versión aparecen como flechas azules sobre el gráfico que indican el momento en el tiempo en que se produjo una versión." lightbox="./media/annotations/usage-pane.png":::

- Workbooks

    En cualquier consulta de libro basada en registros en la que la visualización muestre el tiempo a lo largo del eje x.
    
    :::image type="content" source="./media/annotations/workbooks-annotations.png" alt-text="Captura de pantalla del panel de libros con una consulta basada en registro de serie temporal con las anotaciones mostradas." lightbox="./media/annotations/workbooks-annotations.png":::
    
    Para habilitar las anotaciones en el libro, vaya a **Configuración avanzada** y seleccione **Mostrar anotaciones**.
    
    :::image type="content" source="./media/annotations/workbook-show-annotations.png" alt-text="Captura de pantalla del menú Configuración avanzada con la casilla Mostrar anotaciones resaltada.":::

Seleccione un marcador de anotación para abrir los detalles de la versión, incluidos el solicitante, la rama de control de código fuente, la canalización de la versión y el entorno.

## <a name="next-steps"></a>Pasos siguientes

* [Crear elementos de trabajo](./diagnostic-search.md#create-work-item)
* [Automation con PowerShell](./powershell.md)
