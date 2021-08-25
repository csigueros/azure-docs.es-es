---
title: Desduplicación de filas y búsqueda de valores NULL mediante fragmentos de código de flujo de datos
description: Aprenda a desduplicar filas y buscar valores NULL mediante fragmentos de código de flujos de datos.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 116b1e2afded3ceee0d249e326e721ca420dcf17
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638571"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Desduplicación de filas y búsqueda de valores NULL mediante fragmentos de código de flujo de datos

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Si utiliza fragmentos de código en los flujos de datos de asignación, puede realizar de manera muy sencilla tareas comunes, como la desduplicación de datos y el filtrado de valores NULL. En este artículo se explica cómo agregar fácilmente esas funciones a las canalizaciones mediante fragmentos de scripts de flujo de datos.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Crear una canalización

1. Seleccione **Nueva canalización**.

1. Agregue una actividad de flujo de datos.

1. Seleccione la pestaña **Configuración de origen**, agregue una transformación de origen y conéctela a uno de los conjuntos de datos.

    ![Captura de pantalla del panel "Configuración de origen" para agregar un tipo de origen.](media/data-flow/snippet-adf-2.png)

    Los fragmentos de código de desduplicación y comprobación de valores NULL emplean patrones genéricos que aprovechan las ventajas del desplazamiento del esquema de flujo de datos. Los fragmentos de código funcionan con cualquier esquema del conjunto de datos o con conjuntos de datos que no tienen ningún esquema predefinido.

1. En la sección "Fila DISTINCT que utiliza todas las columnas" de [Script de flujo de datos (DFS)](./data-flow-script.md#distinct-row-using-all-columns), copie el fragmento de código de DistinctRows.

1. [Vaya a la página de documentación Script de flujo de datos y copie el fragmento de código de filas distintas](./data-flow-script.md#distinct-row-using-all-columns).

    ![Captura de pantalla de un fragmento de código de origen.](media/data-flow/snippet-adf-3.png)

1. En el script, después de la definición de `source1`, presione Entrar y, luego, pegue el fragmento de código.

1. Realice cualquiera de las siguientes acciones:

   * Conecte este fragmento de código pegado a la transformación de origen que creó anteriormente en el gráfico escribiendo **source1** delante.

   * También puede conectar la transformación nueva en el diseñador seleccionando la secuencia entrante del nuevo nodo de transformación en el gráfico.

     ![Captura de pantalla del panel "Conditional split settings" (Configuración de división condicional).](media/data-flow/snippet-adf-4.png)

   Ahora, el flujo de datos quitará las filas duplicadas del origen mediante la transformación de agregados, que agrupa todas las filas usando un hash general en todos los valores de columna.
    
1. Agregue un fragmento de código para dividir los datos en un flujo que contenga filas con valores NULL y otro flujo sin valores NULL. Para ello:

1. [Vuelva a la biblioteca de fragmentos de código y esta vez copie el código para las comprobaciones de valores NULL](./data-flow-script.md#check-for-nulls-in-all-columns).

   b. En el diseñador de flujos de datos, seleccione **Script** de nuevo y, luego, pegue este nuevo código de transformación en la parte inferior. Esta acción conecta el script a la transformación anterior al colocar el nombre de esa transformación delante del fragmento de código pegado.

   El gráfico de flujo de datos debería tener ahora un aspecto similar al siguiente:

    ![Captura de pantalla del gráfico de flujo de datos.](media/data-flow/snippet-adf-1.png)

  Ahora ha creado un flujo de datos funcional con desduplicación y comprobación de valores NULL genéricos, para lo cual ha tomado fragmentos de código existentes de la biblioteca de scripts de flujo de datos y los ha agregado al diseño existente.

## <a name="next-steps"></a>Pasos siguientes

* Compile el resto de la lógica del flujo de datos mediante [transformaciones](concepts-data-flow-overview.md) de flujos de datos de asignación.