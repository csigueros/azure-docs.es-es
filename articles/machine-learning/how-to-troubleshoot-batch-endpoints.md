---
title: Solución de problemas de puntos de conexión por lotes (versión preliminar)
titleSuffix: Azure Machine Learning
description: Sugerencias para el uso correcto de los puntos de conexión por lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting, devplatv2
ms.reviewer: laobri
ms.author: tracych
author: tracych
ms.date: 10/21/2021
ms.openlocfilehash: 97b235e45e1f988fde94272252e738d38b0a53e6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564677"
---
# <a name="troubleshooting-batch-endpoints-preview"></a>Solución de problemas de puntos de conexión por lotes (versión preliminar)

Obtenga información sobre cómo solucionar errores comunes que pueden producirse al usar [puntos de conexión por lotes](how-to-use-batch-endpoint.md) (versión preliminar) para la puntuación por lotes.

 [!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

La tabla siguiente contiene problemas comunes y soluciones que pueden observarse durante el desarrollo y el consumo de puntos de conexión por lotes.

| Problema | Posible solución |
|--|--|
| Falta la configuración de código o el entorno. | Asegúrese de proporcionar el script de puntuación y una definición de entorno si usa un modelo que no es de MLflow. La implementación sin programación solo se admite para el modelo de MLflow. Para más información, consulte [Seguimiento de modelos de Machine Learning con MLflow y Azure Machine Learning](how-to-use-mlflow.md).|
| Datos de entrada no admitidos. | El punto de conexión por lotes acepta datos de entrada de tres formas: 1) datos registrados 2) datos en la nube 3) datos locales. Asegúrese de utilizar el formato adecuado. Para más información, consulte [Uso de puntos de conexión por lotes (versión preliminar) para la puntuación por lotes](how-to-use-batch-endpoint.md).|
| La salida ya existe. | Si configura su propia ubicación de salida, asegúrese de proporcionar una nueva salida para cada invocación de punto de conexión. |

## <a name="understanding-logs-of-a-batch-scoring-job"></a>Descripción de los registros de un trabajo de puntuación por lotes

### <a name="get-logs"></a>Obtención de registros

Después de invocar un punto de conexión por lotes mediante la CLI de Azure o REST, el trabajo de puntuación por lotes se ejecutará de forma asincrónica. Existen dos opciones para obtener los registros para un trabajo de puntuación por lotes.

Opción 1: Transmisión de registros a la consola local

Puede ejecutar el siguiente comando para transmitir los registros generados por el sistema a la consola. Solo se transmitirán los registros de la carpeta `azureml-logs`.

```bash
az ml job stream -name <job_name>
```

Opción 2: Visualización de registros en Studio 

Para obtener el vínculo a la ejecución en Studio, ejecute: 

```azurecli
az ml job show --name <job_name> --query interaction_endpoints.Studio.endpoint -o tsv
```

1. Abra el trabajo en Studio con el valor devuelto por el comando anterior. 
1. Seleccione **batchscoring** (puntuación por lotes).
1. Abra la pestaña **Outputs + logs** (Resultados y registros). 
1. Seleccione los registros que desee revisar.

### <a name="understand-log-structure"></a>Descripción de la estructura de los registros

Existen dos carpetas de registro de nivel superior: `azureml-logs` y `logs`. 

El archivo `~/azureml-logs/70_driver_log.txt` contiene información del controlador que inicia el script de puntuación.  

Debido a la naturaleza distribuida de los trabajos de puntuación por lotes, hay registros de distintos orígenes. No obstante, se crean dos archivos combinados que proporcionan información de alto nivel: 

- `~/logs/job_progress_overview.txt`: este archivo proporciona información de alto nivel sobre el número de minilotes (también conocidos como tareas) que se han creado hasta el momento y el número de minilotes que se han procesado hasta ahora. Cuando finalizan los mini lotes, el registro registra los resultados del trabajo. Si se produjo un error en el trabajo, se mostrará el mensaje de error y dónde se debe iniciar la solución de problemas.

- `~/logs/sys/master_role.txt`: Este archivo proporciona la vista del nodo principal (también conocido como orquestador) del trabajo en ejecución. Este registro proporciona información sobre la creación de tareas, la supervisión del progreso y el resultado de la ejecución.

Para obtener una descripción concisa de los errores del script, hay lo siguiente:

- `~/logs/user/error.txt`: este archivo intentará resumir los errores del script.

Para obtener más información sobre los errores del script, hay lo siguiente:

- `~/logs/user/error/`: este archivo contiene seguimientos de pila completos de las excepciones producidas al cargar y ejecutar el script de entrada.

Cuando necesite comprender en detalle cómo ejecuta cada nodo el script de puntuación, examine los registros de proceso individuales para cada nodo. Los registros de proceso se pueden encontrar en la carpeta `sys/node`, agrupados por nodos de trabajo:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: este archivo proporciona información detallada sobre cada uno de los minilotes a medida que un trabajo lo elige o lo completa. Para cada minilote, este archivo incluye:

    - La dirección IP y el PID del proceso de trabajo. 
    - El número total de elementos, el número de elementos procesados correctamente y el número de elementos con errores.
    - Hora de inicio, duración, tiempo de proceso y tiempo del método de ejecución.

También puede ver los resultados de las comprobaciones periódicas del uso de recursos de cada nodo. Los archivos de registro y los archivos de configuración se encuentran en esta carpeta:

- `~/logs/perf`: establezca `--resource_monitor_interval` para cambiar el intervalo de comprobación en segundos. El intervalo predeterminado es `600`, que son aproximadamente 10 minutos. Para detener la supervisión, establezca el valor en `0`. Cada carpeta `<ip_address>` incluye:

    - `os/`: información acerca de todos los procesos en ejecución en el nodo. Una comprobación ejecuta un comando del sistema operativo y guarda el resultado en un archivo. En Linux, el comando es `ps`.
        - `%Y%m%d%H`: el nombre de la subcarpeta es la fecha y hora.
            - `processes_%M`: el archivo finaliza con el minuto de la hora de la comprobación.
    - `node_disk_usage.csv`: detalles de uso del disco del nodo.
    - `node_resource_usage.csv`: información general sobre el uso de recursos del nodo.
    - `processes_resource_usage.csv`: información general sobre el uso de recursos de cada proceso.

### <a name="how-to-log-in-scoring-script"></a>Cómo registrar en el script de puntuación

Puede usar el registro de Python en el script de puntuación. Los registros se almacenan en `logs/user/stdout/<node_id>/processNNN.stdout.txt`. 

```python
import argparse
import logging

# Get logging_level
arg_parser = argparse.ArgumentParser(description="Argument parser.")
arg_parser.add_argument("--logging_level", type=str, help="logging level")
args, unknown_args = arg_parser.parse_known_args()
print(args.logging_level)

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.logging_level.upper())
logger.info("Info log statement")
logger.debug("Debug log statement")
```