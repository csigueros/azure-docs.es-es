---
title: Convertir en valores de indicador
titleSuffix: Azure Machine Learning
description: Use el componente Convert to Indicator Values (Convertir en valores de indicador) en el diseñador de Azure Machine Learning para convertir las columnas de categorías en una serie de columnas de indicadores binarios.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: c99944a21e648dcd9cecfc96489a58f776395ff4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565898"
---
# <a name="convert-to-indicator-values"></a>Convertir en valores de indicador
En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use el componente **Convert to Indicator Values** (Convertir en valores de indicador) en el diseñador de Azure Machine Learning para convertir las columnas que contienen valores de categorías en una serie de columnas de indicadores binarios.  

Este componente también genera una definición de la transformación usada para la conversión en valores de indicador. Puede volver a usar esta transformación en otros conjuntos de datos que tengan el mismo esquema mediante el componente [Aplicar transformación](apply-transformation.md).

## <a name="how-to-configure-convert-to-indicator-values"></a>Procedimiento para configurar Convertir en valores de indicador

1.  Busque el módulo **Convertir en valores de indicador** y arrástrelo hasta el borrador de la canalización. Puede encontrar este componente en la categoría **Transformación de datos**.
    > [!NOTE]
    > Puede usar el componente [Editar metadatos](edit-metadata.md) antes del módulo **Convert to Indicator Values** (Convertir en valores de indicador) para marcar las columnas de destino como categorías.

1. Conecte el componente **Convert to Indicator Values** (Convertir en valores de indicador) al conjunto de datos que contiene las columnas que desea convertir. 

1. Use **Editar columna** para elegir una o varias columnas de categorías.

1. Seleccione la opción **Overwrite categorical columns** (Sobrescribir columnas de categorías) si quiere devolver **únicamente** las nuevas columnas de valores booleanos. De forma predeterminada, esta opción está desactivada.
    

    > [!TIP]
    >  Si elige la opción para sobrescribir, la columna de origen no se elimina ni modifica realmente. En su lugar, las columnas nuevas se generan y presentan en el conjunto de datos de salida, y la columna de origen sigue disponible en el área de trabajo. Si necesita ver los datos originales, puede usar el componente [Agregar columnas](add-columns.md) en cualquier momento para volver a agregar la columna de origen.

1. Envíe la canalización.

## <a name="results"></a>Results

Supongamos que tiene una columna con puntuaciones que indican si un servidor tiene una probabilidad de errores alta, media o baja.  

| Id. de servidor | Puntuación de error |
| --------- | ------------- |
| 10301     | Bajo           |
| 10302     | Media        |
| 10303     | Alto          |

Al aplicar el módulo **Convertir en valores de indicador**, el diseñador convierte una sola columna de etiquetas en varias columnas que contienen valores booleanos:  

| Id. de servidor | Puntuación de error - baja | Puntuación de error - media | Puntuación de error - alta |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

A continuación se muestra el funcionamiento de la conversión:  

-   En la columna **Puntuación de error** que describe el riesgo, solo hay tres valores posibles (alta, media y baja) y no falta ningún valor. Por lo tanto, se crean exactamente tres columnas nuevas.  

-   Los nombres de las nuevas columnas de indicador se basan en los encabezados de columna y en los valores de la columna de origen, con este patrón: *\<source column>- \<data value>* .  

-   Debe haber un número 1 en exactamente una columna de indicador, y un número 0 en todas las demás columnas de indicador, ya que cada servidor solo puede tener una clasificación de riesgo.  

Ahora puede usar las tres columnas de indicador como características en un modelo de Machine Learning.

El componente devuelve dos resultados:

- **Results dataset** (Conjunto de datos de resultados): un conjunto de datos con columnas de valores de indicador convertidos. Las columnas no seleccionadas para la limpieza también se "pasan a través".
- **Indicator values transformation** (Transformación de valores de indicador): una transformación de datos utilizada para la conversión en valores de indicador, que se puede guardar en el área de trabajo para aplicarse a datos nuevos más tarde.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Aplicación de una operación de valores de indicador guardada a datos nuevos

Si necesita repetir las operaciones de valores de indicador a menudo, puede guardar los pasos para la manipulación de datos como una *transformación* para reutilizarla con el mismo conjunto de datos. Esto resulta útil si frecuentemente debe volver a importar y limpiar datos que tienen el mismo esquema.

1. Agregue el componente [Aplicar transformación](apply-transformation.md) a la canalización.

1. Agregue el conjunto de datos que desea limpiar y conecte el conjunto de datos al puerto de entrada derecho.

1. Expanda el grupo **Transformación de datos** en el panel izquierdo del diseñador. Localice la transformación guardada y arrástrela hasta la canalización.

1. Conecte la transformación guardada al puerto de entrada izquierdo de [Aplicar transformación](apply-transformation.md).

   Al aplicar una transformación guardada, no se pueden seleccionar las columnas que se van a transformar. Esto se debe a que la transformación ya se ha definido y se aplica automáticamente a los tipos de datos especificados en la operación original.

1. Envíe la canalización.
 
## <a name="technical-notes"></a>Notas técnicas  

Esta sección contiene detalles de implementación, sugerencias y respuestas a las preguntas más frecuentes.

### <a name="usage-tips"></a>Consejos de uso

-   Solamente las columnas que están marcadas como categorías se pueden convertir en columnas de indicador. Si se muestra el siguiente error, es probable que una de las columnas seleccionadas no sea una categoría:  

     Error 0056: La columna con nombre \<column name> no pertenece a una categoría permitida.  

     De forma predeterminada, la mayoría de las columnas de cadena se tratan como características de cadena, por lo que debe marcarlas de forma explícita como categorías a través de [Editar metadatos](edit-metadata.md).  

-   No hay ningún límite para el número de columnas que se pueden convertir en columnas de indicador. No obstante, dado que cada columna de valores puede generar varias columnas de indicador, debería convertir y revisar solo algunas columnas cada vez.  

-   Si la columna contiene valores ausentes, se crea una columna de indicador independiente para la categoría que falta con este nombre: *\<source column>- Missing*  

-   Si la columna que va a convertir en valores de indicador contiene números, se deben marcar como categorías como cualquier otra columna de características. Una vez marcados, los números se tratan como valores discretos. Por ejemplo, si tiene una columna numérica con valores de MPG entre 25 y 30, se creará una nueva columna de indicador para cada valor discreto:  

    | Asegúrese       | MPG en autopista - 25 | MPG en autopista - 26 | MPG en autopista - 27 | MPG en autopista - 28 | MPG en autopista - 29 | MPG en autopista - 30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Automóviles de Contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Para evitar que se agreguen demasiadas dimensiones al conjunto de datos, se recomienda comprobar primero el número de valores de la columna, así como ubicar o cuantificar los datos de forma adecuada.  


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
