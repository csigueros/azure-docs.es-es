---
title: Creación de informes interactivos de VM Insights con libros
description: Simplifique la creación de informes complejos con libros parametrizados predefinidos y personalizados de VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a448c5b7ecb69c79c05c28c03593058e4c5ab87a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112541003"
---
# <a name="create-interactive-reports-vm-insights-with-workbooks"></a>Creación de informes interactivos de VM Insights con libros

Los libros combinan texto, [consultas de registros](/azure/data-explorer/kusto/query/), métricas y parámetros en informes interactivos avanzados. Otros miembros del equipo con acceso a los mismos recursos de Azure pueden editar los libros.

Los libros son útiles en escenarios como los siguientes:

* Explorar la utilización de la máquina virtual cuando no se conocen de antemano las métricas de interés: utilización de CPU, espacio en disco, memoria, dependencias de red, etc. A diferencia de otras herramientas de análisis de uso, los libros le permiten combinar varios tipos de visualizaciones y análisis, lo que los hace idóneos para este tipo de exploración de forma libre.
* Explicar a su equipo cómo funciona una máquina virtual aprovisionada recientemente, mostrando las métricas para contadores clave y otros eventos de registro.
* Compartir los resultados de un experimento de cambio de tamaño de la máquina virtual con otros miembros del equipo. Puede explicar los objetivos del experimento con texto y luego mostrar cada métrica de utilización y las consultas de análisis que se usan para evaluar el experimento, junto con indicadores claros sobre si cada métrica está por encima o por debajo del objetivo.
* Notificar el impacto de una interrupción del servicio en la utilización de la máquina virtual, con la combinación de datos, explicación del texto y análisis de los pasos siguientes para evitar más interrupciones en el futuro.

## <a name="vm-insights-workbooks"></a>Libro de Información de máquinas virtuales
Información de máquinas virtuales incluye los siguientes libros. Puede usar estos libros o utilizarlos como punto de partida para crear libros personalizados a fin de abordar requisitos concretos.

### <a name="single-virtual-machine"></a>una sola máquina virtual

| Libro | Descripción |
|----------|-------------|
| Rendimiento | Proporciona una versión personalizable de la vista Rendimiento que aprovecha todos los contadores de rendimiento de Log Analytics que se han habilitado. | 
| Conexiones | Las conexiones ofrecen una visión detallada de las conexiones entrantes y salientes de las máquinas virtuales. | 

### <a name="multiple-virtual-machines"></a>Múltiples máquinas virtuales

| Libro | Descripción |
|----------|-------------|
| Rendimiento | Proporciona una versión personalizable de la vista Lista de N principales y gráficos en un único libro que aprovecha todos los contadores de rendimiento de Log Analytics que se han habilitado.|
| Contadores de rendimiento | Una vista de Gráfico de N principales entre un amplio conjunto de contadores de rendimiento. |
| Conexiones | Las conexiones ofrecen una visión detallada de las conexiones entrantes y salientes de las máquinas virtuales supervisadas. |
| Puertos activos | Proporciona una lista de los procesos que se han enlazado a los puertos en las máquinas virtuales supervisadas y su actividad en el periodo de tiempo seleccionado. |
| Open Ports (Abrir puertos) | Proporciona el número de puertos abiertos en las máquinas virtuales supervisadas, así como los detalles de estos puertos. |
| Conexiones con errores | Muestra el número de conexiones con errores en las máquinas virtuales supervisadas, la tendencia de errores y si el porcentaje de errores aumenta con el tiempo. |
| Seguridad y auditoría | Un análisis del tráfico TCP/IP que informa sobre las conexiones generales, las conexiones malintencionadas, en las que se encuentran los puntos de conexión IP de forma global.  Para habilitar todas las características, deberá habilitar la Detección de seguridad. |
| Tráfico TCP | Un informe de clasificación de las máquinas virtuales supervisadas y del tráfico enviado, recibido y el total, en una cuadrícula que se muestra como una línea de tendencia. |
| Comparación de tráfico | Este libro le permite comparar las tendencias en el tráfico de red para solo una máquina o para un grupo. |

## <a name="creating-a-new-workbook"></a>Creación de un libro

Un libro se compone de secciones que constan de tablas, texto, controles de entrada y gráficos que se pueden editar de forma independientemente. Para entender mejor los libros, empecemos por abrir una plantilla para recorrer la creación de un libro personalizado. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Seleccione **Máquinas virtuales**.

3. En la lista, seleccione una máquina virtual.

4. En la página de la máquina virtual, en la sección **Supervisión**, seleccione **Insights**.

5. En la página de conclusiones de la máquina virtual, seleccione la pestaña de **Rendimiento** o de **Mapas** y luego seleccione **Ver libros** en el vínculo en la página. En la lista desplegable, seleccione **Ir a la galería**.

    ![Captura de pantalla de la lista desplegable de un libro.](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Se inicia la galería de libros con varios libros pregenerados que le ayudarán a empezar a trabajar.

7. Para crear un libro, seleccione **Nuevo**.

    ![Captura de pantalla de la galería de libros](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Edición de las secciones del libro

Los libros tienen dos modos: **modo de edición** y **modo de lectura**. Cuando se inicia por primera vez un libro, se abre en **modo de edición**. Se muestra todo el contenido del libro, incluidos los pasos y los parámetros que se encuentran ocultos. El **modo de lectura** presenta una vista simplificada de estilo del informe. El modo de lectura permite reducir la complejidad que conlleva la creación de un informe, a la vez que se puede acceder a la mecánica subyacente con tan solo unos clics cuando la necesita para realizar modificaciones.

![Captura de pantalla de la sección Libro de Virtual Machines en Azure Monitor, donde se muestra un nuevo libro en el modo de edición con los controles de edición resaltados.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Cuando haya terminado de editar una sección, haga clic en **Edición finalizada** en la esquina inferior izquierda de la sección.

2. Para crear un duplicado de una sección, haga clic en el icono **Clone this section** (Clonar este sección). La creación de secciones duplicadas es una forma ideal de iterar una consulta sin perder las iteraciones anteriores.

3. Para mover hacia arriba una sección de un libro, haga clic en el icono **Subir** o **Bajar**.

4. Para eliminar una sección de forma permanente, haga clic en el icono **Quitar**.

## <a name="adding-text-and-markdown-sections"></a>Adición de texto y secciones de Markdown

La adición de títulos, explicaciones y comentarios a los libros le ayuda a convertir un conjunto de tablas y gráficos en una descripción. Las secciones de texto de los libros son compatibles con la [sintaxis de Markdown](https://daringfireball.net/projects/markdown/) para dar formato a los textos como títulos, negrita, cursiva y listas con viñetas.

Para agregar una sección de texto al libro, use el botón **Agregar texto** situado en la parte inferior del libro o en la parte inferior de cualquier sección.

## <a name="adding-query-sections"></a>Incorporación de secciones de consulta

![Sección de consulta en los libros](media/vminsights-workbooks/005-workbook-query-section.png)

Para agregar una sección de consulta al libro, use el botón **Agregar consulta** situado en la parte inferior del libro o en la parte inferior de cualquier sección.

Las secciones de consulta son muy flexibles y pueden usarse para responder preguntas como las siguientes:

* ¿Cómo ha sido la utilización de la CPU durante el mismo período de tiempo en el que se ha producido un aumento de tráfico?
* ¿Cuál ha sido la tendencia en el espacio disponible en disco en el último mes?
* ¿Cuántos errores de conexión de red ha experimentado mi máquina virtual en las últimas dos semanas? 

Además, no está limitado únicamente a realizar consultas del contexto de la máquina virtual con la que inició el libro. Puede hacer consultas en varias máquinas virtuales, así como en las áreas de trabajo de Log Analytics, siempre que tenga permiso para acceder a esos recursos.

Para incluir los datos de otras áreas de trabajo de Log Analytics o de una aplicación de Application Insights específica, use el identificador de **área de trabajo**. Para obtener más información acerca de las consultas entre recursos, consulte la [guía oficial](../logs/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Configuración avanzada de consultas analíticas

Cada sección tiene su propia configuración avanzada, a la que se puede acceder mediante el icono de configuración ![Controles de edición de la sección de libros](media/vminsights-workbooks/006-settings.png) situado a la derecha del botón **Agregar parámetros**.

![Captura de pantalla del cuadro de diálogo Configuración avanzada en la sección Libro de Virtual Machines de Azure Monitor. Se resalta el icono que abre el cuadro de diálogo.](media/vminsights-workbooks/007-settings-expanded.png)

| Configuración | Descripción |
| ---------------- |:-----|
| Ancho personalizado    | Define un tamaño arbitrario para un elemento, de modo que se puedan ajustar varios elementos en una sola línea. Esto le permitirá organizar mejor los gráficos y las tablas en informes interactivos enriquecidos.  |
| Conditionally visible (Visible condicionalmente) | Especificar para ocultar los pasos en función de un parámetro en el modo de lectura. |
| Export a parameter (Exportar un parámetro)| Permite que una fila seleccionada en la cuadrícula o en el gráfico genere más pasos para cambiar los valores o para hacerlos visibles.  |
| Mostrar la consulta cuando no se está editando | Muestra la consulta por encima del gráfico o la tabla incluso en modo de lectura.
| Mostrar el botón Abrir en Analytics cuando no se esté editando | Agrega el icono azul de Analytics a la esquina derecha del gráfico para permitir el acceso con un solo clic.|

La mayoría de estas opciones de configuración son bastante intuitivas, pero para comprender la opción **Exportar un parámetro** le recomendamos que examine un libro que use esta funcionalidad.

Uno de los libros compilados previamente, **Tráfico de TCP**, proporciona información sobre las métricas de conexión de una máquina virtual.

La primera sección del libro se basa en datos de consulta de registro. La segunda sección también se basa en datos de consulta de registro pero, si se selecciona una fila en la primera tabla, se actualizará el contenido de los gráficos de forma interactiva:

![Captura de pantalla de la sección Virtual Machines de Azure Monitor, donde se muestra el tráfico TCP del libro compilado previamente.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

El comportamiento es posible mediante la utilización de la configuración avanzada **Cuando se seleccione un elemento, exporte un parámetro**, que está habilitada en la consulta de registro de la tabla.

![Captura de pantalla del cuadro de diálogo Configuración avanzada de un libro de Virtual Machines con la opción "When an item is selected, export a parameter" (Cuando se seleccione un elemento, exporte un parámetro) activada.](media/vminsights-workbooks/009-settings-export.png)

Después, la segunda consulta de registro utiliza los valores exportados cuando se selecciona una fila para crear un conjunto de valores que luego utilizan el encabezado y los gráficos de la sección. Si no se selecciona ninguna fila, oculta el encabezado y los gráficos de la sección. 

Por ejemplo, el parámetro oculto en la segunda sección utiliza la siguiente referencia de la fila seleccionada en la cuadrícula:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Incorporación de secciones de métricas

Las secciones de las métricas le proporcionan acceso total para incorporar datos de métricas de Azure Monitor en sus informes interactivos. En WM Insights, los libros compilados previamente normalmente contendrán datos de consulta de análisis en lugar de datos de métrica.  Se puede elegir la creación de libros con datos de métrica, lo que permite aprovechar al máximo lo mejor de ambas características en un mismo lugar. También tiene la capacidad de extraer datos de métricas de recursos en cualquiera de las suscripciones a las que tiene acceso.

Este es un ejemplo de datos de la máquina virtual que se extraen en un libro para proporcionar una visualización de cuadrícula del rendimiento de la CPU:

![Captura de pantalla de la sección de métricas de un libro de máquina virtual en Azure Monitor. El rendimiento de la CPU de cada máquina virtual se muestra gráficamente.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Incorporación de secciones de parámetros

Los parámetros de libros le permiten cambiar valores en el libro sin tener que editar manualmente las secciones de texto o consulta. Esto elimina la necesidad de tener que comprender el lenguaje de consulta de análisis subyacente y amplía significativamente la audiencia potencial de creación de informes basados en el libro.

Para reemplazar los valores de parámetros en consultas, texto o en otras secciones de parámetros, se coloca el nombre del parámetro entre llaves, como ``{parameterName}``. Los nombres de parámetros se limitan a reglas similares como identificadores de JavaScript, básicamente caracteres alfabéticos o de subrayado, seguidos de caracteres alfanuméricos o de subrayado. Por ejemplo, **a1** está permitido, pero **1a** no lo está.

Los parámetros son lineales, desde la parte superior de un libro y hasta los pasos posteriores.  Los parámetros que se declaran más adelante en un libro pueden reemplazar a aquellos que se han declarado anteriormente. Esto también permite a los parámetros que usan consultas tener acceso a los valores de parámetros definidos anteriormente. En el propio paso de un parámetro, los parámetros también son lineales, de izquierda a derecha, donde los parámetros de la derecha pueden depender de un parámetro declarado anteriormente en ese mismo paso.
 
Hay cuatro tipos diferentes de parámetros que se admiten actualmente:

| Parámetro | Descripción |
| ---------------- |:-----|
| Texto    | Permite al usuario editar un cuadro de texto y, si se quiere, se puede proporcionar una consulta para rellenar el valor predeterminado. |
| Desplegable | Permite al usuario elegir entre un conjunto de valores. |
| Selector de intervalo de tiempo| Permite al usuario elegir entre un conjunto predefinido de valores de intervalo de tiempo o seleccionar entre un intervalo de tiempo personalizado.|
| Selector de recursos | Permite al usuario elegir entre los recursos seleccionados para el libro.|

### <a name="using-a-text-parameter"></a>Uso de un parámetro de texto

El valor que un usuario escribe en el cuadro de texto se sustituye directamente en la consulta, sin caracteres de escape ni comillas. Si el valor necesario es una cadena, la consulta debe tener comillas alrededor del parámetro (como **"{parameter}"** ).

El parámetro de texto permite que el valor de un cuadro de texto se use en cualquier lugar. Puede ser el nombre de la tabla, el nombre de la columna, el nombre de la función, el operador, etc.  El tipo de parámetro de texto tiene la configuración **Obtener el valor predeterminado de consulta de análisis**, lo que permite que el autor del libro use una consulta para rellenar el valor predeterminado de ese cuadro de texto.

Cuando se usa el valor predeterminado de una consulta de registro, solo el primer valor de la primera fila (fila 0, columna 0) se utiliza como el valor predeterminado. Por lo tanto, se recomienda limitar la consulta para devolver solo una sola fila y una columna. Se omite cualquier otro dato devuelto por la consulta. 

Independientemente del valor que devuelva la consulta, se reemplazará directamente sin ningún carácter de escape ni comillas. Si la consulta no devuelve ninguna fila, el resultado del parámetro es una cadena vacía (si el parámetro no es necesario) o indefinido (si el parámetro es necesario).

### <a name="using-a-drop-down"></a>Uso de una lista desplegable

El tipo de parámetro de la lista desplegable le permite crear un control de lista desplegable, lo que permite a su vez seleccionar uno o varios valores.

La lista desplegable se rellena mediante una consulta de registro o JSON. Si la consulta devuelve una columna, los valores de esa columna son el valor y la etiqueta en el control de la lista desplegable. Si la consulta devuelve dos columnas, la primera es el valor y la segunda es la etiqueta que se muestra en la lista desplegable. Si la consulta devuelve tres columnas, la tercera se utiliza para indicar la selección predeterminada en esa lista desplegable. Esta columna puede ser de cualquier tipo, pero lo más sencillo es usar tipos booleanos o numéricos, donde 0 es falso y 1 es verdadero.

Si la columna es un tipo de cadena, la cadena nula o vacía se considera falsa y cualquier otro valor se considera verdadero. Para las listas desplegables de selección única, el primer valor con un valor verdadero se utiliza como la selección predeterminada.  Para las listas desplegables de selección múltiple, todos los valores con un valor verdadero se utilizan como el conjunto de selección predeterminada. Los elementos en la lista desplegable se muestran en el orden en el que la consulta devuelve las filas. 

Echemos un vistazo a los parámetros del informe Información general de conexiones. Haga clic en el símbolo de edición junto a **Dirección**.

![Captura de pantalla de la sección para agregar y editar parámetros de informe en Azure Monitor. El icono de edición para el parámetro Dirección está seleccionado.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Esto iniciará el elemento de menú **Editar parámetro**.

![Captura de pantalla del cuadro de diálogo Editar parámetro. El nombre del parámetro es Dirección, el tipo de parámetro es Desplegable y está seleccionado JSON para Obtener datos de.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON permite generar una tabla arbitraria que se rellena con contenido. Por ejemplo, el siguiente JSON genera dos valores en la lista desplegable:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Un ejemplo más aplicable es usar una lista desplegable para elegir entre un conjunto de contadores de rendimiento por el nombre:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

La consulta mostrará los resultados de la siguiente forma:

![Lista desplegable de contador de rendimiento](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Las listas desplegables son herramientas increíblemente eficaces para personalizar y crear informes interactivos.

### <a name="time-range-parameters"></a>Parámetros de intervalo de tiempo

Aunque puede elaborar su propio parámetro de intervalo de tiempo personalizado mediante el tipo de parámetro de la lista desplegable, también puede usar el tipo de parámetro de intervalo de tiempo ya definido si no necesita el mismo grado de flexibilidad. 

Los tipos de parámetro de intervalo de tiempo tienen 15 intervalos predeterminados que van desde cinco minutos hasta los últimos 90 días. También hay una opción para permitir la selección del intervalo de tiempo personalizado, que permite que el operador del informe elija los valores explícitos de inicio y fin del intervalo de tiempo.

### <a name="resource-picker"></a>Selector de recursos

El tipo de parámetro del selector de recursos le permite definir el ámbito del informe para ciertos tipos de recursos. Un ejemplo de libro compilado previamente que aprovecha el tipo de selector de recursos es el libro **Rendimiento**.

![Lista desplegable de áreas de trabajo](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Almacenamiento y uso compartido de libros con el equipo

Los libros se guardan en un área de trabajo de Log Analytics o en un recurso de máquina virtual, en función de cómo se accede a la galería de libros. El libro se puede guardar en la sección privada de **Mis informes** o en la sección **Informes compartidos** a la que pueden acceder todos los usuarios con acceso al recurso. Para ver todos los libros del recurso, haga clic en el botón **Abrir** de la barra de acciones.

Para compartir un libro que está actualmente en **Mis informes**:

1. Haga clic en **Abrir** en la barra de acciones
2. Haga clic en el botón "..." situado junto al libro que desea compartir
3. Haga clic en **Move to Shared Reports** (Mover a informes compartidos).

Para compartir un libro con un vínculo o por correo electrónico, haga clic en **Compartir** en la barra de acciones. Tenga en cuenta que los destinatarios del vínculo necesitarán acceder a este recurso en Azure Portal para ver el libro. Para realizar ediciones, los destinatarios necesitan al menos permisos de colaborador para el recurso.

Para anclar un vínculo a un libro en un panel de Azure:

1. Haga clic en **Abrir** en la barra de acciones
2. Haga clic en el botón "..." situado junto al libro que desea anclar
3. Haga clic en **Anclar al panel**.

## <a name="next-steps"></a>Pasos siguientes

- Para identificar las limitaciones y el rendimiento general de la máquina virtual, consulte [Visualización del rendimiento de la máquina virtual de Azure](vminsights-performance.md).

- Para obtener información sobre las dependencias de las aplicaciones detectadas, vea [Visualización de la característica de asignación de VM Insights](vminsights-maps.md).
