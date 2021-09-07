---
title: Solución de problemas de la API multivariante de Anomaly Detector
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo corregir códigos de error comunes al usar la API de Anomaly Detector.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomaly detection, machine learning, algorithms
ms.openlocfilehash: 46b67e3f465bfb481389049e4c45e5c2edbefbfc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292699"
---
# <a name="troubleshooting-the-multivariate-api"></a>Solución de problemas de la API multivariante

En este artículo se proporcionan instrucciones sobre cómo solucionar mensajes de error comunes al usar la API multivariante.

## <a name="multivariate-error-codes"></a>Códigos de error de multivariante

### <a name="common-errors"></a>Errores comunes

| Código de error                 | Código de error HTTP | Mensaje de error                                  | Comentario                                                      |
| -------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `SubscriptionNotInHeaders` | 400             | apim-subscription-id no se encuentra en los encabezados | Agregue el id. de suscripción de APIM en el encabezado. Encabezado de ejemplo: `{"apim-subscription-id": <Your Subscription ID>}` |
| `FileNotExist`             | 400             | El archivo \<source> no existe.                  | Compruebe la validez de la firma de acceso compartido (SAS) del blob. Asegúrese de que no haya expirado. |
| `InvalidBlobURL`           | 400             |                                                | La firma de acceso compartido (SAS) de blob no es una SAS válida.                            |
| `StorageWriteError`        | 403             |                                                | Este error puede deberse a problemas de permisos. Nuestro servicio no puede escribir los datos en el blob cifrado mediante una clave administrada por el cliente (CMK). Quite la CMK o vuelva a conceder acceso a nuestro servicio. Consulte [esta página](../../encryption/cognitive-services-encryption-keys-portal.md) para obtener más detalles. |
| `StorageReadError`         | 403             |                                                | Igual a `StorageWriteError`.                                 |
| `UnexpectedError`          | 500             |                                                | Póngase en contacto con nosotros para obtener información detallada del error. Puede realizar las opciones de soporte técnico de [este documento](../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fanomaly-detector%2fcontext%2fcontext) o enviarnos un correo electrónico a [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com)           |


### <a name="train-a-multivariate-anomaly-detection-model"></a>Entrenamiento de un modelo de detección de anomalías de multivariante

| Código de error               | Código de error HTTP | Mensaje de error                                                | Comentario                                                      |
| ------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `TooManyModels`          | 400             | Esta suscripción ha alcanzado el número máximo de modelos.  | Cada id. de suscripción de APIM puede tener 300 modelos activos. Elimine los modelos no usados antes de entrenar un nuevo modelo. |
| `TooManyRunningModels`   | 400             | Esta suscripción ha alcanzado el número máximo de modelos en ejecución. | Cada id. de suscripción de APIM puede entrenar 5 modelos simultáneamente. Entrene un nuevo modelo después de que los modelos anteriores hayan completado su proceso de entrenamiento. |
| `InvalidJsonFormat`      | 400             | Formato JSON no válido.                                         | La solicitud de entrenamiento no es un JSON válido.                        |
| `InvalidAlignMode`       | 400             | El valor del campo `'alignMode'` debe ser uno de los siguientes: `'Inner'` o `'Outer'`. | Compruebe el valor de `'alignMode'` que debe ser `'Inner'` o `'Outer'` (distingue mayúsculas de minúsculas). |
| `InvalidFillNAMethod`    | 400             | El valor del campo `'fillNAMethod'` debe ser uno de los siguientes: `'Previous'`, `'Subsequent'`, `'Linear'`, `'Zero'`, `'Fixed'` o `'NotFill'`, y no puede ser `'NotFill'` cuando `'alignMode'` es `'Outer'`. | Compruebe el valor de `'fillNAMethod'`. Consulte [esta sección](./best-practices-multivariate.md#optional-parameters-for-training-api) para más detalles. |
| `RequiredPaddingValue`   | 400             | El campo `'paddingValue'` es necesario en la solicitud cuando `'fillNAMethod'` es `'Fixed'`. | Debe proporcionar un valor de relleno válido cuando `'fillNAMethod'` sea `'Fixed'`. Consulte [esta sección](./best-practices-multivariate.md#optional-parameters-for-training-api) para más detalles. |
| `RequiredSource`         | 400             | El campo `'source'` es necesario en la solicitud.             | La solicitud de entrenamiento no ha especificado un valor para el campo `'source'`. Ejemplo: `{"source": <Your Blob SAS>}`. |
| `RequiredStartTime`      | 400             | El campo `'startTime'` es necesario en la solicitud.          | La solicitud de entrenamiento no ha especificado un valor para el campo `'startTime'`. Ejemplo: `{"startTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidTimestampFormat` | 400             | Formato de marca de tiempo no válido. `<timestamp>` no es un formato válido. | El formato de marca de tiempo en el cuerpo de la solicitud no es correcto. Puede probar `import pandas as pd; pd.to_datetime(timestamp)` para comprobar. |
| `RequiredEndTime`        | 400             | El campo `'endTime'` es necesario en la solicitud.            | La solicitud de entrenamiento no ha especificado un valor para el campo `'startTime'`. Ejemplo: `{"endTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidSlidingWindow`   | 400             | El campo `'slidingWindow'` debe ser un entero entre 28 y 2880. | `'slidingWindow'` debe ser un entero entre 28 y 2880 (ambos incluidos). |

### <a name="get-multivariate-model-with-model-id"></a>Obtención de un modelo multivariado con el id. de modelo

| Código de error      | Código de error HTTP | Mensaje de error             | Comentario                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
| `ModelNotExist` | 404             | El modelo no existe. | El modelo con el id. de modelo correspondiente no existe. Compruebe el id. del modelo en la dirección URL de la solicitud. |

### <a name="list-multivariate-models"></a>Enumeración de modelos multivariados

| Código de error      | Código de error HTTP | Mensaje de error             | Comentario                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
|`InvalidRequestParameterError`| 400             | Valores no válidos para $skip o $top... | Compruebe si los valores de los dos parámetros son numéricos. $skip y $top se usan para enumerar los modelos con paginación. Dado que la API solo devuelve los 10 modelos actualizados más recientemente, puede usar $skip y $top para obtener los modelos actualizados anteriormente. | 

### <a name="anomaly-detection-with-a-trained-model"></a>Detección de anomalías con un modelo entrenado

| Código de error        | Código de error HTTP | Mensaje de error                                                | Comentario                                                      |
| ----------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ModelNotExist`   | 404             | El modelo no existe.                                    | El modelo utilizado para la inferencia no existe. Compruebe el id. del modelo en la dirección URL de la solicitud. |
| `ModelFailed`     | 400             | No se pudo entrenar el modelo.                                  | El modelo no se entrenó correctamente. Obtenga información detallada obteniendo el modelo con el id. de modelo. |
| `ModelNotReady`   | 400             | El modelo aún no está preparado.                                  | El modelo aún no está preparado. Espere un tiempo hasta que se complete el proceso de entrenamiento. |
| `InvalidFileSize` | 413             | El archivo \<file> supera el límite de tamaño de archivo (\<size limit> bytes). | El tamaño de los datos de inferencia supera el límite superior (2 GB actualmente). Use menos datos para la inferencia. |

### <a name="get-detection-results"></a>Obtención de los resultados de detección

| Código de error       | Código de error HTTP | Mensaje de error              | Comentario                                                      |
| ---------------- | --------------- | -------------------------- | ------------------------------------------------------------ |
| `ResultNotExist` | 404             | Los resultados no existen. | El resultado por solicitud no existe. La inferencia no se ha completado o el resultado ha expirado (7 días). |

### <a name="data-processing-errors"></a>Errores de procesamiento de datos

Los siguientes códigos de error no tienen códigos de error HTTP asociados.

| Código de error             | Mensaje de error                                                | Comentario                                                      |
| ---------------------  | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `NoVariablesFound`     | No se han encontrado variables. Compruebe que los archivos están organizados según las instrucciones. | No se encontró ningún archivo CSV en el origen de datos. Esto suele deberse a una organización incorrecta de los archivos. Consulte los datos de ejemplo para ver la estructura deseada. |
| `DuplicatedVariables`  | Hay varias variables con el nombre mismo nombre.             | Hay nombres de variables duplicados.                         |
| `FileNotExist`         | El archivo \<filename> no existe.                              | Este error suele ocurrir durante la inferencia. La variable ha aparecido en los datos de entrenamiento, pero falta en los datos de inferencia. |
| `RedundantFile`        | El archivo \<filename> es redundante.                                | Este error suele ocurrir durante la inferencia. La variable no estaba en los datos de entrenamiento, pero aparece en los datos de inferencia. |
| `FileSizeTooLarge`     | El tamaño del archivo \<filename> es demasiado grande.                    | El tamaño del archivo CSV único \<filename> supera el límite. Entrene con menos datos. |
| `ReadingFileError`     | Se produjeron errores al leer \<filename>. \<error messages>    | No se pudo leer el archivo \<filename>. Puede consultar \<error messages> para obtener más detalles o comprobar con `pd.read_csv(filename)` en un entorno local. |
| `FileColumnsNotExist`  | La marca de tiempo de las columnas o el valor del archivo \<filename> no existen.  | Cada archivo CSV debe tener dos columnas con los nombres **timestamp** y **value** (distingue mayúsculas de minúsculas). |
| `VariableParseError`   | Error \<error message> de análisis de variable \<variable>.             | No se puede procesar \<variable> debido a errores en tiempo de ejecución. Consulte \<error message> para obtener más detalles o póngase en contacto con nosotros con \<error message>. |
| `MergeDataFailed`      | No se pudieron combinar los datos. Compruebe el formato de los datos.              | Error en la combinación de datos. Posiblemente se deba a un formato de datos incorrecto, a la organización de archivos, etc. Consulte los datos de ejemplo de la estructura de archivos actual. |
| `ColumnNotFound`       | No se puede encontrar la columna \<column> en los datos combinados.          | Falta una columna después de la combinación. Compruebe los datos.     |
| `NumColumnsMismatch`   | El número de columnas de datos combinados no coincide con el número de variables. | Compruebe los datos.                                      |
| `TooManyData`          | Demasiados puntos de datos. El número máximo es 1 000 000 por variable.       | Reduzca el tamaño de los datos de entrada.                        |
| `NoData`               | No hay datos efectivos.                                   | No hay ningún dato para entrenar o de inferencia después del procesamiento. Seleccione la hora de inicio y la hora de finalización. |
| `DataExceedsLimit`     | Longitud de los datos cuya marca de tiempo está entre `startTime` y `endTime` supera el límite (\<limit>). | El tamaño de los datos después del procesamiento supera el límite. (Actualmente no hay límite en los datos procesados). |
| `NotEnoughInput`       | No hay suficientes datos. La longitud de los datos es \<data length>, pero la longitud mínima debe ser mayor que la ventana deslizante, que es \<sliding window size>. | El número mínimo de puntos de datos para la inferencia es el tamaño de la ventana deslizante. Intente proporcionar más datos para la inferencia. |