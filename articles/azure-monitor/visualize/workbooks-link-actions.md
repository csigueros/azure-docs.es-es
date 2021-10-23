---
title: Acciones de vínculo de Libros de Azure Monitor
description: Cómo usar las acciones de vínculo en los libros de Azure Monitor
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: dd8d4b1d7aefe070f72c4087162527dadd69f09e
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129251"
---
# <a name="link-actions"></a>Acciones de vínculo

Se puede acceder a las acciones de vínculo a través de los pasos de vinculación del libro o a través de la configuración de columna de [cuadrículas](../visualize/workbooks-grid-visualizations.md), [títulos](../visualize/workbooks-tile-visualizations.md) o [grafos](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Acciones de vínculo generales

| Acción de vínculo | Acción al hacer clic |
|:------------- |:-------------|
| `Generic Details` | Muestra los valores de fila de una vista de contexto de cuadrícula de propiedades. |
| `Cell Details` | Muestra el valor de celda de una vista de contexto de cuadrícula de propiedades. Resulta útil cuando la celda contiene un tipo dinámico con información (por ejemplo, JSON con propiedades de solicitud, como ubicación, instancia de rol, etc.). |
| `Url` | Se espera que el valor de la celda sea una dirección URL HTTP válida y que la celda funcione como un vínculo que abrirá dicha dirección URL en una nueva pestaña.|

## <a name="application-insights"></a>Application Insights

| Acción de vínculo | Acción al hacer clic |
|:------------- |:-------------|
| `Custom Event Details` | Abre los detalles de búsqueda de Application Insights con el id. de evento personalizado (`itemId`) en la celda. |
| `* Details` | Similar a los detalles de eventos personalizados, exceptuando las dependencias, las excepciones, las vistas de página, las solicitudes y los seguimientos. |
| `Custom Event User Flows` | Abre la experiencia Flujos de usuario de Application Insights ubicada dinámicamente en el nombre del evento personalizado de la celda. |
| `* User Flows` | Es similar a Flujos de usuario de eventos personalizados, salvo para las excepciones, las vistas de página y las solicitudes. |
| `User Timeline` | Abre la escala de tiempo del usuario con el id. de usuario (`user_Id`) de la celda. |
| `Session Timeline` | Abre la experiencia de búsqueda de Application Insights para el valor de la celda (por ejemplo, busca el texto "abc" cuando abc es el valor de la celda). |

`*` denota un carácter comodín para la tabla anterior

## <a name="azure-resource"></a>Recurso de Azure

| Acción de vínculo | Acción al hacer clic |
|:------------- |:-------------|
| `ARM Deployment` | Implementa una plantilla de Azure Resource Manager.  Cuando se selecciona este elemento, se muestran campos adicionales para el autor pueda configurar qué plantilla de Azure Resource Manager se va a abrir, los parámetros de la plantilla, etc. [Consulte la configuración de vínculos de implementación de Azure Resource Manager](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Crea una regla de alerta para un recurso.  |
| `Custom View` | Abre una vista personalizada. Cuando se selecciona este elemento, se muestran campos adicionales para permitir que el autor configure la extensión de vista, el nombre de la vista y los parámetros necesarios para abrir la vista. [Consulte la vista personalizada](#custom-view-link-settings). |
| `Metrics` | Abre una vista de métricas.  |
| `Resource overview` | Abre la vista del recurso en el portal en función del valor del id. del recurso de la celda. Opcionalmente, el autor también puede establecer un valor de `submenu` que abrirá un elemento de menú específico en la vista de recursos. |
| `Workbook (template)` | Abre una plantilla de libro.  Cuando se selecciona este elemento, se muestran campos adicionales para permitir que el autor configure la plantilla que se va a abrir, etc.  |

## <a name="link-settings"></a>Configuración del vínculo

Cuando se usa el representador de vínculos, están disponibles las opciones siguientes:

![Captura de pantalla de la configuración del vínculo](./media/workbooks-link-actions/link-settings.png)

| Configuración | Explicación |
|:------------- |:-------------|
| `View to open` | Permite que el autor seleccione una de las acciones mencionadas anteriormente. |
| `Menu item` | Si está seleccionada la opción "Información general de recursos", este es el elemento de menú que se abrirá en la información general del recurso. Se puede usar para abrir alertas o registros de actividad en lugar de la "información general" del recurso. Los valores de los elementos de menú son diferentes para cada `Resourcetype`de Azure.|
| `Link label` | Si se especifica, este valor se mostrará en la columna Cuadrícula. Si no se especifica este valor, se mostrará el valor de la celda. Si quiere que se muestre otro valor, como un mapa térmico o un icono, no use el representador `Link`; en su lugar, use el representador adecuado y seleccione la opción `Make this item a link`. |
| `Open link in Context Blade` | Si se especifica, el vínculo se abrirá como una vista emergente "en contexto" en el lado derecho de la ventana, en lugar de abrirse como una vista completa. |

Cuando se usa la opción `Make this item a link`, están disponibles las opciones siguientes:

| Configuración | Explicación |
|:------------- |:-------------|
| `Link value comes from` | Cuando se muestra una celda como un representador con un vínculo, este campo especifica de dónde procede el valor de "link" que se va a usar en el vínculo, lo que permite al autor seleccionarlo en una lista desplegable de las demás columnas de la cuadrícula. Por ejemplo, la celda puede ser un valor de mapa térmico, pero se quiere que el vínculo abra la Información general de recursos correspondiente al id. de recurso de la fila. En ese caso, debe establecer el valor del vínculo para que provenga del campo `Resource Id`.
| `View to open` | Igual que el anterior. |
| `Menu item` | Igual que el anterior. |
| `Open link in Context Blade` | Igual que el anterior. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Configuración de vínculos de implementación de Azure Resource Manager

Si el tipo de vínculo seleccionado es `ARM Deployment`, el autor debe especificar las opciones adicionales para abrir una implementación de Azure Resource Manager. Hay dos pestañas principales para realizar los ajustes.

### <a name="template-settings"></a>Configuración de plantillas

En esta sección se define la procedencia de la plantilla y los parámetros que se usarán para ejecutar la implementación de Azure Resource Manager.

| Source | Explicación |
|:------------- |:-------------|
|`Resource group id comes from` | Id. de recurso se usa para administrar los recursos implementados. La suscripción se usa para administrar los recursos y costos implementados. Los grupos de recursos se usan como carpetas para organizar y administrar todos los recursos. Si no se especifica este valor, la implementación devolverá un error. Seleccione entre los valores `Cell`, `Column`, `Static Value`o `Parameter` en [orígenes de vínculo](#link-sources).|
|`ARM template URI from` | URI de la plantilla de Azure Resource Manager. Los usuarios que van a implementar la plantilla deben tener acceso al URI de la plantilla. Seleccione entre los valores `Cell`, `Column`, `Parameter`o `Static Value` en [orígenes de vínculo](#link-sources). Para comenzar, eche un vistazo a las [plantillas de inicio rápido](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | En esta sección se definen los parámetros de plantilla que se usan para el URI de plantilla definido anteriormente. Estos parámetros se usarán para implementar la plantilla en la página de ejecución. La cuadrícula contiene un botón de la barra de herramientas Expandir para ayudarle a rellenar los parámetros con los nombres definidos en el URI de la plantilla y establecerlos en valores vacíos estáticos. Esta opción solo se puede usar cuando no hay ningún parámetro en la cuadrícula y ya se ha establecido el URI de la plantilla. La sección inferior es una vista previa de cuál será el aspecto de la salida del parámetro. Seleccione Actualizar para actualizar la vista previa con los cambios actuales. Los parámetros normalmente son valores, mientras que las referencias podrían apuntar a secretos de almacén de claves a los que el usuario tiene acceso. <br/><br/> **Limitación de la hoja del Visor de plantillas**: no representa correctamente los parámetros de referencia y se mostrarán como NULL/value. Por lo tanto, los usuarios no podrán implementar correctamente los parámetros de referencia desde la pestaña Visor de plantillas.|

![Captura de pantalla de la configuración de la plantilla de Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Configuración de la experiencia de usuario

En esta sección se configura lo que verán los usuarios antes de ejecutar la implementación de Azure Resource Manager.

| Source | Explicación |
|:------------- |:-------------|
|`Title from` | Título usado en la vista de ejecución. Seleccione entre los valores `Cell`, `Column`, `Parameter`o `Static Value` en [orígenes de vínculo](#link-sources).|
|`Description from` | Este es el texto Markdown que se usa para proporcionar una descripción útil a los usuarios cuando quieren implementar la plantilla. Seleccione entre los valores `Cell`, `Column`, `Parameter`o `Static Value` en [orígenes de vínculo](#link-sources). <br/><br/> **NOTA:** Si se selecciona `Static Value`, aparecerá un cuadro de texto de varias líneas. En este cuadro de texto, puede resolver los parámetros mediante `{paramName}`. También puede tratar las columnas como parámetros si coloca `_column` después del nombre de la columna de la siguiente manera: `{columnName_column}`. En la imagen de ejemplo siguiente, podemos hacer referencia a la columna `VMName` al escribir `{VMName_column}`. El valor que aparece después de los dos puntos es el [formateador de parámetros](../visualize/workbooks-parameters.md#parameter-options), en este caso es `value`.|
|`Run button text from` | Etiqueta usada en el botón Ejecutar para implementar la plantilla de Azure Resource Manager. Este es el elemento que los usuarios seleccionarán para empezar a implementar la plantilla de Azure Resource Manager.|

![Captura de pantalla de la configuración de experiencia de usuario de Azure Resource Manager](./media/workbooks-link-actions/ux-settings.png)

Una vez establecidas estas configuraciones, cuando los usuarios seleccionen el vínculo, se abrirá la vista con la experiencia de usuario que se describe en las opciones de experiencia de usuario. Si el usuario selecciona `Run button text from`, se implementará una plantilla de Azure Resource Manager con los valores de la [configuración de plantilla](#template-settings). Ver plantilla abrirá la pestaña Visor de plantillas para que el usuario examine la plantilla y sus parámetros antes de la implementación.

![Captura de pantalla de la vista de Azure Resource Manager ejecutada](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Configuración del vínculo de vista personalizada

Úselo para abrir vistas personalizadas en Azure Portal. Compruebe la configuración y las opciones. Los valores incorrectos producirán errores en el portal o no podrán abrir correctamente las vistas. Existen dos maneras de configurar las opciones, a través de `Form` o `URL`.

> [!NOTE]
> Las vistas con un menú no se pueden abrir en una pestaña de contexto. Si una vista con menú está configurada para abrirse en una pestaña de contexto, no se mostrará ninguna pestaña de contexto cuando se seleccione el vínculo.

### <a name="form"></a>Form

| Source | Explicación |
|:------------- |:-------------|
|`Extension name` | Nombre de la extensión que hospeda el nombre de la vista.|
|`View name` | Nombre de la vista que se va a abrir.|

#### <a name="view-inputs"></a>Visualización de entradas

Hay dos tipos de entradas, cuadrículas y JSON. Use `Grid` para las entradas de pestañas sencillas de tipo clave y valor, o seleccione `JSON` para especificar una entrada JSON anidada.

- Cuadrícula
    - `Parameter Name`: nombre del parámetro de entrada de la vista.
    - `Parameter Comes From`: ubicación de la que debe provenir el valor del parámetro de vista. Seleccione entre los valores `Cell`, `Column`, `Parameter`o `Static Value` en [orígenes de vínculo](#link-sources).
    > [!NOTE]
    > Si se selecciona `Static Value`, los parámetros se pueden resolver mediante un vínculo entre corchetes `{paramName}` en el cuadro de texto. Las columnas se pueden tratar como columnas de parámetro al colocar `_column` después del nombre de la columna de la siguiente manera: `{columnName_column}`.

    - `Parameter Value`: según el valor de `Parameter Comes From`, se trata de una lista desplegable de parámetros, columnas o valores estáticos disponibles.

    ![Captura de pantalla de Editar configuración de columna que muestra los valores de vista personalizada del formulario.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Especifique la entrada de la pestaña en formato JSON en el editor. Al igual que en el modo `Grid`, se puede hacer referencia a los parámetros y columnas mediante `{paramName}` para los parámetros y mediante `{columnName_column}` para las columnas. Al seleccionar `Show JSON Sample`, se mostrará la salida esperada de todos los parámetros y columnas resueltos para la entrada de la vista.

    ![Captura de pantalla que muestra la configuración de vista personalizada abierta con las entradas de vista en formato JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>Dirección URL

Pegue la dirección URL del portal que contiene la extensión, el nombre de la vista y todas las entradas necesarias para abrir la vista. Después de seleccionar `Initialize Settings`, se rellenará el `Form` para que el autor agregue, modifique o quite cualquiera de las entradas de la vista.

![Captura de pantalla que muestra la configuración de columna con las opciones de vista personalizada de URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Configuración del vínculo de libro (plantilla)

Si el tipo de vínculo seleccionado es `Workbook (Template)`, el autor debe especificar opciones adicionales para abrir la plantilla de libro correcta. La siguiente configuración tiene las opciones para la manera en que la cuadrícula buscará el valor adecuado para cada una de las configuraciones.

| Configuración | Explicación |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Este es el id. de recurso correspondiente al recurso de Azure que es "propietario" del libro. Normalmente, se trata de un recurso de Application Insights o un área de trabajo de Log Analytics. Dentro de Azure Monitor, también puede ser literalmente la cadena `"Azure Monitor"`. Cuando se guarda el libro, este es el elemento al que se vinculará el libro. |
| `Workbook resources` | Matriz de id. de recursos de Azure que especifica el recurso predeterminado que se usa en el libro. Por ejemplo, si la plantilla que abrirá muestra las métricas de máquina virtual, los valores de esta matriz serían los id. de recursos de máquina virtual.  Muchas veces, el propietario y los recursos tienen establecida la misma configuración. |
| `Template Id` | Especifique el id. de la plantilla que se va a abrir. Si se trata de una plantilla de la comunidad de la galería (el caso más común), coloque antes de la ruta de acceso a la plantilla la palabra `Community-`, como en `Community-Workbooks/Performance/Apdex` para la plantilla `Workbooks/Performance/Apdex`. Si se trata de un vínculo a un libro o plantilla guardados, es el id. de recurso de Azure completo de ese elemento. |
| `Workbook Type` | Especifique el tipo de plantilla de libro que se va a abrir. En los casos más comunes, use la opción `Default` o `Workbook` para usar el valor en el libro actual. |
| `Gallery Type` | Esto especifica el tipo de galería que se mostrará en la vista "Galería" de la plantilla que se abrirá. En los casos más comunes, use la opción `Default` o `Workbook` para usar el valor en el libro actual. |
|`Location comes from` | El campo de ubicación debe especificarse si se abrirá un recurso de libro específico. Si no se especifica una ubicación, la búsqueda del contenido del libro será mucho más lenta. Si conoce la ubicación, especifíquela. Si no conoce la ubicación o abrirá una plantilla que no tiene una ubicación específica, deje este campo como "valor predeterminado".|
|`Pass specific parameters to template` | Seleccione esta opción para pasar parámetros específicos a la plantilla. Si está seleccionada, solo se pasan los parámetros especificados a la plantilla. De lo contrario, todos los parámetros del libro actual se pasan a la plantilla y, en ese caso, el parámetro *names* debe ser el mismo en ambos libros para que este valor de parámetro funcione.|
|`Workbook Template Parameters` | En esta sección se definen los parámetros que se pasan a la plantilla de destino. El nombre debe coincidir con el nombre del parámetro en la plantilla de destino. Seleccione alguno de los siguientes valores: `Cell`, `Column`, `Parameter`y `Static Value`. El nombre y el valor no deben estar vacíos para pasar el parámetro a la plantilla de destino.|

Para cada una de las opciones anteriores, el autor debe elegir de dónde procederá el valor en el libro vinculado. Consulte [orígenes de vínculo](#link-sources).

Cuando se abre el vínculo del libro, se pasan todos los valores configurados en las opciones anteriores en la nueva vista de libro.

![Captura de pantalla de la configuración de vínculos del libro](./media/workbooks-link-actions/workbook-link-settings.png)

![Captura de pantalla de la configuración de parámetros de la plantilla del libro](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Orígenes de vínculo

| Source | Explicación |
|:------------- |:-------------|
| `Cell` | Se usará el valor de esa celda de la cuadrícula como valor del vínculo. |
| `Column` | Cuando esté seleccionada esta opción, se mostrará otro campo para que el autor seleccione otra columna en la cuadrícula.  El valor de esa columna para la fila se usará en el valor del vínculo. Normalmente se usa para permitir que cada fila de una cuadrícula abra una plantilla diferente al establecer el campo `Template Id` en `column`, o para abrir la misma plantilla de libro para distintos recursos si el campo `Workbook resources` está establecido en una columna que contenga un id. de recurso de Azure. |
| `Parameter` | Cuando esté seleccionada esta opción, se mostrará otro campo para que el autor seleccione un parámetro. El valor de ese parámetro se usará como valor cuando se haga clic en el vínculo. |
| `Static value` | Cuando esté seleccionada esta opción, se mostrará otro campo para permitir que autor escriba un valor estático que se usará en el libro vinculado. Normalmente se usa cuando todas las filas de la cuadrícula usarán el mismo valor para un campo. |
| `Step` | Use el valor establecido en el paso actual del libro. Esta opción suele usarse en los pasos de consulta y métricas para establecer los recursos de libro del libro vinculado en los valores que se usan *en el paso de consulta/métricas*, no en el libro actual. |
| `Workbook` | Use el valor establecido en el libro actual. |
| `Default` | Use el valor predeterminado que se usaría si no se especificara ningún valor. Esta opción es común para el tipo Gallery, en el que la galería predeterminada se establece según el tipo del recurso propietario. |

## <a name="next-steps"></a>Pasos siguientes

- [Controle](../visualize/workbooks-access-control.md) y comparta el acceso a los recursos del libro.
- Aprenda a usar [grupos en libros](../visualize/workbooks-groups.md).