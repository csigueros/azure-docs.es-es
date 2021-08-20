---
title: Parámetros de entrada de MVAD
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 5b4cea9dea4bb8ee6d1f32ec0e21fd87eb55da95
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215243"
---
### <a name="input-parameters"></a>Parámetros de entrada

#### <a name="required-parameters"></a>Parámetros obligatorios

Estos tres parámetros son necesarios en las solicitudes de API de entrenamiento e inferencia:

* `source`: vínculo al archivo ZIP que se encuentra en la instancia de Azure Blob Storage con las firmas de acceso compartido (SAS).
* `startTime`: hora de inicio de los datos usados para el entrenamiento o la inferencia. Si es anterior a la marca de tiempo más temprana real de los datos, se usará la marca de tiempo más temprana real como punto de partida.
* `endTime`: hora final de los datos usados para el entrenamiento o la inferencia, que debe ser posterior o igual a `startTime`. Si `endTime` es posterior a la marca de tiempo más reciente real de los datos, se usará la marca de tiempo más reciente real como punto final. Si `endTime` es igual a `startTime`, significa la inferencia de un único punto de datos, que se suele usar en escenarios de streaming.

#### <a name="optional-parameters-for-training-api"></a>Parámetros opcionales para la API de entrenamiento

Otros parámetros de la API de entrenamiento son opcionales:

* `slidingWindow`: cuántos puntos de datos se usan para determinar las anomalías. Número entero entre 28 y 2880. El valor predeterminado es 300. Si `slidingWindow` es `k` para el entrenamiento del modelo, deben ser accesibles al menos `k` puntos desde el archivo de origen durante la inferencia para obtener resultados válidos.

    MVAD toma un segmento de puntos de datos para decidir si el siguiente punto de datos es una anomalía. La longitud del segmento es `slidingWindow`.
    Tenga en cuenta dos cosas al elegir el valor de `slidingWindow`:
    1. Las propiedades de los datos: si son periódicos y la frecuencia de muestreo. Cuando los datos son periódicos, puede establecer una longitud de 1 a 3 ciclos como valor de `slidingWindow`. Cuando los datos tienen una frecuencia alta (granularidad pequeña), como en el nivel de minutos o segundos, podría establecer un valor relativamente superior para `slidingWindow`.
    1. El equilibrio entre el tiempo de entrenamiento o inferencia y el posible impacto en el rendimiento. Un valor mayor de `slidingWindow` puede provocar un tiempo de entrenamiento o inferencia más largo. **No hay ninguna garantía** de que valores mayores de `slidingWindow` vayan a dar lugar a mejoras de precisión. Un valor pequeño de `slidingWindow` puede hacer difícil que el modelo vaya a converger en una solución óptima. Por ejemplo, es difícil detectar anomalías cuando `slidingWindow` solo tiene dos puntos.

* `alignMode`: cómo alinear varias variables (series temporales) en las marcas de tiempo. Hay dos opciones para este parámetro, `Inner` y `Outer`, y el valor predeterminado es `Outer`.

    Este parámetro es fundamental cuando hay una alineación incorrecta entre las secuencias de marcas de tiempo de las variables. El modelo debe alinear las variables en la misma secuencia de marcas de tiempo antes de continuar el procesamiento.

    `Inner` significa que el modelo solo mostrará los resultados de la detección en las marcas de tiempo en las que **todas las variables** tienen un valor, por ejemplo, la intersección de todas las variables. `Outer` significa que el modelo solo mostrará los resultados de la detección en las marcas de tiempo en las que **alguna variable** tiene un valor, por ejemplo, la unión de todas las variables.

    Este es un ejemplo para explicar los distintos valores de `alignModel`.

    *Variable-1*

    |timestamp | value|
    ----------| -----|
    |2020-11-01| 1  
    |2020-11-02| 2  
    |2020-11-04| 4  
    |05-11-2020| 5

    *Variable-2*

    timestamp | value  
    --------- | -
    2020-11-01| 1  
    2020-11-02| 2  
    2020-11-03| 3  
    2020-11-04| 4

    *Combinación `Inner` de dos variables*

    timestamp | Variable-1 | Variable-2
    ----------| - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-04| 4 | 4

    *Combinación `Outer` de dos variables*

    timestamp | Variable-1 | Variable-2
    --------- | - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-03| `nan` | 3
    2020-11-04| 4 | 4
    05-11-2020| 5 | `nan`

* `fillNAMethod`: cómo rellenar `nan` en la tabla combinada. Es posible que falten valores en la tabla combinada y se deben controlar adecuadamente. Proporcionamos varios métodos para rellenarlos. Las opciones son `Linear`, `Previous`, `Subsequent`, `Zero` y `Fixed`, y el valor predeterminado es `Linear`.

    | Opción     | Método                                                                                           |
    | ---------- | -------------------------------------------------------------------------------------------------|
    | `Linear`     | Rellenar los valores `nan` por interpolación lineal                                                           |
    | `Previous`   | Propagar el último valor válido para rellenar los espacios. Ejemplo: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
    | `Subsequent` | Usar el siguiente valor válido para rellenar los espacios. Ejemplo: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
    | `Zero`       | Rellenar los valores `nan` con 0.                                                                           |
    | `Fixed`      | Rellenar los valores `nan` con un valor válido especificado que se debe proporcionar en `paddingValue`.          |

* `paddingValue`: el valor de relleno se usa para rellenar los valores `nan` cuando `fillNAMethod` es `Fixed` y se debe proporcionar en ese caso. En otros casos, es opcional.

* `displayName`: parámetro opcional que se usa para identificar los modelos. Por ejemplo, puede usarlo para marcar parámetros, orígenes de datos y cualquier otro metadato sobre el modelo y sus datos de entrada. El valor predeterminado es una cadena vacía.
