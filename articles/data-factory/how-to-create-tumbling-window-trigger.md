---
title: Creación de un desencadenador periódico
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a crear un desencadenador en Azure Data Factory o Azure Synapse Analytics que ejecute una canalización en una ventana de saltos de tamaño constante.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 7b0af3fbd090eec36c69f784639ff401f5d80c46
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831434"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Creación de un desencadenador que ejecuta una canalización en una ventana de saltos de tamaño constante

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se explica cómo crear, iniciar y supervisar un desencadenador de ventana de saltos de tamaño constante. Para obtener información general sobre los desencadenadores y los tipos compatibles, vea [Ejecución y desencadenadores de canalización](concepts-pipeline-execution-triggers.md).

Los desencadenadores de ventana de saltos de tamaño constante son un tipo de desencadenador que se activa en un intervalo de tiempo periódico a partir de una hora de inicio especificada, mientras conserva el estado. Las ventanas de saltos de tamaño constante son una serie de intervalos de tiempo de tamaño fijo, contiguos y que no se superponen. Un desencadenador de ventana de saltos de tamaño constante tiene una relación uno a uno con una canalización y solo puede hacer referencia a una única canalización. El desencadenador de ventana de saltos de tamaño constante es una alternativa de mayor peso para el desencadenador de programación, que ofrece un conjunto de características para escenarios complejos ([dependencia en otros desencadenadores de ventana de saltos de tamaño constante](#tumbling-window-trigger-dependency), [nueva ejecución de un trabajo con errores](tumbling-window-trigger-dependency.md#monitor-dependencies) y [definición del reintento de canalizaciones por el usuario](#user-assigned-retries-of-pipelines)). Para comprender mejor la diferencia entre el desencadenador de programación y el desencadenador de ventana de saltos de tamaño constante, visite [aquí](concepts-pipeline-execution-triggers.md#trigger-type-comparison).

## <a name="ui-experience"></a>Experiencia de IU

1. Para crear un desencadenador de ventana de saltos de tamaño constante en la interfaz de usuario, seleccione la pestaña de los **desencadenadores** y, a continuación, seleccione **Nuevo**. 
1. Después de abrir el panel de configuración del desencadenador,seleccione la **ventana de saltos de tamaño constante** y, a continuación, defina las propiedades del desencadenador de la misma. 
1. Cuando finalice, seleccione **Guardar**.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png" alt-text="Creación de un desencadenador de ventana de saltos de tamaño constante en Azure Portal":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger-synapse.png" alt-text="Creación de un desencadenador de ventana de saltos de tamaño constante en Azure Portal":::

---

## <a name="tumbling-window-trigger-type-properties"></a>Propiedades del tipo de desencadenador de ventana de saltos de tamaño constante

Una ventana de saltos de tamaño constante tiene las siguientes propiedades del tipo de desencadenador:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

En la tabla siguiente se muestra una descripción general de los elementos JSON más importantes relacionados con la periodicidad y la programación de un desencadenador de ventana de saltos de tamaño constante:

| Elemento JSON | Descripción | Tipo | Valores permitidos | Obligatorio |
|:--- |:--- |:--- |:--- |:--- |
| **type** | El tipo del desencadenador. El tipo es el valor fijo "TumblingWindowTrigger". | String | "TumblingWindowTrigger" | Sí |
| **runtimeState** | Estado actual del tiempo de ejecución del desencadenador.<br/>**Nota**: Este elemento es \<readOnly>. | String | "Started," "Stopped," "Disabled" | Sí |
| **frequency** | Una cadena que representa la unidad de frecuencia (minutos u horas) con que se repite el desencadenador. Si los valores de fecha **startTime** son más granulares que el valor **frequency**, las fechas **startTime** se tienen en cuenta para calcular los límites de ventana. Por ejemplo, si el valor **frequency** es cada hora y el valor **startTime** es 2017-09-01T10:10:10Z, la primera ventana es (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | String | "minute", "hour"  | Sí |
| **interval** | Un entero positivo que indica el intervalo para el valor **frequency**, que determina la frecuencia con la que se ejecuta el desencadenador. Por ejemplo, si **interval** es 3 y **frequency** es "hour", el desencadenador se repite cada tres horas. <br/>**Nota**: El intervalo de ventana mínimo es de 5 minutos. | Entero | Un número entero positivo. | Sí |
| **startTime**| La primera repetición, que puede ser en el pasado. El primer intervalo de desencadenador es (**startTime**, **startTime** + **interval**). | DateTime | Un valor DateTime. | Sí |
| **endTime**| La última repetición, que puede ser en el pasado. | DateTime | Un valor DateTime. | Sí |
| **delay** | La cantidad de tiempo para retrasar el inicio del procesamiento de datos de la ventana. La ejecución de la canalización se inicia después del tiempo de ejecución esperado más el tiempo de retraso establecido en **delay**. **delay** define el tiempo de espera del desencadenador antes de desencadenar una nueva ejecución. El valor de **delay** no altera el valor de **startTime** de la ventana. Por ejemplo, un valor **delay** de 00:10:00 implica un retraso de diez minutos. | TimeSpan<br/>(hh:mm:ss)  | Un valor de intervalo de tiempo donde el valor predeterminado es 00:00:00. | No |
| **maxConcurrency** | Número de ejecuciones simultáneas del desencadenador que se activan para las ventanas que están listas. Por ejemplo, reponer las ejecuciones cada hora para el día de ayer genera veinticuatro ventanas. Si **maxConcurrency** = 10, los eventos del desencadenador se activan solo para las diez primeras ventanas (00:00-01:00 - 09:00-10:00). Una vez completadas las diez primeras ejecuciones de canalización desencadenadas, se activan las ejecuciones del desencadenador para las diez siguientes (10:00-11:00 - 19:00-20:00). Siguiendo con el ejemplo de **maxConcurrency** = 10, si hay diez ventanas listas, habrá también diez ejecuciones de canalización en total. Si solo hay una ventana lista, solo se producirá una ejecución de canalización. | Entero | Un número entero comprendido entre uno y cincuenta. | Sí |
| **retryPolicy: Count** | El número de reintentos antes de que la ejecución de la canalización se marque como "error".  | Entero | Un entero, donde el valor predeterminado es 0 (ningún reintento). | No |
| **retryPolicy: intervalInSeconds** | El retraso entre intentos de reintentos, especificado en segundos. | Entero | El número de segundos, donde el valor predeterminado es 30. | No |
| **dependsOn: type** | Tipo de TumblingWindowTriggerReference. Obligatorio si se establece una dependencia. | String |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | No |
| **dependsOn: size** | Tamaño de la ventana de saltos de tamaño constante de la dependencia. | TimeSpan<br/>(hh:mm:ss)  | Un valor de intervalo de tiempo positivo en el que el valor predeterminado es el tamaño de la ventana del desencadenador secundario.  | No |
| **dependsOn: offset** | Desplazamiento del desencadenador de la dependencia. | TimeSpan<br/>(hh:mm:ss) |  Un valor de intervalo de tiempo que debe ser negativo en una autodependencia. Si no se especifica ningún valor, la ventana es igual al desencadenador. | Autodependencia: Sí<br/>Otros: No  |

> [!NOTE]
> Después de publicar un desencadenador de ventana de saltos de tamaño constante, no es posible editar el **intervalo** y la **frecuencia**.

### <a name="windowstart-and-windowend-system-variables"></a>Variables del sistema WindowStart y WindowEnd

Puede usar las variables del sistema **WindowStart** y **WindowEnd** del desencadenador de ventana de saltos de tamaño constante en la definición de **pipeline** (es decir, para la parte de una consulta). Pase las variables del sistema como parámetros a la canalización en la definición de **trigger**. En el ejemplo siguiente se muestra cómo pasar estas variables como parámetros:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Para usar los valores de las variables del sistema **WindowStart** y **WindowEnd** en la definición de la canalización, use los parámetros "MyWindowStart" y "MyWindowEnd", según corresponda.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Orden de ejecución de ventanas en un escenario de reposición

Si valor de startTime del desencadenador se encuentra en el pasado, en función de esta fórmula, M=(CurrentTime- TriggerStartTime)/TumblingWindowSize, el desencadenador generará {M} ejecuciones de reposición (pasadas) en paralelo, respetando la simultaneidad del desencadenador, antes de llevar a cabo las ejecuciones futuras. El orden de ejecución de las ventanas es determinista, de los intervalos más antiguos a los más recientes. Actualmente, no se puede modificar este comportamiento.

### <a name="existing-triggerresource-elements"></a>Elementos TriggerResource existentes

Los siguientes puntos se aplican a la actualización de elementos **TriggerResource** existentes:

* El valor para el elemento **frecuencia** (o el tamaño de la ventana) del desencadenador junto con el elemento **intervalo** no se puede cambiar una vez que se crea el desencadenador. Esto es necesario para el correcto funcionamiento de las nuevas ejecuciones de triggerRun y las evaluaciones de dependencias.
* Si el valor del elemento **endTime** del desencadenador cambia (se agrega o se actualiza), *no* se restablece el estado de las ventanas que ya se han procesado. El desencadenador respeta el nuevo valor de **endTime**. Si el nuevo valor de **endTime** es anterior a las ventanas que ya se han ejecutado, el desencadenador se detiene. En caso contrario, el desencadenador se detiene cuando se encuentra el nuevo valor de **endTime**.

### <a name="user-assigned-retries-of-pipelines"></a>Reintentos de canalizaciones asignados por el usuario

En caso de errores de canalización, el desencadenador de ventana de saltos de tamaño constante puede volver a intentar la ejecución de la canalización referenciada automáticamente, utilizando los mismos parámetros de entrada, sin la intervención del usuario. Esto se puede especificar mediante la propiedad "retryPolicy" en la definición del desencadenador.

### <a name="tumbling-window-trigger-dependency"></a>Dependencia de un desencadenador de ventana de saltos de tamaño constante

Si quiere asegurarse de que un desencadenador de ventana de saltos de tamaño constante se ejecute solo después de la correcta ejecución de otro desencadenador de ventana de saltos de tamaño constante en la factoría de datos, [cree una dependencia de desencadenador de ventana de saltos de tamaño constante](tumbling-window-trigger-dependency.md).

### <a name="cancel-tumbling-window-run"></a>Cancelación de la ejecución de la ventana de saltos de tamaño constante

Puede cancelar las ejecuciones de un desencadenador de ventana de saltos de tamaño constante si la ventana específica está en estado _Waiting_ (En espera), _Waiting on Dependency_ (En espera de dependencia) o _Running_ (En ejecución).

* Si la ventana se encuentra en estado **Running** (En ejecución), cancele la _ejecución de canalización_ asociada y la ejecución del desencadenador se marcará después como _Canceled_ (Cancelada).
* Si la ventana está en estado **Waiting** (En espera) o **Waiting on Dependency** (En espera de dependencia), puede cancelar la ventana desde Supervisión:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png" alt-text="Cancelación de un desencadenador de ventana de saltos de tamaño constante desde la página Supervisión":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger-synapse.png" alt-text="Cancelación de un desencadenador de ventana de saltos de tamaño constante desde la página Supervisión":::

---

También puede volver a ejecutar una ventana cancelada. En esta nueva ejecución se tomarán las definiciones publicadas _más recientes_ del desencadenador, y las dependencias de la ventana especificada se _volverán a evaluar_ después.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png" alt-text="Nueva ejecución de un desencadenador de ventana de saltos de tamaño constante para las ejecuciones canceladas anteriormente":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger-synapse.png" alt-text="Nueva ejecución de un desencadenador de ventana de saltos de tamaño constante para las ejecuciones canceladas anteriormente":::

---

## <a name="sample-for-azure-powershell"></a>Ejemplo para Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En esta sección se muestra cómo usar Azure PowerShell para crear, iniciar y supervisar un desencadenador.

1. Cree un archivo JSON llamado **MyTrigger.json** en la carpeta C:\ADFv2QuickStartPSH\ con el siguiente contenido:

    > [!IMPORTANT]
    > Antes de guardar el archivo JSON, establezca el valor del elemento **startTime** en la hora UTC actual. Establezca el valor del elemento **endTime** en una hora anterior a la hora UTC actual.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Cree un desencadenador mediante el cmdlet **Set-AzDataFactoryV2Trigger**:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Confirme que el estado del desencadenador es **Detenido** mediante el cmdlet **Get-AzDataFactoryV2Trigger**:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie el desencadenador mediante el cmdlet **Start-AzDataFactoryV2Trigger**:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme que el estado del desencadenador es **Iniciado** mediante el cmdlet **Get-AzDataFactoryV2Trigger**:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Haga que el desencadenador se ejecute en Azure PowerShell mediante el cmdlet **Get-AzDataFactoryV2TriggerRun**. Para obtener información sobre cómo se ejecuta el desencadenador, ejecute el siguiente comando periódicamente. Actualice los valores **TriggerRunStartedAfter** y **TriggerRunStartedBefore** para que coincidan con los valores de la definición del desencadenador:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

Para supervisar las ejecuciones del desencadenador o de la canalización en Azure Portal, consulte la sección sobre la [supervisión de ejecuciones de canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información detallada acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#trigger-execution) (Ejecución de canalizaciones y desencadenadores).
* [Creación de una dependencia de un desencadenador periódico](tumbling-window-trigger-dependency.md).
* Aprenda a hacer referencia a los metadatos de desencadenador en la canalización; para ello, consulte [Referencia a los metadatos de desencadenador en las ejecuciones de canalización](how-to-use-trigger-parameterization.md).
