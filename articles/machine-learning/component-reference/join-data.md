---
title: 'Combinación de datos: referencia de componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Combinación de datos del diseñador de Azure Machine Learning para combinar dos conjuntos de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: a19ae7de4355e5b4d2d8ce4379ef3359edd55f07
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566063"
---
# <a name="join-data"></a>Combinación de datos

En este artículo se explica cómo usar el componente **Combinación de datos** del diseñador de Azure Machine Learning para combinar dos conjuntos de datos mediante una operación de combinación de estilo de base de datos.  

## <a name="how-to-configure-join-data"></a>Configuración de Combinación de datos

Para realizar una combinación en dos conjuntos de datos, deben estar relacionados por una columna de clave. También se admiten las claves compuestas con varias columnas. 

1. Agregue los conjuntos de datos que quiere combinar y luego arrastre el componente **Combinación de datos** a la canalización. 

    Puede encontrar el componente en la categoría **Transformación de datos**, en **Manipulación**.

1. Conecte los conjuntos de datos al componente **Combinación de datos**. 
 
1. Seleccione **Iniciar el selector de columnas** para elegir las columnas de clave. Recuerde que debe elegir columnas tanto para la entrada izquierda como para la derecha.

    Para una sola clave:

    Seleccione una sola columna de clave para ambas entradas.
    
    Para una clave compuesta:

    Seleccione todas las columnas de clave de la entrada izquierda y la entrada derecha en el mismo orden. El componente **Combinación de datos** combina las tablas si todas las columnas de clave coinciden. Active la opción **Permitir duplicados y conversar el orden de las columnas en la selección** si el orden de las columnas no es igual que en la tabla original. 

    ![selector de columnas](media/module/join-data-column-selector.png)


1. Active la opción **Match case** (Coincidir mayúsculas y minúsculas) si quiere conservar la distinción entre mayúsculas y minúsculas en una combinación de columnas de texto. 
   
1. Use la lista desplegable **Tipo de combinación** para especificar cómo se deben combinar los conjuntos de datos.  
  
    * **Combinación interna**: una *combinación interna* es la operación de combinación más común. Devuelve las filas combinadas solo cuando coinciden los valores de las columnas de clave.  
  
    * **Combinación externa izquierda**: una *combinación externa izquierda* devuelve filas combinadas para todas las filas de la tabla izquierda. Si hay una fila en la tabla izquierda sin filas coincidentes en la tabla derecha, la fila devuelta contiene valores faltantes para todas las columnas que provienen de la tabla derecha. También puede especificar un valor de reemplazo para los valores faltantes.  
  
    * **Combinación externa completa**: una *combinación externa completa* devuelve todas las filas de la tabla izquierda (**table1**) y de la tabla derecha (**table2**).  
  
         Para cada una de las filas de cualquiera de las tablas sin filas coincidentes en la otra tabla, el resultado incluye una fila que contiene valores faltantes.  
  
    * **Semicombinación izquierda**: una *semicombinación izquierda* solo devuelve los valores de la tabla izquierda cuando coinciden los valores de las columnas de clave.  

1. Para la opción **Keep right key columns in joined table** (Mantener las columnas de clave derechas en la tabla combinada):

    * active esta opción para ver las claves de ambas tablas de entrada.
    * Anule la selección para devolver solo las columnas de clave de la entrada izquierda.

1. Envíe la canalización.

1. Para ver los resultados, haga clic con el botón derecho en **Combinación de datos** y seleccione **Visualize** (Visualizar).

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 