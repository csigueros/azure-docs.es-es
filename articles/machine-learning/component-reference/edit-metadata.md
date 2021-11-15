---
title: 'Edición de metadatos: Referencia del componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Editar metadatos de Azure Machine Learning para cambiar los metadatos que están asociados con las columnas de un conjunto de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: 6eef946b88f4f77167623480a86a82c34714ba69
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565890"
---
# <a name="edit-metadata-component"></a>Componente Editar metadatos

En este artículo se describe un componente incluido en el diseñador de Azure Machine Learning.

Use el componente Editar metadatos para cambiar los metadatos asociados con las columnas de un conjunto de datos. El valor y el tipo de datos del conjunto de datos cambiará después de usar el componente Editar metadatos.

Entre los cambios de metadatos habituales se pueden incluir:
  
+ El tratamiento de columnas booleanas o numéricas como valores de categorías.
  
+ Indicación de qué columna contiene la etiqueta **class** o contiene los valores que desea clasificar o predecir.
  
+ Marcado de columnas como características.
  
+ Cambio de los valores de fecha y hora a valores numéricos o viceversa.
  
+ Cambio de nombre de las columnas.
  
 Use Editar metadatos siempre que necesite modificar la definición de una columna, normalmente para cumplir los requisitos de un componente de bajada. Por ejemplo, algunos componentes solo funcionan con tipos de datos específicos o requieren marcas en las columnas, como `IsFeature` o `IsCategorical`.  
  
 Después de realizar la operación necesaria, puede restablecer los metadatos a su estado original.
  
## <a name="configure-edit-metadata"></a>Configuración del módulo Editar metadatos
  
1. En el diseñador de Azure Machine Learning, agregue el componente Editar metadatos a la canalización y conecte el conjunto de datos que desea actualizar. Puede encontrar el componente en la categoría **Transformación de datos**.
  
1. Haga clic en **Editar columna** en el panel derecho del componente y elija la columna o el conjunto de columnas con el que desea trabajar. Puede elegir las columnas individualmente por nombre o por índice, o puede elegir un grupo de columnas por tipo.  
  
1. Seleccione la opción **Tipo de datos** si tiene que asignar un tipo de datos diferente a las columnas seleccionadas. Es posible que deba cambiar el tipo de datos para determinadas operaciones. Por ejemplo, si el conjunto de datos de origen tiene números tratados como texto, debe cambiarlos a un tipo de datos numérico antes de usar las operaciones matemáticas.

    + Los tipos de datos admitidos son **cadena**, **entero**, **doble**, **booleano** y **DateTime**.

    + Si selecciona varias columnas, debe aplicar los cambios de los metadatos en *todas* las columnas seleccionadas. Por ejemplo, supongamos que elige dos o tres columnas numéricas. Puede cambiarlas todas al tipo de datos de cadena y cambiar su nombre en una sola operación. Sin embargo, no se puede cambiar una columna a un tipo de datos de cadena y otra columna de float a entero.
  
    + Si no especifica un nuevo tipo de datos, los metadatos de la columna no cambiarán.

    + El tipo de columna y los valores cambiarán después de realizar la operación Editar metadatos. Puede recuperar el tipo de datos original en cualquier momento mediante Editar metadatos para restablecer el tipo de datos de la columna.  

    > [!NOTE]
    > El **formato de fecha y hora** sigue el [formato de fecha y hora integrado](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior) de Python.  
    > Si cambia cualquier tipo de número al tipo **DateTime**, deje el campo **Formato de fecha y hora** en blanco. Actualmente no es posible especificar el formato de datos de destino.

1. Seleccione la opción **Categórico** para especificar que los valores de las columnas seleccionadas deben tratarse como categorías.

    Por ejemplo, podría tener una columna que contiene los números 0, 1 y 2, pero sabe que estos números realmente significan "Fumador", "No fumador" y "Desconocido". En ese caso, al marcar la columna con la opción Categórico se asegura de que los valores se usan solo para agrupar los datos y no en cálculos numéricos.
  
1. Use la opción **Campos** si desea cambiar la forma en que Azure Machine Learning usa los datos de un modelo.

    + **Característica**: Use esta opción para marcar una columna como característica en los componentes que funcionan solo en las columnas de características. De forma predeterminada, todas las columnas se tratan inicialmente como características.  
  
    + **Etiqueta**: Use esta opción para marcar la etiqueta, también conocida como atributo de predicción o variable de destino. Muchos componentes requieren que haya exactamente una columna de etiqueta en el conjunto de datos.

        En muchos casos, Azure Machine Learning puede deducir que una columna contiene una etiqueta de clase. Estableciendo estos metadatos, se puede asegurar de que la columna se identifica correctamente. El establecimiento de esta opción no cambia los valores de los datos. Solo cambia la forma en la que algunos algoritmos de Machine Learning administran los datos.
  
    > [!TIP]
    > ¿Tiene datos que no encajan en estas categorías? Por ejemplo, el conjunto de datos puede contener valores como identificadores únicos que no son útiles como variables. A veces, estos identificadores pueden causar problemas cuando se usan en un modelo.
    >
    > Afortunadamente, Azure Machine Learning conserva todos los datos, por lo que no tiene que eliminar estas columnas del conjunto de datos. Cuando necesite realizar operaciones en un conjunto especial de columnas, simplemente quite temporalmente todas las demás columnas mediante el componente [Select Columns in Dataset](select-columns-in-dataset.md) (Seleccionar columnas del conjunto de datos). Más adelante puede combinar las columnas de nuevo en el conjunto de datos mediante el componente [Agregar columnas](add-columns.md).  
  
1. Utilice las siguientes opciones para borrar las selecciones anteriores y restaurar los metadatos a los valores predeterminados.  
  
    + **Borrar característica**: Utilice esta opción para quitar la marca de características.  
  
         Todas las columnas se tratan inicialmente como características. En el caso de los componentes que realizan operaciones matemáticas, debe usar esta opción para evitar que las columnas numéricas se traten como variables.
  
    + **Borrar etiqueta**: Use esta opción para quitar los metadatos de **etiqueta** de la columna especificada.  
  
    + **Borrar puntuación**: Use esta opción para quitar los metadatos de **puntuación** de la columna especificada.  
  
         Actualmente no puede marcar explícitamente una columna como puntuación en Azure Machine Learning. Sin embargo, algunas operaciones darán como resultado que una columna se etiquete internamente como puntuación. Además, un componente de R personalizado puede generar valores de puntuación.

1. En **New column names** (Nuevos nombres de columna), escriba el nuevo nombre de las columnas seleccionadas.  
  
    + En los nombres de columna solo se pueden usar caracteres que sean compatibles con la codificación UTF-8. No se permiten cadenas vacías, valores NULL o nombres que consten únicamente de espacios.  
  
    + Para cambiar el nombre de varias columnas, escriba los nombres como una lista separada por comas en el orden de los índices de columna.  
  
    + Se debe cambiar el nombre de todas las columnas seleccionadas. No puede omitir columnas.  
  
1. Envíe la canalización.  

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.
