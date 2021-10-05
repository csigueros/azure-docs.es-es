---
title: Creación de dependencias en un desencadenador de ventana de saltos de tamaño constante
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a crear dependencia de un desencadenador periódico en Azure Data Factory y Synapse Analytics.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 89a6db7fcfd78ec2e1115256cc162a91cc9180da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805936"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Creación de una dependencia de un desencadenador de ventana de saltos de tamaño constante
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se exponen los pasos necesarios para crear una dependencia en un desencadenador de ventana de saltos de tamaño constante. Para obtener información general acerca de los desencadenadores de ventanas de saltos de tamaño constante, consulte [Cómo crear un desencadenador de ventana de saltos de tamaño constante](how-to-create-tumbling-window-trigger.md).

Con el fin de generar una cadena de dependencia y de asegurarse de que un desencadenador se ejecuta solo después de la correcta ejecución de otro desencadenador en el servicio, utilice esta característica avanzada para crear una dependencia periódica.

Puede ver una demostración de cómo crear canalizaciones dependientes mediante el desencadenador periódico en el vídeo siguiente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-ui"></a>Creación de una dependencia en la interfaz de usuario

Para crear una dependencia en un desencadenador, seleccione **Desencadenador > Avanzado > Nuevo**, y luego elija el desencadenador del que se va a depender con el desplazamiento y tamaño adecuados. Seleccione **Finalizar** y publique los cambios para que se apliquen las dependencias.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png" alt-text="Creación de dependencias":::

## <a name="tumbling-window-dependency-properties"></a>Propiedades de dependencia de ventana de saltos de tamaño constante

Un desencadenador de ventana de saltos de tamaño constante con una dependencia tiene las siguientes propiedades:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
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
        }
    }
}
```

La tabla siguiente proporciona la lista de los atributos necesarios para definir una dependencia de ventana de saltos de tamaño constante.

| **Nombre de la propiedad** | **Descripción**  | **Tipo** | **Obligatorio** |
|---|---|---|---|
| type  | Todos los desencadenadores de ventana de saltos de tamaño constante existentes se muestran en este menú desplegable. Elija el desencadenador del que se va a depender.  | TumblingWindowTriggerDependencyReference o SelfDependencyTumblingWindowTriggerReference. | Sí |
| offset | Desplazamiento del desencadenador de dependencia. Proporcione un valor en formato de intervalo de tiempo; admite desplazamientos negativos y positivos. Esta propiedad es obligatoria si el desencadenador depende de sí mismo; en todos los demás casos, es opcional. La autodependencia debe tener siempre un valor de desplazamiento negativo. Si no se especifica ningún valor, la ventana es igual al desencadenador. | TimeSpan<br/>(hh:mm:ss) | Autodependencia: Sí<br/>Otros: No |
| tamaño | Tamaño de la ventana de saltos de tamaño constante de dependencia. Proporcione un valor de intervalo de tiempo positivo. Esta propiedad es opcional. | TimeSpan<br/>(hh:mm:ss) | No  |

> [!NOTE]
> Un desencadenador de ventana de saltos de tamaño constante puede depender de un máximo de otros cinco desencadenadores.

## <a name="tumbling-window-self-dependency-properties"></a>Propiedades de la autodependencia de ventana de saltos de tamaño constante

En los escenarios en los que el desencadenador no debe continuar a la siguiente ventana hasta que se haya completado correctamente la ventana anterior, cree una autodependencia. Un desencadenador de autodependencia que depende del éxito de sus propias ejecuciones anteriores durante la hora anterior tendrá las propiedades que se indican en el código siguiente.

> [!NOTE]
> Si la canalización desencadenada se basa en la salida de las canalizaciones en las ventanas desencadenadas previamente, se recomienda usar solo la autodependencia del desencadenador de la ventana de saltos de tamaño constante. Para limitar ejecuciones de desencadenador paralelas, defina la simultaneidad de desencadenador máxima.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Escenarios de uso y ejemplos

A continuación se muestran las ilustraciones de los escenarios y el uso de las propiedades de dependencia de ventana de saltos de tamaño constante.

### <a name="dependency-offset"></a>Desplazamiento de la dependencia

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png" alt-text="Ejemplo de desplazamiento":::

### <a name="dependency-size"></a>Tamaño de la dependencia

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png" alt-text="Ejemplo de tamaño":::

### <a name="self-dependency"></a>Autodependencia

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png" alt-text="Autodependencia":::

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependencia de otro desencadenador de ventana de saltos de tamaño constante

Un trabajo de procesamiento de datos de telemetría diario que dependa de otro trabajo diario que agregue la salida de los últimos siete días de salida y genere secuencias de ventana gradual de siete días:

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png" alt-text="Ejemplo de dependencia":::

### <a name="dependency-on-itself"></a>Dependencia de sí mismo

Un trabajo diario sin interrupciones en los flujos de salida del trabajo:

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png" alt-text="Ejemplo de autodependencia":::

## <a name="monitor-dependencies"></a>Supervisión de dependencias

Puede supervisar la cadena de dependencias y las ventanas correspondientes desde la página de supervisión de ejecución del desencadenador. Vaya a  **Supervisión > Trigger Runs (Ejecuciones de desencadenador)** . Si un desencadenador de ventana de saltos de tamaño constante tiene dependencias, el nombre del desencadenador llevará un hipervínculo a la vista de supervisión de dependencias.  

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png" alt-text="Supervisión de las ejecuciones del desencadenador":::

Haga clic en el nombre del desencadenador para ver sus dependencias. En el panel derecho se muestra información detallada sobre la ejecución del desencadenador, como el identificador RunID, la ventana de tiempo, el estado, etc.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png" alt-text="Vista de lista de la supervisión de dependencias":::

Puede ver el estado de las dependencias y las ventanas de cada desencadenador dependiente. Si se produce un error en uno de los desencadenadores de dependencias, debe volver a ejecutarlo correctamente para que se ejecute el desencadenador dependiente.

Un desencadenador de ventana de saltos de tamaño constante esperará las dependencias durante _siete días_ antes de que se agote el tiempo de espera. Pasados los siete días, se producirá un error en la ejecución del desencadenador.

Para ver una vista más clara de la programación de dependencia del desencadenador, seleccione la vista Gantt.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png" alt-text="Gráfico de Gantt de la supervisión de dependencias":::

Los cuadros transparentes muestran las ventanas de dependencia de cada desencadenador dependiente descendiente, mientras que los cuadros de color sólido que aparecen encima muestran las ventanas de ejecución individuales. A continuación se muestran algunas sugerencias para interpretar la vista del gráfico de Gantt:

* El cuadro transparente se representa en azul cuando las ventanas dependientes están en estado pendiente o en ejecución
* Una vez que todas las ventanas se hayan realizado correctamente para un desencadenador dependiente, el cuadro transparente se volverá verde
* El cuadro transparente se representa en rojo cuando se produce un error en una ventana dependiente. Busque un cuadro rojo sólido para identificar un error de ejecución de ventana

Para volver a ejecutar una ventana en la vista del gráfico de Gantt, seleccione el cuadro de color sólido de la ventana y aparecerá un panel de acciones con los detalles y las opciones para volver a ejecutar.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Cómo crear un desencadenador de ventana de saltos de tamaño constante](how-to-create-tumbling-window-trigger.md)
