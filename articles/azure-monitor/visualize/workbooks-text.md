---
title: Parámetros de texto de los libros de Azure Monitor
description: Simplifique la creación de informes complejos con libros parametrizados predefinidos y personalizados. Aprenda más sobre los parámetros de texto de libros.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: e3beedff4b9d65f5f0b69b5c60bd67d4b134d096
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287579"
---
# <a name="workbook-text-parameters"></a>Parámetros de texto de libros

Los parámetros TextBox proporcionan una manera sencilla de recopilar entrada de texto de los usuarios de libros. Se usan cuando no es práctico usar una lista desplegable para recopilar la entrada (por ejemplo, un umbral arbitrario o filtros genéricos). Los libros permiten a los autores obtener el valor predeterminado del cuadro de texto de una consulta, lo que hace posible escenarios interesantes, como establecer el umbral predeterminado en función del P95 de la métrica.

Un uso común de los cuadros de texto es como variables internas que otros controles del libro utilizan. Para ello, se usa una consulta de valores predeterminados y se hace invisible el control de entrada en modo de lectura. Por ejemplo, puede que a un usuario le interese que un umbral venga de una fórmula (no de un usuario) y, luego, usar el umbral en las sucesivas consultas.

## <a name="creating-a-text-parameter"></a>Creación de un parámetro de texto
1. Comience con un libro vacío en modo de edición.
2. Elija _Agregar parámetros_ desde los vínculos del libro.
3. Seleccione el botón azul _Agregar parámetro_.
4. En el panel de nuevo parámetro que aparece, escriba:
    1. Nombre de parámetro: `SlowRequestThreshold`
    2. Tipo de parámetro: `Text`
    3. Obligatorio: `checked`
    4. Obtener datos de: `None`
5. Elija "Guardar" en la barra de herramientas para crear el parámetro.

    :::image type="content" source="./media/workbooks-text/text-create.png" alt-text="Captura de pantalla que muestra la creación de un parámetro de texto.":::

Este es el aspecto que tendrá el libro en modo de lectura.

:::image type="content" source="./media/workbooks-text/text-readmode.png" alt-text="Captura de pantalla que muestra un parámetro de texto en modo de lectura." border="false":::

## <a name="parameter-field-style"></a>Estilo del campo de parámetros
El parámetro de texto admite el siguiente estilo de campo:

- Estándar: campo de texto de una sola línea.

     :::image type="content" source="./media/workbooks-text/standard-text.png" alt-text="Captura de pantalla que muestra un campo de texto estándar.":::

- Contraseña: campo de contraseña de una sola línea. El valor de la contraseña solo se oculta en la interfaz de usuario cuando el usuario escribe. El valor sigue siendo totalmente accesible como un valor de parámetro cuando se hace referencia a él y se almacena sin cifrar cuando se guarda el libro.

     :::image type="content" source="./media/workbooks-text/password-text.png" alt-text="Captura de pantalla que muestra un campo de contraseña.":::

- Multilínea: campo de texto multilínea compatible con IntelliSense enriquecido y coloración de sintaxis para los siguientes lenguajes:
    - Texto
    - Markdown
    - JSON
    - SQL
    - TypeScript
    - KQL
    - TOML

    El usuario también puede especificar el alto del editor de varias líneas.

     :::image type="content" source="./media/workbooks-text/kql-text.png" alt-text="Captura de pantalla que muestra un campo de texto de varias líneas.":::

## <a name="referencing-a-text-parameter"></a>Referencia a un parámetro de texto
1. Para agregar un control de consulta al libro, seleccione el vínculo azul `Add query` y seleccione un recurso de Application Insights.
2. En el cuadro KQL, agregue este fragmento de código:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Con el uso del parámetro de texto con un valor de 500 junto con el control de consulta, la siguiente consulta se ejecutará de forma eficaz:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Ejecución de la consulta para ver los resultados

    :::image type="content" source="./media/workbooks-text/text-reference.png" alt-text="Captura de pantalla que muestra un parámetro de texto al que se hace referencia en KQL.":::

> [!NOTE]
> En el ejemplo anterior, `{SlowRequestThreshold}` representa un valor entero. Si estaba consultando una cadena como `{ComputerName}`, tendría que modificar la consulta de Kusto y agregar comillas, `"{ComputerName}"`, para que el campo del parámetro aceptara una entrada sin comillas.

## <a name="setting-default-values-using-queries"></a>Establecimiento de los valores predeterminados mediante consultas
1. Comience con un libro vacío en modo de edición.
2. Elija _Agregar parámetros_ desde los vínculos del libro.
3. Seleccione el botón azul _Agregar parámetro_.
4. En el panel de nuevo parámetro que aparece, escriba:
    1. Nombre de parámetro: `SlowRequestThreshold`
    2. Tipo de parámetro: `Text`
    3. Obligatorio: `checked`
    4. Obtener datos de: `Query`
5. En el cuadro KQL, agregue este fragmento de código:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Esta consulta establece el valor predeterminado del cuadro de texto en la duración del percentil 95 para todas las solicitudes de la aplicación.
6. Ejecución de la consulta para ver el resultado
7. Elija "Guardar" en la barra de herramientas para crear el parámetro.

    :::image type="content" source="./media/workbooks-text/text-default-value.png" alt-text="Captura de pantalla que muestra un parámetro de texto con el valor predeterminado desde KQL.":::

> [!NOTE]
> Aunque en este ejemplo se consultan los datos de Application Insights, el enfoque se puede usar con cualquier origen de datos basado en registros: Log Analytics, Azure Resource Graph, etc.

## <a name="adding-validations"></a>Adición de validaciones 

Para los parámetros de texto estándar y de contraseña, el usuario puede agregar reglas de validación que se aplican al campo de texto. Agregue una expresión regular válida con un mensaje de error. Si se establece el mensaje, se muestra como un error cuando el campo no es válido.

Si el operador de coincidencia está seleccionado, el campo es válido si el valor coincide con la expresión regular y si el operador de coincidencia no está seleccionado, el campo es válido si no coincide con la expresión regular.

:::image type="content" source="./media/workbooks-text/validation-settings.png" alt-text="Captura de pantalla de la configuración de la validación del texto.":::

## <a name="format-json-data"></a>Formato de datos JSON 

Si se selecciona JSON como lenguaje para el campo de texto de varias líneas, el campo tendrá un botón que dará formato a los datos JSON del campo. El usuario también puede usar el acceso directo `(ctrl + \)` para dar formato a los datos JSON.

Si los datos proceden de una consulta, el usuario puede seleccionar la opción para dar formato previo a los datos JSON devueltos por la consulta.

:::image type="content" source="./media/workbooks-text/pre-format-json-data.png" alt-text="Captura de pantalla de los datos JSON con formato previo.":::

## <a name="next-steps"></a>Pasos siguientes

* [Comience](./workbooks-overview.md#visualizations) a aprender más sobre las muchas opciones de visualizaciones enriquecidas de los libros.
* [Controle](./workbooks-access-control.md) y comparta el acceso a los recursos del libro.