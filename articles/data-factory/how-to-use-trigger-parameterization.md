---
title: Paso de información del desencadenador a la canalización
description: Aprenda a hacer referencia a los metadatos de desencadenador en una canalización.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 4d51377daeeb16d7e3e74a77a4379b6fc13cb94b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637879"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Referencia a los metadatos de desencadenador en las ejecuciones de canalización

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se describe cómo se pueden usar los metadatos de desencadenador, como la hora de inicio del desencadenador, en las ejecuciones de canalizaciones.

A veces, la canalización debe comprender y leer los metadatos del desencadenador que la invoca. Por ejemplo, con la ejecución de un desencadenador periódico, la canalización procesará segmentos o carpetas de datos diferentes, en función de la hora de inicio y de finalización de la ventana. En Azure Data Factory, se usa la parametrización y la [variable del sistema](control-flow-system-variables.md) para pasar metadatos de un desencadenador a una canalización.

Este patrón es especialmente útil para el [desencadenador periódico](how-to-create-tumbling-window-trigger.md), donde el desencadenador proporciona la hora de inicio y de finalización de la ventana, y el [desencadenador de evento personalizado](how-to-create-custom-event-trigger.md), donde el desencadenador analiza y procesa los valores en el [campo de _datos_ definido por el usuario](../event-grid/event-schema.md).

> [!NOTE]
> Un tipo de desencadenador diferente proporciona información de metadatos diferente. Para más información, consulte [Variable del sistema](control-flow-system-variables.md).

## <a name="data-factory-ui"></a>Interfaz de usuario de Data Factory

En esta sección se muestra cómo pasar información de metadatos de un desencadenador a una canalización, dentro de la interfaz de usuario de Azure Data Factory.

1. Vaya al **lienzo de creación** y edite una canalización.

1. Haga clic en el lienzo en blanco para abrir la configuración de canalización. No seleccione ninguna actividad. Puede que tenga que desplegar el panel de configuración desde la parte inferior del lienzo, ya que puede que se haya contraído

1. Seleccione la sección **Parámetros** y elija **+ Nuevo** para agregar parámetros.

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Captura de pantalla de la configuración de canalización que muestra cómo definir parámetros en la canalización.":::

1. Para agregar desencadenadores a la canalización, haga clic en **+ Desencadenador**.

1. Cree o asocie un desencadenador a la canalización y haga clic en **Aceptar**.

1. En la página siguiente, rellene los metadatos del desencadenador de cada parámetro. Use el formato definido en [Variable del sistema](control-flow-system-variables.md) para recuperar la información del desencadenador. No es necesario rellenar la información de todos los parámetros, solo los que asumirán los valores de los metadatos del desencadenador. Por ejemplo, aquí asignamos la hora de inicio de ejecución del desencadenador a *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Captura de pantalla de la página de definición del desencadenador que muestra cómo pasar información del desencadenador a los parámetros de canalización.":::

1. Para usar los valores en la canalización, utilice los parámetros _@pipeline().parameters.parameterName_, __no la variable del sistema__ , en las definiciones de la canalización. Por ejemplo, en nuestro caso, para leer la hora de inicio del desencadenador, haremos referencia a @pipeline().parameters.parameter_1.

## <a name="json-schema"></a>Esquema JSON

Para pasar la información del desencadenador a las ejecuciones de canalización, tanto el desencadenador como el código JSON de la canalización deben actualizarse con la sección _parameters_.

### <a name="pipeline-definition"></a>Definición de la canalización

En la sección **properties** agregue definiciones de parámetros a la sección **parameters**.

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
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
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Definición de desencadenador

En la sección **pipelines**, asigne valores de parámetro en la sección **parameters**. No es necesario rellenar la información de todos los parámetros, solo los que asumirán los valores de los metadatos del desencadenador.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Uso de la información del desencadenador en la canalización

Para usar los valores en la canalización, utilice los parámetros _@pipeline().parameters.parameterName_, __no la variable del sistema__ , en las definiciones de la canalización.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información detallada acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#trigger-execution) (Ejecución de canalizaciones y desencadenadores).
