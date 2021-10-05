---
title: Capacidades de búsqueda en Azure Sentinel| Microsoft Docs
description: Use las consultas de búsqueda integradas de Azure Sentinel a modo de guía para formular las preguntas adecuadas que lleven a detectar problemas en los datos.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2021
ms.author: yelevin
ms.openlocfilehash: ecb8559af1d2aaf70bee0031930a748ff534f1c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622538"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Búsqueda de amenazas con Azure Sentinel

> [!IMPORTANT]
>
> La experiencia de consulta entre recursos y las actualizaciones del **panel de búsqueda** (vea los elementos marcados a continuación) se encuentran actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Tanto los analistas como los investigadores de seguridad, quieren buscar amenazas de seguridad de forma proactiva, pero los distintos sistemas y dispositivos de seguridad generan gran cantidad de datos que pueden ser difíciles de analizar y filtrar para convertirlos en eventos significativos. Azure Sentinel tiene eficaces herramientas de búsqueda y consulta que permiten buscar amenazas de seguridad en los orígenes de datos de cualquier organización. Para ayudar a los analistas de seguridad a buscar proactivamente nuevas anomalías que ni las aplicaciones de seguridad ni las reglas de análisis programadas han sido capaces de identificar, las consultas de búsqueda integradas de Azure Sentinel servirán de guía para formular las preguntas adecuadas para detectar problemas en los datos que ya hay en la red. 

Por ejemplo, una consulta integrada proporciona datos sobre los procesos menos habituales que se ejecutan en la infraestructura. No desea recibir una alerta cada vez que se ejecuten (pueden ser totalmente inofensivos), pero puede echar un vistazo a la consulta de vez en cuando para ver si hay algo inusual.

## <a name="use-built-in-queries"></a>Uso de consultas integradas

En el [panel de búsqueda](#use-the-hunting-dashboard-public-preview) se proporcionan ejemplos predefinidos de consultas diseñadas para que pueda empezar a trabajar y a familiarizarse con las tablas y el lenguaje de consulta. Las consultas se ejecutan en datos almacenados en tablas de registro, como para la creación de procesos, eventos de DNS u otros tipos de eventos.

Las consultas de búsqueda integradas las desarrollan los investigadores de seguridad de Microsoft de forma continua mediante la incorporación de nuevas consultas y el ajuste de las consultas existentes para proporcionar un punto de entrada para buscar nuevas detecciones y averiguar dónde empezar a buscar los comienzos de nuevos ataques.

Use consultas antes, durante y después de un ataque para realizar las acciones siguientes:

- **Antes de que se produzca un incidente**: no es suficiente esperar a las detecciones. Tome medidas proactivas mediante la ejecución de cualquier consulta de búsqueda de amenazas relacionada con los datos que ingiere en el área de trabajo al menos una vez a la semana.

    Los resultados de la búsqueda proactiva proporcionan una visión temprana de los eventos que puede confirmar que hay un ataque en proceso o, al menos, mostrará áreas más débiles en el entorno que están en riesgo y necesitan atención.

- **Durante un ataque**: use [Live Stream](livestream.md) para ejecutar una consulta específica de manera constante y presentar los resultados a medida que llegan. Use Live Stream cuando necesite supervisar de forma activa los eventos de usuario, por ejemplo, si necesita comprobar si todavía se produce un ataque específico, para ayudar a determinar la siguiente acción de un actor de amenazas y hacia el final de una investigación para confirmar que el ataque ha terminado realmente.

- **Después de un ataque**: después de que se haya producido un ataque o un incidente, asegúrese de mejorar la cobertura y la información para evitar incidentes similares en el futuro.

    - Modifique las consultas existentes o cree otras para ayudar con la detección temprana, en función de las conclusiones que haya obtenido del ataque o incidente.

    - Si ha detectado o creado una consulta de búsqueda que proporciona conclusiones muy valiosas sobre posibles ataques, cree reglas de detección personalizadas en función de esa consulta y exponga esas conclusiones como alertas para los elementos de respuesta a los incidentes de seguridad.

        Vea los resultados de la consulta y seleccione **Nueva regla de alertas** > **Crear una alerta de Azure Sentinel**. Use el **asistente para reglas de análisis** para crear una regla en función de la consulta. Para obtener más información, vea [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md).


> [!TIP]
> - Ahora en versión preliminar pública, también puede crear consultas de búsqueda y Live Stream sobre los datos almacenados en Azure Data Explorer. Para obtener más información, vea los detalles de la [creación de consultas entre recursos](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md) en la documentación de Azure Monitor.
>
> - Use recursos de la comunidad, como el [repositorio de Azure Sentinel en GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Hunting%20Queries) para buscar consultas y orígenes de datos adicionales.
>

## <a name="use-the-hunting-dashboard-public-preview"></a>Uso del panel de búsqueda (versión preliminar pública)

El panel de búsqueda le permite ejecutar todas las consultas, o un subconjunto concreto, en una sola selección. En el portal de Azure Sentinel, seleccione **Hunting** (Búsqueda).

En la tabla se muestran todas las consultas escritas por el equipo de analistas de seguridad de Microsoft, y otras consultas adicionales que haya creado o modificado. En cada consulta se proporciona una descripción de lo que se busca y en qué tipo de datos se ejecuta. Estas plantillas se agrupan según diversas tácticas; así, los iconos de la derecha clasifican el tipo de amenaza, como acceso inicial, persistencia y filtración.

:::image type="content" source="media/hunting/hunting-start.png" alt-text="Azure Sentinel empieza a buscar" lightbox="media/hunting/hunting-start.png":::

Use el panel de búsqueda para identificar dónde empezar la búsqueda. Para ello, examine el recuento de resultados, los picos o el cambio en el recuento de resultados en un período de 24 horas. Puede ordenar y filtrar por favoritos, origen de datos, táctica o técnica MITRE ATT&CK, resultados o diferencias de resultados. Vea las consultas que todavía necesitan orígenes de datos conectados** y obtenga recomendaciones sobre cómo habilitarlas.

En la tabla siguiente se describen las acciones detalladas disponibles en el panel de búsqueda:

|Acción  |Descripción  |
|---------|---------|
|**Ver cómo se aplican las consultas al entorno**     |   Seleccione el botón **Run all queries (Preview)** [Ejecutar todas las consultas (versión preliminar)], o bien seleccione un subconjunto de consultas mediante las casillas que hay a la izquierda de cada fila y seleccione el botón **Run selected queries (Preview)** [Ejecutar las consultas seleccionadas (versión preliminar)]. <br><br>La ejecución de las consultas puede tardar entre unos segundos y varios minutos, en función del número de consultas seleccionadas, el intervalo de tiempo y la cantidad de datos que se consultan.      |
|**Ver las consultas que han devuelto resultados**    |      Una vez que las consultas terminen de ejecutarse, vea cuáles han devuelto resultados mediante el filtro **Results** (Resultados): <br>- Ordene para ver qué consultas han tenido más el mayor o menor número de resultados. <br>- Vea qué consultas no están activas en el entorno; para ello, seleccione *N/A* (N/D) en el filtro **Results** (Resultados). <br>- Mantenga el puntero sobre el icono de información (**i**) que aparece junto a *N/A* (N/D) para ver qué orígenes de datos se necesitan para activar esta consulta.  |
|**Identificar picos en los datos**     |   Para identificar picos en los datos, ordene o filtre por **Results delta** (Diferencia de resultados). <br><br>De esta forma se comparan los resultados de las últimas 24 horas con los de las 24-48 horas anteriores, para resaltar las diferencias de volumen grandes.     |
|**Ver consultas asignadas a la táctica MITRE Att&CK**     | En la **barra de tácticas de MITRE ATT&CK**, al principio de la tabla, se muestran cuántas consultas se asignan a cada táctica de MITRE ATT&CK. La barra de tácticas se actualiza dinámicamente en función del actual conjunto de filtros aplicado. <br><br>Esto le permite ver qué tácticas de MITRE ATT&CK se muestran al filtrar por un recuento de resultados determinado, una diferencia de resultados alta, resultados de tipo *N/A* (N/D) o cualquier otro conjunto de filtros.        |
|**Ver las consultas asignadas a técnicas de MITRE ATT&CK**     | Las consultas también se pueden asignar a técnicas de MITRE ATT&CK. Para filtrar u ordenar por técnicas de MITRE ATT&CK, utilice el filtro **Technique** (Técnica). Al abrir una consulta, podrá seleccionar la técnica para ver la descripción de MITRE ATT&CK de la técnica.        |
|**Guardar una consulta en los favoritos**     |   Las consultas guardadas en favoritos se ejecutan automáticamente cada vez que se accede a la página **Hunting** (Búsqueda). Puede crear sus propias consultas de búsqueda o clonar y personalizar una plantilla de consulta de búsqueda ya existente.      |
|**Ejecución de consultas**     |   Seleccione **Run query** (Ejecutar consulta) en la página de detalles de la consulta de búsqueda para ejecutar la consulta directamente desde la página de búsquedas. El número de coincidencias se muestra en la tabla, en la columna **Results** (Resultados). Revise la lista de consultas de búsqueda y las coincidencias encontradas correspondientes.     |
|**Revisión de una consulta subyacente**     | Realice una revisión rápida de la consulta subyacente en el panel de detalles de la consulta. Para ver los resultados, haga clic en el vínculo **View query results** (Ver resultados de la consulta) (debajo de la ventana de consulta) o en el botón **View Results** (Ver resultados) (en la parte inferior del panel). La consulta se abrirá en la hoja **Logs** (Registros) (Log Analytics) y, debajo de la consulta, puede revisar las coincidencias de la consulta.         |
|     |         |


## <a name="create-your-own-bookmarks"></a>Creación de marcadores propios

Durante el proceso de búsqueda e investigación, es posible que encuentre resultados de consulta que puedan parecer inusuales o sospechosos. Marque estos elementos para hacerles referencia en el futuro, por ejemplo, al crear o enriquecer un incidente para investigarlo.

- En los resultados, marque las casillas de las filas que quiera conservar y seleccione **Add bookmark** (Agregar marcador). Esto crea un registro para cada fila marcada (un marcador) que contiene los resultados de la fila, la consulta que ha creado los resultados y las asignaciones de entidades para extraer usuarios, hosts y direcciones IP. Puede agregar etiquetas y notas propias a cada marcador.

- Para ver todos los resultados añadidos como marcadores, haga clic en la pestaña **Bookmarks** (Marcadores) de la página principal de **Hunting** (Búsqueda). Agregue etiquetas a los marcadores a fin de clasificarlos para el filtrado. Por ejemplo, si va a investigar una campaña de ataques, puede crear una etiqueta para la campaña, aplicar la etiqueta a todos los marcadores pertinentes y, después, filtrar todos los marcadores por esa campaña.

- Para investigar un único resultado incluido en los marcadores, seleccione el marcador y, después, haga clic en **Investigate** (Investigar) en el panel de detalles para abrir la experiencia de investigación.

    También puede crear un incidente a partir de uno o varios marcadores, o bien agregar uno o varios marcadores a un incidente existente. Active una casilla situada a la izquierda de los marcadores que quiera usar y, después, seleccione **Incident actions** > **Create new incident** (Acciones de incidente > Crear incidente), o bien **Add to existing incident** (Agregar a incidente existente). Realice una evaluación de prioridades del incidente e investíguelo como cualquier otro.


> [!TIP]
> Los marcadores representan eventos clave que son importantes y deben escalarse a incidentes si son lo suficientemente graves como para justificar una investigación. Los eventos como las posibles causas principales, los indicadores de riesgo u otros eventos importantes se deben generar como marcadores.
>

Para obtener más información, vea [Uso de marcadores de búsqueda](bookmarks.md).

## <a name="use-notebooks-to-power-investigations"></a>Uso de cuadernos como tecnología de las investigaciones

Cuando la búsqueda y las investigaciones se vuelvan más complejas, use cuadernos de Azure Sentinel para mejorar su actividad con aprendizaje automático, visualizaciones y análisis de datos.

Los cuadernos se asemejan a un espacio aislado virtual, con su propio kernel, donde puede llevar a cabo una investigación completa. El cuaderno puede incluir los datos sin procesar, el código que se ejecuta en dichos datos, los resultados y sus visualizaciones. Guarde los cuadernos para que pueda compartirlos con otros usuarios con el fin de reutilizarlos en su organización.

Los cuadernos pueden ser útiles cuando la búsqueda o la investigación adquieran un volumen demasiado grande como para recordarlas fácilmente, para ver detalles, o bien si necesita guardar consultas y resultados. Para ayudarle a crear y compartir cuadernos, Azure Sentinel proporciona [Jupyter Notebook](https://jupyter.org), un entorno de desarrollo interactivo y manipulación de datos de código abierto, integrado directamente en la página **Notebooks** de Azure Sentinel.

Para más información, consulte:

- [Uso de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)
- [Documentación de Jupyter Project](https://jupyter.org/documentation)
- [Documentación introductoria de Jupyter](https://jupyter.readthedocs.io/en/latest/tryjupyter.html)
- [Infosec Jupyter Book](https://infosecjupyterbook.com)
- [Tutoriales de Real Python](https://realpython.com)

En la siguiente tabla se describen varios métodos de uso de cuadernos de Jupyter Notebook para ayudarle con sus procesos en Azure Sentinel:

|Método  |Descripción  |
|---------|---------|
|**Persistencia de datos, repetibilidad y vuelta atrás (backtracking)**     |  Si trabaja con muchas consultas y conjuntos de resultados, es probable que tenga puntos muertos. Tendrá que decidir qué consultas y resultados conservar y cómo acumular los resultados útiles en un único informe. <br><br> Use cuadernos de Jupyter Notebook para guardar consultas y datos sobre la marcha, use variables para volver a ejecutar consultas con diferentes valores o fechas, o guarde las consultas para volver a ejecutarlas en futuras investigaciones.       |
|**Scripting y programación**     |    Use cuadernos de Jupyter Notebook para agregar programación a sus consultas, lo que incluye: <br><br>- Lenguajes *declarativos*, como el [lenguaje de consulta Kusto (KQL)](/azure/kusto/query/) o SQL, para codificar su lógica en una sola instrucción, posiblemente compleja.<br>- Lenguajes de programación de *procedimientos*, para ejecutar una lógica en una serie de pasos. <br><br>Dividir la lógica en pasos puede ayudarle a ver y depurar resultados intermedios, agregar una funcionalidad que podría no estar disponible en el lenguaje de consulta y reutilizar resultados parciales en pasos de procesamiento posteriores.     |
|**Vínculos a datos externos**     | Aunque las tablas de Azure Sentinel son las que tienen más datos de eventos y telemetría, los cuadernos de Jupyter Notebook pueden vincularse a datos que estén accesibles a través de su red o desde un archivo. Usando cuadernos de Jupyter Notebook, puede incluir datos como los siguientes: <br><br>- Datos de servicios externos que no posee, como datos de geolocalización u orígenes de inteligencia sobre amenazas<br>- Datos confidenciales que solo se almacenen en el seno de su organización, como bases de datos de recursos humanos o listas de recursos de gran valor<br>- Datos que aún no haya migrado a la nube        |
|**Herramientas especializadas de procesamiento de datos, aprendizaje automático y visualización**     | Los cuadernos de Jupyter Notebook proporcionan visualizaciones adicionales, bibliotecas de aprendizaje automático y características de procesamiento y transformación de datos. <br><br>Por ejemplo, use cuadernos de Jupyter Notebook con las siguientes funcionalidades de [Python](https://python.org):<br>- [Pandas](https://pandas.pydata.org/) para procesamiento de datos, limpieza e ingeniería<br>- [Matplotlib](https://matplotlib.org), [HoloViews](https://holoviews.org) y [Plotly](https://plot.ly) para visualización<br>- [NumPy](https://www.numpy.org) y [SciPy](https://www.scipy.org) para procesamiento numérico y científico avanzado<br>- [scikit-learn](https://scikit-learn.org/stable/index.html) para aprendizaje automático<br>- [TensorFlow](https://www.tensorflow.org/), [PyTorch](https://pytorch.org) y [Keras](https://keras.io/) para aprendizaje profundo<br><br>**Sugerencia:** Los cuadernos de Jupyter Notebook admiten varios kernels de lenguaje. Use *magics* para mezclar lenguajes dentro del mismo cuaderno, permitiendo la ejecución de celdas individuales mediante otro lenguaje. Por ejemplo, puede recuperar datos mediante una celda de script de PowerShell, procesar los datos en Python y usar JavaScript para representar una visualización.        |
|     |         |

### <a name="mstic-jupyter-and-python-security-tools"></a>Herramientas de seguridad de MSTIC, Jupyter y Python

[Microsoft Threat Intelligence Center (MSTIC)](https://msrc-blog.microsoft.com/tag/mstic/) es un equipo de ingenieros y analistas de seguridad de Microsoft que realizan detecciones de seguridad para varias plataformas de Microsoft y trabajan en la identificación e investigación de amenazas.

MSTIC ha creado [MSTICPy](https://github.com/Microsoft/msticpy), una biblioteca para investigaciones de seguridad de la información y búsqueda en cuadernos de Jupyter Notebook. MSTICPy proporciona una funcionalidad reutilizable que pretende agilizar la creación de cuadernos y que facilita la lectura de cuadernos en Azure Sentinel por parte de los usuarios.

Por ejemplo, MSTICPy puede hacer lo siguiente:

- Consultar datos de registro de varios orígenes.
- Enriquecer los datos con inteligencia sobre amenazas, geolocalizaciones y datos de recursos de Azure.
- Extraer indicadores de actividad (IoA) de registros y desempaquetar datos codificados.
- Realizar análisis sofisticados, como la detección de sesiones anómalas y la descomposición de series temporales.
- Visualizar datos usando escalas de tiempo interactivas, árboles de proceso y Morph Charts multidimensionales.

MSTICPy también incluye varias herramientas de cuadernos que ahorran tiempo, como widgets que establecen límites de tiempo de consulta, seleccionan y muestran elementos de listas, y configuran el entorno del cuaderno.

Para más información, consulte:

- [Documentación de MSTICPy](https://msticpy.readthedocs.io/en/latest/)
- [Tutorial: Introducción a los cuadernos de Jupyter Notebook y MSTICPy en Azure Sentinel](notebook-get-started.md)
- [Configuraciones avanzadas para cuadernos de Jupyter Notebook y MSTICPy en Azure Sentinel](notebooks-msticpy-advanced.md)


## <a name="useful-operators-and-functions"></a>Operadores y funciones útiles

Las consultas de búsqueda se crean en el [lenguaje de consulta Kusto (KQL)](/azure/data-explorer/kusto/query/), un lenguaje de consulta eficaz con IntelliSense que proporciona la eficacia y flexibilidad necesarias para llevar la búsqueda a un nuevo nivel.

Es el mismo lenguaje que usan las consultas en las reglas de análisis y en otros lugares de Azure Sentinel. Para obtener más información, vea [Referencia de lenguaje de consulta](../azure-monitor/logs/get-started-queries.md).

Los operadores siguientes son especialmente útiles en las consultas de búsqueda de Azure Sentinel:

- **where**: filtra una tabla por el subconjunto de filas que cumplen un predicado.

- **summarize**: crea una tabla que agrega el contenido de la tabla de entrada.

- **join**: combina las filas de dos tablas para formar una nueva tabla, haciendo coincidir los valores de las columnas especificadas de cada tabla.

- **count**: devuelve el número de registros en el conjunto de registros de entrada.

- **top**: devuelve los primeros N registros ordenados por las columnas especificadas.

- **limit**: devuelve hasta el número de filas especificado.

- **project**: selecciona las columnas que se incluirán, las cambia de nombre o las quita e inserta nuevas columnas calculadas.

- **extend**: crea columnas calculadas y las anexa al conjunto de resultados.

- **makeset**: devuelve una matriz dinámica (JSON) del conjunto de valores distintos que una expresión toma en el grupo.

- **find**: busca filas que coinciden con un predicado a través de un conjunto de tablas.

- **adx() (versión preliminar)** : esta función ejecuta consultas entre recursos de orígenes de datos de Azure Data Explorer desde la experiencia de búsqueda de Azure Sentinel y Log Analytics. Para obtener más información, consulte [Consulta entre recursos en Azure Data Explorer mediante Azure Monitor](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md).

## <a name="save-a-query"></a>Almacenamiento de una consulta

Cree o modifique una consulta, y guárdela como consulta propia o compártala con los usuarios que están en el mismo inquilino.

:::image type="content" source="./media/hunting/save-query.png" alt-text="Guardar consulta" lightbox="./media/hunting/save-query.png":::

**Para crear una consulta**:

1. Seleccione **Nueva consulta**.

1. Rellene todos los campos en blanco y seleccione **Create** (Crear).

    :::image type="content" source="./media/hunting/new-query.png" alt-text="Nueva consulta" lightbox="./media/hunting/new-query.png":::

**Para clonar y modificar una consulta existente**:

1. Seleccione la consulta de búsqueda en la tabla que quiere modificar.

1. Seleccione el botón de puntos suspensivos (...) en la línea de la consulta que quiere modificar y seleccione **Clone query** (Clonar consulta).

    :::image type="content" source="./media/hunting/clone-query.png" alt-text="Clonar una consulta" lightbox="./media/hunting/clone-query.png":::

1. Modifique la consulta y seleccione **Create** (Crear).

    :::image type="content" source="./media/hunting/custom-query.png" alt-text="Consulta personalizada" lightbox="./media/hunting/custom-query.png":::



## <a name="sample-query"></a>Consulta de ejemplo

Una consulta típica empieza por un nombre de tabla, seguido de una serie de operadores separados por \|.

En el ejemplo anterior, empiece con la tabla SecurityEvent y agregue elementos canalizados según sea necesario.

1. Defina un filtro de tiempo para revisar solo los registros de los últimos siete días.

1. Agregue un filtro a la consulta para mostrar únicamente el evento con el identificador 4688.

1. Agregue un filtro a la consulta en CommandLine para que contenga solo instancias de cscript.exe.

1. Proyecte solo las columnas que quiera explorar, limite los resultados a 1000 y seleccione **Run query** (Ejecutar consulta).

1. Seleccione el triángulo de color verde y ejecute la consulta. Puede comprobar la consulta y ejecutarla para hallar comportamientos anómalos.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a realizar una investigación de búsqueda en Azure Sentinel. 

Para más información, consulte:

- [Uso de cuadernos de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)
- [Realizar un seguimiento de los datos durante una búsqueda](bookmarks.md)

Obtenga información de un ejemplo del uso de reglas de análisis personalizadas al [supervisar Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) con un [conector personalizado](create-custom-connector.md).

