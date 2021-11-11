---
title: 'Supresión de filas duplicadas: referencia de componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Supresión de filas duplicadas de Azure Machine Learning para eliminar posibles duplicados de un conjunto de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 2fa7d5f3cf7d80531c03ae4ab7ef019c90cf8c71
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566044"
---
# <a name="remove-duplicate-rows-component"></a>Componente Supresión de filas duplicadas

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para eliminar posibles duplicados de un conjunto de datos.

Por ejemplo, supongamos tiene unos datos parecidos a los siguientes y que representan varios registros de pacientes. 

| PatientID | Initials| Sexo|Age|Admitido|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este ejemplo tiene varias columnas con datos potencialmente duplicados. Que sean duplicados reales depende de su conocimiento de los datos. 

+ Por ejemplo, es posible que sepa que muchos pacientes se llaman igual. No eliminará los duplicados utilizando las columnas de nombre, solo la columna **Identificador**. De este modo, solo se filtran las filas con valores de identificador duplicados, independientemente de si los pacientes se llaman igual o no.

+ Como alternativa, puede que decida permitir duplicados en el campo Identificador y utilizar otra combinación de archivos para buscar registros únicos, como, por ejemplo, nombre, apellido, edad y sexo.  

Para establecer los criterios para determinar si una fila está duplicada o no, especifica una sola columna o un conjunto de columnas que se usarán como **claves**. Dos filas se consideran duplicadas solo cuando los valores de **todas** las columnas de clave son iguales. Si una fila no tiene un valor para **claves**, no se considerará una fila duplicada. Por ejemplo, si Sexo y Edad se establecen como claves en la tabla anterior, las filas 6 y 7 no son duplicadas puesto que no tienen ningún valor en Edad.

Al ejecutar el componente, este crea un conjunto de datos candidato y devuelve un conjunto de filas que no tienen duplicados en el conjunto de columnas especificado.

> [!IMPORTANT]
> El conjunto de datos de origen no se modifica; este componente crea un nuevo conjunto de datos que se filtra para excluir duplicados en función de los criterios que especifique.

## <a name="how-to-use-remove-duplicate-rows"></a>Cómo usar Supresión de filas duplicadas

1. Agregue el componente a la canalización. Puede encontrar el componente **Supresión de filas duplicadas** en **Transformación de datos**, **Manipulación**.  

2. Conecte el conjunto de datos para el que desea comprobar las filas duplicadas.

3. En el panel **Propiedades**, en **Expresión de filtro de selección de columnas de clave**, haga clic en **Iniciar selector de columnas** para elegir las columnas que usará para identificar los duplicados.

    En este contexto, **Clave** no tiene el sentido de identificador único. Todas las columnas que seleccione mediante el Selector de columnas se designan como **columnas de clave**. Todas las columnas sin seleccionar se consideran columnas no de clave. La combinación de columnas que seleccione como claves determina la unicidad de los registros. (Considérelo como una instrucción SQL que usa varias combinaciones de igualdades.)

    Ejemplos:

    + "Quiero asegurarme de que los identificadores son únicos": Elija solo la columna de identificador.
    + "Quiero asegurarme de que la combinación de nombre, apellido e identificador es única": Seleccione las tres columnas.

4. Use la casilla **Conservar primera fila duplicada** para indicar qué fila debe devolverse cuando se encuentran duplicados:

    + Si se selecciona, se devuelve la primera fila y se descartan las demás. 
    + Si desactiva esta opción, se mantiene la última fila duplicada en los resultados y se descartan las demás. 

5. Envíe la canalización.

6. Para revisar los resultados, haga clic con el botón derecho en el componente y seleccione **Visualizar**. 

> [!TIP]
> Si los resultados son difíciles de entender, o si quiere dejar de tener en cuenta algunas columnas, puede quitarlas mediante el componente [Seleccionar columnas de conjunto de datos](./select-columns-in-dataset.md).

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 