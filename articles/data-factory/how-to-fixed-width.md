---
title: Procesamiento de archivos de texto de longitud fija con flujos de datos de asignación en Azure Data Factory
description: Aprenda a procesar archivos de texto de longitud fija en Azure Data Factory mediante flujos de datos de asignación.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 58bd77eb815a510328c49ca7c614b0bc680a4b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571435"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Procesamiento de archivos de texto de longitud fija mediante flujos de datos de asignación de Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mediante el uso de flujos de datos de asignación en Microsoft Azure Data Factory, puede transformar datos de archivos de texto de ancho fijo. En la tarea siguiente, definiremos un conjunto de datos para un archivo de texto sin un delimitador y, a continuación, configuraremos divisiones de subcadenas basadas en la posición ordinal.

## <a name="create-a-pipeline"></a>Crear una canalización

1. Seleccione **+Nueva canalización** para iniciar una nueva canalización.

2. Agregue una actividad de flujo de datos que se usará para el procesamiento de archivos de ancho fijo:

    :::image type="content" source="media/data-flow/fwpipe.png" alt-text="Canalización de ancho fijo":::

3. En la actividad de flujo de datos, seleccione **New Mapping Data Flow** (Nuevo flujo de datos de asignación).

4. Agregue una transformación de origen, una columna derivada, una de selección y una de receptor:

    :::image type="content" source="media/data-flow/fw2.png" alt-text="Flujo de datos de ancho fijo":::

5. Configure la transformación de origen para que use un nuevo conjunto de datos, que será del tipo texto delimitado.

6. No establezca ningún delimitador o encabezado de columna.

   Ahora vamos a establecer simplemente puntos iniciales y longitudes de campo para el contenido de este archivo:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. En la pestaña **Proyección** de la transformación de origen, debería ver una columna de cadena denominada *Column_1*.

8. En la columna derivada, cree una nueva columna.

9. Asignaremos a las columnas nombres sencillos como *col1*.

10. A continuación, en el generador de expresiones, escriba lo siguiente:

    `substring(Column_1,1,4)`

    :::image type="content" source="media/data-flow/fwderivedcol1.png" alt-text="columna derivada":::

11. Repita el paso 10 para todas las columnas que necesite analizar.

12. Seleccione la pestaña **Inspeccionar** para ver las nuevas columnas que se van a generar:

    :::image type="content" source="media/data-flow/fwinspect.png" alt-text="inspeccionar":::

13. Use la transformación de selección para quitar todas las columnas que no necesite para la transformación:

    :::image type="content" source="media/data-flow/fwselect.png" alt-text="seleccionar transformación":::

14. Use la transformación de receptor para generar los datos en una carpeta:

    :::image type="content" source="media/data-flow/fwsink.png" alt-text="receptor de ancho fijo":::

    Este es el aspecto de la salida:

    :::image type="content" source="media/data-flow/fxdoutput.png" alt-text="salida de ancho fijo":::

  Los datos de ancho fijo se dividen ahora con cuatro caracteres cada uno y se asignan a Col1, Col2, Col3, Col4, etc. Según el ejemplo anterior, los datos se dividen en cuatro columnas.

## <a name="next-steps"></a>Pasos siguientes

* Compile el resto de la lógica del flujo de datos mediante [transformaciones](concepts-data-flow-overview.md) de flujos de datos de asignación.
