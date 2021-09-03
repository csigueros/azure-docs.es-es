---
title: Establecimiento dinámico de nombres de columna en flujos de datos
description: En este tutorial se proporcionan los pasos para establecer dinámicamente nombres de columna en flujos de datos.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/17/2021
ms.openlocfilehash: 96143f39811658c2794b46f3504a1a604264ab13
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112542995"
---
# <a name="dynamically-set-column-names-in-data-flows"></a>Establecimiento dinámico de nombres de columna en flujos de datos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Muchas veces, al procesar datos para trabajos ETL, tendrá que cambiar los nombres de columna antes de escribir los resultados. A veces, esto es necesario para alinear los nombres de columna con un esquema de destino conocido. En otras ocasiones, es posible que tenga que establecer nombres de columna en tiempo de ejecución en función de los esquemas en constante evolución. En este tutorial, aprenderá a usar flujos de datos para establecer nombres de columna para los archivos de destino y las tablas de base de datos dinámicamente mediante parámetros y archivos de configuración externos.

Si no está familiarizado con Azure Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).

## <a name="prerequisites"></a>Requisitos previos
* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de Azure Storage**. El almacenamiento ADLS se puede usar como almacén de datos de *origen* y *receptor*. Si no tiene una cuenta de almacenamiento, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md) para crear una.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

En este paso, creará una factoría de datos y abrirá la interfaz de usuario de Data Factory para crear una canalización en la factoría de datos.

1. Abra **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
1. En el menú de la izquierda, seleccione **Crear un recurso** > **Integración** > **Data Factory**.
1. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**.
1. Seleccione la **suscripción** de Azure en la que quiere crear la factoría de datos.
1. Para **Grupo de recursos**, realice uno de los siguientes pasos:
    * Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable.
    * Seleccione **Crear nuevo** y especifique un nombre de grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).    
1. En **Versión**, seleccione **V2**.
1. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que la factoría de datos usa pueden estar en otras regiones.
1. Seleccione **Crear**.
1. Una vez finalizada la creación, verá el aviso en el centro de notificaciones. Seleccione **Ir al recurso** para ir a la página de Data Factory.
1. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creación de una canalización con una actividad de flujo de datos

En este paso, creará una canalización que contiene una actividad de flujo de datos.

1. En la página principal de ADF, seleccione **Crear canalización**.
1. En la pestaña **General** de la canalización, escriba **DeltaLake** en el campo **Nombre** de la canalización.
1. En la barra superior de Data Factory, deslice el control deslizante **Depuración de Data Flow** para activarlo. El modo de depuración permite realizar pruebas interactivas de la lógica de transformación en un clúster de Spark activo. Los clústeres de Data Flow tardan de 5 a 7 minutos en prepararse y se recomienda que los usuarios activen primero la depuración si planean realizar el desarrollo de Data Flow. Para más información, consulte [Modo de depuración](concepts-data-flow-debug-mode.md).

    ![Actividad de Data Flow](media/tutorial-data-flow/dataflow1.png)
1. En el panel **Actividades** expanda el acordeón **Movimiento y transformación**. Arrastre y coloque la actividad **Data Flow** del panel al lienzo de la canalización.

    ![Captura de pantalla que muestra el lienzo de canalización donde puede colocar la actividad de Data Flow.](media/tutorial-data-flow/activity1.png)
1. En el menú emergente **Adding Data Flow** (Agregar Data Flow), seleccione **Create New Data Flow** (Crear Data Flow) y, después, asigne el nombre **DynaCols** al flujo de datos. Haga clic en Finalizar cuando haya terminado.    

## <a name="build-dynamic-column-mapping-in-data-flows"></a>Creación de asignaciones de columnas dinámicas en flujos de datos

En este tutorial, se usará un archivo de clasificación de películas de ejemplo y se modificará el nombre de algunos de los campos del origen a un nuevo conjunto de columnas de destino que pueden cambiar con el tiempo. Los conjuntos de datos que creará a continuación deben apuntar a este archivo CSV de películas en la cuenta de almacenamiento de Blob Storage o ADLS Gen2. [Descargue el archivo de películas aquí](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB.csv) y guárdelo en la cuenta de almacenamiento de Azure.

![Flujo final](media/data-flow/dynacols-1.png "Flujo final")

### <a name="tutorial-objectives"></a>Objetivos del tutorial

Aprenderá a establecer dinámicamente nombres de columna mediante un flujo de datos.

1. Creará un conjunto de datos de origen para el archivo CSV de películas.
1. Creará un conjunto de datos de búsqueda para un archivo de configuración JSON de asignación de campos.
1. Convertirá las columnas del origen a los nombres de las columnas de destino.

### <a name="start-from-a-blank-data-flow-canvas"></a>Comenzar con un lienzo de flujo de datos en blanco

En primer lugar, se configurará el entorno de flujo de datos para cada uno de los mecanismos que se describen a continuación para enviar datos a ADLS Gen2.

1. Haga clic en la transformación de origen y asígnele el nombre ```movies1```.
1. Haga clic en el botón Nuevo situado junto al conjunto de datos en el panel inferior.
1. Elija Blob o ADLS Gen2, en función de dónde haya almacenado el archivo moviesDB.csv anterior.
1. Agregue un segundo origen, que usará para obtener el origen del archivo JSON de configuración en las asignaciones de campos de búsqueda.
1. Asígnele el nombre ```columnmappings```.
1. Para el conjunto de datos, apunte a un nuevo archivo JSON que almacenará una configuración para la asignación de columnas. Puede pegar el archivo JSON para este ejemplo de tutorial:
    ```
    [
    {"prevcolumn":"title","newcolumn":"movietitle"},
    {"prevcolumn":"year","newcolumn":"releaseyear"}
    ]
    ```

1. Establezca esta configuración de origen en ```array of documents```.
1. Agregue un tercer origen y asígnele el nombre ```movies2```. Configure esto exactamente igual que ```movies1```.
   
### <a name="parameterized-column-mapping"></a>Asignación de columnas parametrizadas

En este primer escenario, definirá nombres de columna de salida en el flujo de datos mediante el establecimiento de la asignación de columnas en función de la coincidencia de campos entrantes con un parámetro que es una matriz de cadenas de columnas y hará coincidir cada índice de matriz con la posición ordinal de la columna entrante. Al ejecutar este flujo de datos desde una canalización, podrá establecer nombres de columna diferentes en cada ejecución de canalización mediante el envío de este parámetro de matriz de cadenas a la actividad de flujo de datos.

![Parámetros](media/data-flow/dynacols-3.png "Parameters")

1. Vuelva al diseñador de flujo de datos y modifique el flujo de datos creado anteriormente.
1. Haga clic en la pestaña de parámetros.
1. Cree un parámetro y elija el tipo de datos de la matriz de cadenas.
1. Para el valor predeterminado, escriba ```['a','b','c']```.
1. Use el origen ```movies1``` superior para modificar los nombres de columna y asignarlos a estos valores de matriz.
1. Agregue una transformación Select (Seleccionar). La transformación se usará para asignar columnas entrantes a nuevos nombres de columna para la salida.
1. Se cambiarán los tres primeros nombres de columna por los nuevos nombres definidos en el parámetro.
1. Para ello, agregue tres entradas de asignación basadas en reglas en el panel inferior.
1. Para la primera columna, la regla de coincidencia será ```position==1``` y el nombre será ```$parameter1[1]```.
1. Siga el mismo patrón para las columnas 2 y 3.
 
    ![Transformación Select](media/data-flow/dynacols-4.png "Transformación Select (Seleccionar)") (Seleccionar)

1. Haga clic en las pestañas Inspeccionar y Vista previa de datos de la transformación Select (Seleccionar) para ver que los nuevos valores de nombre de columna ```(a,b,c)``` reemplazan a los nombres de columna de películas, títulos y géneros originales.
   
### <a name="create-a-cached-lookup-of-external-column-mappings"></a>Creación de una búsqueda en caché de asignaciones de columnas externas

A continuación, se creará un receptor en caché para una búsqueda posterior. La caché leerá un archivo de configuración JSON externo que se puede usar para cambiar el nombre de las columnas dinámicamente en cada ejecución de canalización del flujo de datos.

1. Vuelva al diseñador de flujo de datos y modifique el flujo de datos creado anteriormente. Agregue una transformación de receptor al origen ```columnmappings```.
1. Establezca el tipo de receptor en ```Cache```.
1. En Configuración, elija ```prevcolumn``` como columna de clave.

### <a name="lookup-columns-names-from-cached-sink"></a>Búsqueda de nombres de columnas desde el receptor en caché

Ahora que ha almacenado el contenido del archivo de configuración en memoria, puede asignar dinámicamente nombres de columna entrantes a nuevos nombres de columna salientes.

1. Vuelva al diseñador del flujo de datos y modifique el flujo de datos creado anteriormente. Haga clic en la transformación de origen ```movies2```.
1. Agregue una transformación Select (Seleccionar). Esta vez, se usará la transformación Select (Seleccionar) para cambiar el nombre de las columnas según el nombre de destino del archivo de configuración JSON que se almacena en el receptor en caché.
1. Agregue una asignación basada en reglas. Para la condición de coincidencia, use esta fórmula: ```!isNull(cachedSink#lookup(name).prevcolumn)```.
1. Para el nombre de la columna de salida, use esta fórmula: ```cachedSink#lookup($$).newcolumn```.
1. Lo que ha hecho es buscar todos los nombres de columna que coincidan con la propiedad ```prevcolumn``` del archivo de configuración JSON externo y cambiar el nombre de cada coincidencia al nuevo nombre ```newcolumn```.
1. Haga clic en las pestañas Vista previa de datos e Inspeccionar de la transformación Select (Seleccionar); ahora debería ver los nuevos nombres de columna del archivo de asignación externo.

![Origen 2](media/data-flow/dynacols-2.png "Origen 2")

## <a name="next-steps"></a>Pasos siguientes

* La canalización completa de este tutorial se puede descargar [aquí](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DynaColsPipe.zip).
* Más información sobre los [receptores de flujo de datos](data-flow-sink.md).
