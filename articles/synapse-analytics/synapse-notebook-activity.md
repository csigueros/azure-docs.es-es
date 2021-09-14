---
title: Transformación de datos mediante la ejecución de un cuaderno de Synapse
description: En este artículo, aprenderá a crear y desarrollar una actividad de cuaderno de Synapse y una canalización de Synapse.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: c8c2246822cdf9faa06833020cd1951bde9e86a3
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449304"
---
# <a name="transform-data-by-running-a-synapse-notebook"></a>Transformación de datos mediante la ejecución de un cuaderno de Synapse

[!INCLUDE[appliesto-adf-xxx-md](../data-factory/includes/appliesto-xxx-asa-md.md)]

La actividad de cuaderno de Azure Synapse de una [canalización de Synapse](../data-factory/concepts-pipelines-activities.md) ejecuta un cuaderno de Synapse en el área de trabajo de Azure Synapse. Este artículo se basa en el artículo sobre [actividades de transformación de datos](../data-factory/transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas. 

## <a name="create-a-synapse-notebook-activity"></a>Creación de una actividad de cuaderno de Synapse

Puede crear una actividad de cuaderno de Synapse directamente desde el lienzo de la canalización de Synapse o desde el editor de cuadernos. La actividad de cuaderno de Synapse se ejecuta en el grupo de Spark que se elige en el cuaderno de Synapse. 

### <a name="add-a-synapse-notebook-activity-from-pipeline-canvas"></a>Adición de una actividad de cuaderno de Synapse desde el lienzo de la canalización

Arrastre y coloque el **cuaderno de Synapse** hasta **Activities** (Actividades) en el lienzo de la canalización de Synapse. Seleccione el cuadro de actividad del cuaderno de Synapse y configure el contenido del cuaderno para la actividad actual en la **configuración**. Puede seleccionar un cuaderno existente del área de trabajo actual o agregar uno nuevo. 

![Captura de pantalla que muestra la creación de una actividad de cuaderno](./media/synapse-notebook-activity/create-synapse-notebook-activity.png)

> [!NOTE]
> La ejecución de cuadernos de Spark en paralelo en canalizaciones de Azure Synapse se pone en cola y se ejecuta de forma FIFO, el orden de los trabajos en la cola lo determina la secuencia de tiempo, el tiempo de expiración de un trabajo en la cola es de 3 días. Tenga en cuenta que la cola de cuadernos solo funciona en la canalización de Synapse.


### <a name="add-a-notebook-to-synapse-pipeline"></a>Adición de un cuaderno a la canalización de Synapse

Seleccione el botón **Agregar a la canalización** en la esquina superior derecha para agregar un cuaderno a una canalización existente o crear una canalización.

![Captura de pantalla que muestra la adición de un cuaderno a la canalización](./media/synapse-notebook-activity/add-to-pipeline.png)

## <a name="passing-parameters"></a>Pasar parámetros

### <a name="designate-a-parameters-cell"></a>Designación de una celda de parámetros

# <a name="classic-notebook"></a>[Cuaderno clásico](#tab/classical)

Para parametrizar el cuaderno, seleccione los puntos suspensivos (…) para acceder al menú de otras acciones de celda del extremo derecho. A continuación, seleccione **Toggle parameter cell** (Alternar celda de parámetros) para designar la celda como la celda de parámetros.

[![Captura de pantalla que muestra la alternancia de parámetros](./media/synapse-notebook-activity/toggle-parameter-cell.png)](./media/synapse-notebook-activity/toggle-parameter-cell.png#lightbox)

# <a name="preview-notebook"></a>[Cuaderno de la versión preliminar](#tab/preview)

Para parametrizar el cuaderno, seleccione los puntos suspensivos (...) para obtener acceso a **más comandos** en la barra de herramientas de la celda. A continuación, seleccione **Toggle parameter cell** (Alternar celda de parámetros) para designar la celda como la celda de parámetros.

[![Captura de pantalla que muestra la alternancia de parámetros de un cuaderno de Azure](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png)](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png#lightbox)

---

Azure Data Factory busca la celda de parámetros y utiliza los valores como valores predeterminados para los parámetros que se pasan en tiempo de ejecución. El motor de ejecución agregará una nueva celda debajo de la celda de parámetros con los parámetros de entrada que van a sobrescribir los valores predeterminados. 


### <a name="assign-parameters-values-from-a-pipeline"></a>Asignación de valores de parámetros de una canalización

Una vez que haya creado un cuaderno con parámetros, podrá ejecutarlo desde una canalización con la actividad de cuaderno de Synapse. Después de agregar la actividad al lienzo de la canalización, podrá establecer los valores de los parámetros en la sección **Base parameters** (Parámetros base) de la pestaña **Settings** (Configuración). 

[![Captura de pantalla en la que se muestra la asignación de un parámetro](./media/synapse-notebook-activity/assign-parameter.png)](./media/synapse-notebook-activity/assign-parameter.png#lightbox)

Al asignar valores de parámetros, puede usar el [lenguaje de expresiones de canalización](../data-factory/control-flow-expression-language-functions.md) o las [variables del sistema](../data-factory/control-flow-system-variables.md).


## <a name="read-synapse-notebook-cell-output-value"></a>Lectura del valor de salida de la celda del cuaderno de Synapse

Puede leer el valor de salida de la celda del cuaderno en las actividades siguientes siguiendo estos pasos:
1. Llame a la API [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) en la actividad de cuaderno de Synapse para devolver el valor que desea mostrar en la salida de la actividad, por ejemplo:  

    ```python
    mssparkutils.notebook.exit("hello world") 
    ```
    
    Al guardar el contenido del cuaderno y volver a desencadenar la canalización, la salida de la actividad de cuaderno contendrá la propiedad exitValue, que se puede consumir en las actividades siguientes en el paso 2. 

2.  Lea la propiedad exitValue de la salida de la actividad de cuaderno. Esta es una expresión de ejemplo que se usa para comprobar si el valor de exitValue capturado de la salida de la actividad de cuaderno es igual a "hello world": 

    [![Captura de pantalla que muestra la lectura de exitValue](./media/synapse-notebook-activity/synapse-read-exit-value.png)](./media/synapse-notebook-activity/synapse-read-exit-value.png#lightbox)


## <a name="run-another-synapse-notebook"></a>Ejecución de otro cuaderno de Synapse 

Puede hacer referencia a otros cuadernos de una actividad de cuaderno de Synapse mediante una llamada a [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) o las [utilidades de cuaderno mssparkutils](./spark/microsoft-spark-utilities.md#notebook-utilities). Ambos admiten llamadas de función de anidamiento. Las diferencias principales entre estos dos métodos que debe tener en cuenta en función de su escenario son:

- [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) copia todas las celdas del cuaderno al que se hace referencia en la celda %run y comparte el contexto de variables. Cuando notebook1 hace referencia a notebook2 mediante `%run notebook2` y notebook2 llama a una función [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook), se detendrá la ejecución de celdas en notebook1. Se recomienda usar %run magic cuando quiera "incluir" un archivo de cuaderno.
- [mssparkutils notebook utilities](./spark/microsoft-spark-utilities.md#notebook-utilities) llama al cuaderno al que se hace referencia como un método o una función. No se comparte el contexto de variables. Cuando notebook1 hace referencia a notebook2 mediante `mssparkutils.notebook.run("notebook2")` y notebook2 llama a una función [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook), la ejecución de celdas en notebook1 continuará. Se recomienda usar las utilidades de cuaderno mssparkutils cuando quiera "importar" un cuaderno.

>[!Note]
> La ejecución de otro cuaderno de Synapse desde una canalización de Synapse solo funcionará para un cuaderno con la versión preliminar habilitada.


## <a name="see-notebook-activity-run-history"></a>Visualización del historial de ejecución de la actividad de cuaderno

Vaya a **Pipeline runs** (Ejecuciones de canalización) en la pestaña **Monitor** (Supervisión); verá la canalización que ha desencadenado. Abra la canalización que contiene la actividad de cuaderno para ver el historial de ejecución. 

Para ver la instantánea de ejecución del cuaderno más reciente, incluida la entrada y la salida de las celdas, seleccione el botón **Open notebook** (Abrir cuaderno). 
![Visualización del historial de la actividad de cuaderno](./media/synapse-notebook-activity/input-output-open-notebook.png)


Para ver la entrada o la salida de la actividad de cuaderno, seleccione el botón de **entrada** o **salida**. Si se produce un error de usuario en la canalización, seleccione la **salida** para comprobar el campo **result** (resultado) y ver el seguimiento de errores de usuario detallado.

![Captura de pantalla que muestra la visualización de errores de usuario de salida](./media/synapse-notebook-activity/notebook-output-user-error.png)


## <a name="synapse-notebook-activity-definition"></a>Definición de la actividad de cuaderno de Synapse

Este es un ejemplo de definición JSON de una actividad de cuaderno de Synapse:

```json
{
    "name": "parameter_test",
    "type": "SynapseNotebook",
    "dependsOn": [],
    "policy": {
        "timeout": "7.00:00:00",
        "retry": 0,
        "retryIntervalInSeconds": 30,
        "secureOutput": false,
        "secureInput": false
    },
    "userProperties": [],
    "typeProperties": {
        "notebook": {
            "referenceName": "parameter_test",
            "type": "NotebookReference"
        },
        "parameters": {
            "input": {
                "value": {
                    "value": "@pipeline().parameters.input",
                    "type": "Expression"
                }
            }
        }
    }
}

```


## <a name="synapse-notebook-activity-output"></a>Salida de la actividad de cuaderno de Synapse

Este es un ejemplo en formato JSON de la salida de una actividad de cuaderno de Synapse:

```json

{
    "status": {
        "Status": 1,
        "Output": {
            "status": <livySessionInfo>
            },
            "result": {
                "runId": "<GUID>",
                "runStatus": "Succeed",
                "message": "Notebook execution is in Succeeded state",
                "lastCheckedOn": "2021-03-23T00:40:10.6033333Z",
                "errors": {
                    "ename": "",
                    "evalue": ""
                },
                "sessionId": 4,
                "sparkpool": "sparkpool",
                "snapshotUrl": "https://myworkspace.dev.azuresynapse.net/notebooksnapshot/{guid}",
                "exitCode": "abc" // return value from user notebook via mssparkutils.notebook.exit("abc")
            }
        },
        "Error": null,
        "ExecutionDetails": {}
    },

    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US 2)",
    "executionDuration": 234,
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    },
    "billingReference": {
        "activityType": "ExternalActivity",
        "billableDuration": [
            {
                "meterType": "AzureIR",
                "duration": 0.06666666666666667,
                "unit": "Hours"
            }
        ]
    }
}

```
