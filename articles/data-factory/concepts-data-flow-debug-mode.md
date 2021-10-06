---
title: Modo de depuración de flujos de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Inicie una sesión de depuración interactiva al crear flujos de datos con Azure Data Factory o Synapse Analytics.
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e6b1524f15f07bc1cb17842c5fca167016136ae0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828441"
---
# <a name="mapping-data-flow-debug-mode"></a>Modo de depuración de flujos de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Información general

El modo de depuración de flujos de datos de asignación de Azure Data Factory y Synapse Analytics permite ver de forma interactiva cómo se transforman los datos mientras se crean y depuran los flujos de datos. La sesión de depuración se puede usar tanto en sesiones de diseño de Data Flow como durante la ejecución de la depuración de la canalización de los flujos de datos. Para activar el modo de depuración, use el botón **Data Flow Debug** (Depuración de flujo de datos) de la barra superior del lienzo de flujo de datos o del lienzo de la canalización cuando tenga actividades de flujo de datos.

:::image type="content" source="media/data-flow/debug-button.png" alt-text="Captura de pantalla que muestra dónde está el control deslizante Depurar 1":::

:::image type="content" source="media/data-flow/debug-button-4.png" alt-text="Captura de pantalla que muestra dónde está el control deslizante Depurar 2":::

Cuando active el control deslizante, se le pedirá que seleccione la configuración del entorno de ejecución de integración que quiere usar. Si se elige AutoResolveIntegrationRuntime, se desarrollará un clúster con ocho núcleos de proceso general con un período de vida predeterminado de 60 minutos. Si desea permitir un equipo más inactivo antes de que se agote el tiempo de espera de la sesión, puede elegir un valor de TTL superior. Para obtener más información sobre los entornos de ejecución de integración de flujo de datos, vea [Rendimiento de Microsoft Integration Runtime](concepts-integration-runtime-performance.md).

:::image type="content" source="media/data-flow/debug-new-1.png" alt-text="Selección del IR de depuración":::

Cuando el modo de depuración está activado, creará interactivamente el flujo de datos con un clúster de Spark activo. Una vez que desactive la depuración, se cerrará la sesión. Debe tener en cuenta los gastos por hora que genera Azure Databricks durante el tiempo que tiene la sesión de depuración activa.

En la mayoría de los casos, se recomienda crear instancias de Data Flow en modo de depuración para poder validar la lógica de negocios y ver las transformaciones de datos antes de publicar el trabajo. Use el botón "Depurar" del panel de la canalización para probar el flujo de datos en una canalización.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions.png" alt-text="Visualización de sesiones de depuración del flujo de datos":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions-synapse.png" alt-text="Visualización de sesiones de depuración del flujo de datos":::

---

> [!NOTE]
> Cada sesión de depuración que un usuario inicia desde su interfaz de usuario del explorador es una sesión nueva con su propio clúster de Spark. Puede usar la vista de supervisión en las sesiones de depuración anteriores para ver y administrar las sesiones de depuración. Se le cobra por cada hora de ejecución de cada sesión de depuración, incluida la hora de TTL.

## <a name="cluster-status"></a>Estado del clúster

El indicador de estado del clúster en la parte superior de la superficie de diseño se pone de color verde cuando el clúster esté listo para realizar la depuración. Si el clúster ya está activo, el indicador verde aparecerá casi al instante. Si el clúster aún no estaba en ejecución cuando entró en modo de depuración, el clúster de Spark realizará un inicio en frío. El indicador girará hasta que el entorno esté listo para la depuración interactiva.

Cuando haya terminado la depuración, desactive el modificador de depuración para que el clúster de Spark pueda finalizar y no se le cobre por la actividad de depuración.

## <a name="debug-settings"></a>Configuración de depuración

Una vez activado el modo de depuración, puede editar la forma en que un flujo de datos obtiene una vista previa de los datos. Para editar las opciones de depuración, haga clic en "Configuración de depuración" en la barra de herramientas del lienzo de Data Flow. Puede seleccionar los límites de fila o el origen de archivos que se usarán en cada transformación de origen. Los límites de fila de esta configuración solo son para la sesión de depuración actual. También puede seleccionar el servicio vinculado de almacenamiento provisional que se va a usar como origen de Azure Synapse Analytics. 

:::image type="content" source="media/data-flow/debug-settings.png" alt-text="Configuración de depuración":::

Si tiene parámetros en su instancia de Data Flow o cualquiera de los conjuntos de datos a los que se hace referencia, puede especificar los valores que se van usar durante la depuración seleccionando la pestaña **Parámetros**.

Use la configuración de ejemplo aquí para apuntar a archivos de ejemplo o tablas de datos de ejemplo, de modo que no tenga que cambiar los conjuntos de datos de origen. Mediante el uso de un archivo o una tabla de ejemplo aquí, puede mantener la misma lógica y los mismos valores de propiedades en el flujo de datos al realizar pruebas en un subconjunto de datos.

:::image type="content" source="media/data-flow/debug-settings2.png" alt-text="Parámetros de configuración de depuración":::

El valor predeterminado de IR usado para el modo de depuración en los flujos de datos es un único nodo de trabajo de 4 núcleos pequeño con un nodo de un solo controlador de 4 núcleos. Este valor funciona bien con ejemplos más pequeños de datos al probar la lógica del flujo de datos. Si expande los límites de fila en la configuración de depuración durante la vista previa de los datos o establece un número mayor de filas muestreadas en el origen durante la depuración de la canalización, es posible que quiera considerar la posibilidad de configurar un entorno de proceso más grande en una nueva instancia de Azure Integration Runtime. Después, puede reiniciar la sesión de depuración con el entorno de proceso más grande.

## <a name="data-preview"></a>Vista previa de datos

Con la depuración activada, la pestaña Vista previa de datos se ilumina en el panel inferior. Sin el modo de depuración activado, Data Flow mostrará solo los metadatos actuales dentro y fuera de cada una de las transformaciones de la pestaña Inspeccionar. La vista previa de datos solo consultará el número de filas que se ha establecido como el límite en la configuración de depuración. Haga clic en **Actualizar** para obtener la vista previa de los datos.

:::image type="content" source="media/data-flow/datapreview.png" alt-text="Vista previa de datos":::

> [!NOTE]
> Los orígenes de archivo solo limitan las filas que se ven, no las filas que se leen. En el caso de conjuntos de datos grandes, se recomienda que tome una pequeña parte de ese archivo y la use para las pruebas. Puede seleccionar un archivo temporal en Configuración de depuración para cada origen que sea un tipo de conjunto de datos de archivo.

Cuando se ejecuta en el modo de depuración en Data Flow, no se escribirán los datos en el receptor de transformación. Una sesión de depuración está diseñada para que funcione como una herramienta de ejecución de pruebas para las transformaciones. Los receptores no son necesarios durante la depuración y se omiten en el flujo de datos. Si quiere probar a escribir los datos en el receptor, ejecute la instancia de Data Flow desde una canalización y use la ejecución de la depuración desde una canalización.

La vista previa de los datos es una instantánea de los datos transformados mediante los límites de fila y el muestreo de datos de las tramas de datos en la memoria de Spark. Por lo tanto, los controladores de receptor no se usan ni se prueban en este escenario.

### <a name="testing-join-conditions"></a>Condiciones de combinación de pruebas

Cuando las pruebas unitarias realicen transformaciones Joins, Exists o Lookup, asegúrese de usar un pequeño conjunto de datos conocidos para la prueba. Puede usar la opción Configuración de depuración anterior para establecer un archivo temporal que se usará para las pruebas. Esto es necesario porque al limitar o muestrear filas de un conjunto de datos grande, no se puede predecir qué filas y qué claves se leerán en el flujo de las pruebas. El resultado es no determinista, lo que significa que se pueden producir errores en las condiciones de combinación.

### <a name="quick-actions"></a>Acciones rápidas

Una vez obtenida la vista previa de los datos, puede generar una transformación rápida para quitar una columna o realizar en ella una conversión de tipo o una modificación. Haga clic en el encabezado de la columna y seleccione una de las opciones de la barra de herramientas de vista previa de datos.

:::image type="content" source="media/data-flow/quick-actions1.png" alt-text="Captura de pantalla en la que se muestra la barra de herramientas de vista previa de los datos con opciones: Conversión de tipo, Modificar, Estadísticas y Eliminar":::.

Una vez que seleccione una modificación, la vista previa de los datos se actualizará inmediatamente. Haga clic en **Confirmar** en la esquina superior derecha para generar una nueva transformación.

:::image type="content" source="media/data-flow/quick-actions2.png" alt-text="Captura de pantalla en la que se muestra el botón Confirmar":::.

Con las opciones **Conversión de tipo** y **Modificar** se generará una transformación Columna derivada, mientras que con la opción **Quitar** se generará una transformación Select.

:::image type="content" source="media/data-flow/quick-actions3.png" alt-text="Captura de pantalla en la que se muestra la configuración de la columna derivada":::.

> [!NOTE]
> Si edita su instancia de Data Flow, deberá volver a obtener la vista previa de los datos para poder agregar una transformación rápida.

### <a name="data-profiling"></a>Generación de perfiles de los datos

Si selecciona una columna en la pestaña de vista previa de datos y hace clic en **Estadísticas** en la barra de herramientas de la vista previa de datos, aparecerá un gráfico en el extremo derecho de la cuadrícula de datos con estadísticas detalladas sobre cada campo. El servicio realizará una determinación basada en el muestreo de datos de qué tipo de gráfico mostrar. Los campos de alta cardinalidad establecerán como valor predeterminado los gráficos NULL o NOT NULL, mientras que los datos categóricos y numéricos que tienen una cardinalidad baja mostrarán gráficos de barras con la frecuencia de valor de los datos. También verá la longitud máxima y mínima de los campos de cadena, los valores máximos y mínimos en los campos numéricos, la desviación estándar, los percentiles, los recuentos y el promedio.

:::image type="content" source="media/data-flow/stats.png" alt-text="Estadísticas de columna":::

## <a name="next-steps"></a>Pasos siguientes

* Una vez que haya terminado de compilar y depurar el flujo de datos, [ejecútelo desde una canalización.](control-flow-execute-data-flow-activity.md)
* Al probar la canalización con un flujo de datos, use la [opción de ejecución de depuración](iterative-development-debugging.md) de la canalización.
