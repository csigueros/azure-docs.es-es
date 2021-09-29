---
title: Creación de canalizaciones de copia de datos a gran escala con un enfoque basado en metadatos en la herramienta de copia de datos
description: Proporciona información sobre el enfoque controlado por metadatos en la herramienta de copia de datos de ADF.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 06/19/2021
ms.author: yexu
ms.openlocfilehash: 02d7b741ec0c3fb9547d10bde759900ce3a69dd6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663352"
---
# <a name="build-large-scale-data-copy-pipelines-with-metadata-driven-approach-in-copy-data-tool-preview"></a>Creación de canalizaciones de copia de datos a gran escala con un enfoque basado en metadatos en la herramienta de copia de datos (versión preliminar)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Si desea copiar grandes cantidades de objetos (por ejemplo, miles de tablas) o cargar datos de una gran variedad de orígenes, el enfoque adecuado es introducir la lista de nombres de los objetos con los comportamientos de copia necesarios en una tabla de control y, a continuación, usar canalizaciones parametrizadas para leer lo mismo desde la tabla de control y aplicarlas a los trabajos en consecuencia.  De este modo, puede mantener (por ejemplo, agregar o quitar) la lista de objetos que se va a copiar fácilmente actualizando los nombres de objeto en la tabla de control en lugar de volver a implementar las canalizaciones. Además, tendrá un único lugar para comprobar fácilmente qué objetos copiados por qué canalizaciones o desencadenadores con comportamientos de copia definidos. 

La herramienta de copia de datos de ADF facilita el recorrido de la creación de estas canalizaciones de copia de datos controladas por metadatos. Después de pasar por un flujo intuitivo desde una experiencia basada en asistente, la herramienta puede generar canalizaciones parametrizadas y scripts de SQL para crear tablas de control externas en consecuencia. Después de ejecutar los scripts generados para crear la tabla de control en la base de datos de SQL, las canalizaciones leerán los metadatos de la tabla de control y las aplicarán automáticamente en los trabajos de copia.

## <a name="create-metadata-driven-copy-jobs-from-copy-data-tool"></a>Creación de trabajos de copia controlados por metadatos desde la herramienta de copia de datos

1. Seleccione **Copia controlada por metadatos** en la herramienta de copia de datos.

   Debe introducir la conexión y el nombre de la tabla de control para que la canalización generada lea los metadatos.

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-task-type.png" alt-text="Seleccionar el tipo de tarea":::

2. Introduzca la **conexión de su base de datos de origen**. También se puede utilizar el [servicio vinculado parametrizado](parameterize-linked-services.md).

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-parameterized-linked-service.png" alt-text="Seleccionar el servicio vinculado parametrizado":::

3. Seleccione el **nombre de la tabla** para copiar.

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-table.png" alt-text="Seleccionar tabla":::

   > [!NOTE]
   > Si selecciona almacén de datos tabulares, tendrá la oportunidad de seleccionar también la carga completa o la carga incremental en la página siguiente. Si selecciona Almacén de almacenamiento, puede seleccionar la carga completa solo en la página siguiente. Actualmente no se admite la carga incremental de nuevos archivos solo desde el almacén de almacenamiento.  

4. Elija el **comportamiento de carga**.
   >[!TIP]
   >Si desea realizar una copia completa en todas las tablas, seleccione **Carga completa de todas las tablas**. Si desea realizar una copia incremental, puede seleccionar **configurar cada tabla individualmente** y elegir **Carga delta**, así como el nombre y el valor de la columna de la marca de agua para comenzar en cada tabla. 

5. Seleccione **Almacén de datos de destino**. 

6. En la página **Configuración**, puede decidir el número máximo de actividades de copia para copiar datos de su almacén de origen de forma simultánea mediante **Número de tareas de copia simultáneas**. El valor predeterminado es 20. 

   :::image type="content" source="./media/copy-data-tool-metadata-driven/settings.png" alt-text="Página de configuración":::

7. Después de la implementación de la canalización, puede copiar o descargar los scripts de SQL de la interfaz de usuario para crear la tabla de control y el procedimiento de almacenamiento. 

   :::image type="content" source="./media/copy-data-tool-metadata-driven/download-scripts.png" alt-text="Descarga de scripts":::

   Verá dos scripts de SQL.
   
    - El primer script de SQL se usa para crear dos tablas de control. La tabla de control principal almacena la lista de tablas, la ruta de acceso de archivo o los comportamientos de copia. La tabla de control de conexión almacena el valor de conexión del almacén de datos si usó un servicio vinculado con parámetros.  
    - El segundo script de SQL se usa para crear un procedimiento de almacén. Se utiliza para actualizar el valor de marca de agua en la tabla de control principal cuando los trabajos de copia incremental se completan cada vez. 

8. Abra **SSMS** para conectarse al servidor de tablas de control y ejecute los dos scripts de SQL para crear tablas de control y almacenar el procedimiento.

   :::image type="content" source="./media/copy-data-tool-metadata-driven/create-control-table-script.png" alt-text="Creación del script de control":::

9. Consulte la tabla de control principal y la tabla de control de conexión para revisar los metadatos que contiene.

   **Tabla de control principal**
   :::image type="content" source="./media/copy-data-tool-metadata-driven/query-control-table.png" alt-text="Tabla de control de consultas de script1":::

   **Tabla de control de conexión**
   :::image type="content" source="./media/copy-data-tool-metadata-driven/query-connection-control-table.png" alt-text="Tabla de control de consultas de script2":::

10. Vuelva al portal de ADF para ver y depurar canalizaciones. Verá una carpeta creada con el nombre "MetadataDrivenCopyTask_### _######". **Haga clic** en la canalización llamada "MetadataDrivenCopyTask_###_TopLevel" y haga clic en **ejecutar depuración**. 

    Debe introducir los parámetros siguientes:
   
    | Nombre del parámetro | Descripción | 
    |:--- |:--- |
    |MaxNumberOfConcurrentTasks |Siempre puede cambiar el número máximo de actividades de copia simultáneas que se ejecutan antes de la ejecución de la canalización. El valor predeterminado será el que se introduce en la herramienta de copia de datos. |
    |MainControlTableName | Siempre puede cambiar el nombre de la tabla de control principal para que la canalización obtenga los metadatos de esa tabla antes de ejecutarse.  |
    |ConnectionControlTableName |Siempre puede cambiar el nombre de la tabla de control de la conexión (opcional), por lo que la canalización obtendrá los metadatos relacionados con la conexión del almacén de datos antes de la ejecución. |
    |MaxNumberOfObjectsReturnedFromLookupActivity |Para evitar alcanzar el límite de la actividad de búsqueda de salida, hay una manera de definir el número máximo de objetos devueltos por la actividad de búsqueda. En la mayoría de los casos, no es necesario cambiar el valor predeterminado.  |
    |windowStart |Cuando se especifica un valor dinámico (por ejemplo, aaaa/mm/dd) como ruta de acceso de carpeta, el parámetro se usa para pasar el tiempo de desencadenador actual a la canalización con el fin de rellenar la ruta de acceso de la carpeta dinámica. Cuando la canalización se desencadena mediante un desencadenador de programación o un desencadenador de ventanas de saltos de número, los usuarios no necesitan especificar el valor de este parámetro. Valor de ejemplo: 2021-01-25T01:49:28Z | 
   

11. Habilite el desencadenador para operacionalizar las canalizaciones.

    :::image type="content" source="./media/copy-data-tool-metadata-driven/enable-trigger.png" alt-text="Habilitar el desencadenador":::


## <a name="update-control-table-by-copy-data-tool"></a>Actualización de la tabla de control mediante la herramienta de copia de datos
Siempre puede actualizar directamente la tabla de control agregando o quitando el objeto que se va a copiar o cambiando el comportamiento de copia de cada tabla. También creamos experiencia de interfaz de usuario en la herramienta de copia de datos para facilitar el recorrido de edición de la tabla de control.

1. Haga clic con el botón secundario en la canalización de nivel superior: **MetadataDrivenCopyTask_xxx_TopLevel** y luego seleccione **Editar tabla de control**.

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table.png" alt-text="Edición de la tabla de control1":::

2. Seleccione las filas de la tabla de control que quiera editar.

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table-select-tables.png" alt-text="Edición de la tabla de control2":::

3. Vaya al rendimiento de la herramienta de copia de datos y aparecerá un nuevo script de SQL. Vuelva a ejecutar script de SQL para actualizar la tabla de control.

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table-create-script.png" alt-text="Edición de la tabla de control3":::

   > [!NOTE]
   > La canalización NO se volverá a implementar. El nuevo script de SQL ayuda a actualizar solo la tabla de control. 

## <a name="control-tables"></a>Tablas de control

### <a name="main-control-table"></a>Tabla de control principal
Cada fila de la tabla de control contiene los metadatos de un objeto (por ejemplo, una tabla) que se va a copiar.

| Nombre de la columna | Descripción | 
|:--- |:--- |
| Identificador | Id. único del objeto que se va a copiar. |
| SourceObjectSettings | Metadatos del conjunto de datos de origen. Puede ser el nombre del esquema, el nombre de la tabla, etc. Aquí puede ver un [ejemplo](connector-azure-sql-database.md#dataset-properties). |
| SourceConnectionSettingsName | Nombre de la configuración de conexión de origen en la tabla de control de conexión. Es opcional. |
| CopySourceSettings | Metadatos de la propiedad source en la actividad de copia. Puede ser una consulta, una partición, etc. Aquí puede ver un [ejemplo](connector-azure-sql-database.md#azure-sql-database-as-the-source). |
| SinkObjectSettings | Metadatos del conjunto de datos de destino. Puede ser un nombre de archivo, una ruta de acceso de carpeta, nombre de tabla, etc. Aquí puede ver un [ejemplo](connector-azure-data-lake-storage.md#azure-data-lake-storage-gen2-as-a-sink-type). Si se especifica la ruta de acceso de carpeta dinámica, el valor de la variable no se escribirá aquí en la tabla de control. |
| SinkConnectionSettingsName | Nombre de la configuración de conexión de origen en la tabla de control de conexión. Es opcional. |
| CopySinkSettings | Metadatos de la propiedad sink en la actividad de copia. Puede ser preCopyScript, tableOption, etc. Aquí puede ver un [ejemplo](connector-azure-sql-database.md#azure-sql-database-as-the-sink). |
| CopyActivitySettings | Metadatos de la propiedad translator en la actividad de copia. Se usa para definir la asignación de columnas. |
| TopLevelPipelineName | Nombre de la canalización superior, que puede copiar este objeto. |
| TriggerName | Nombre del desencadenador, que puede desencadenar la canalización para copiar este objeto. Si se ejecuta la depuración, el nombre es Sandbox. Si se realiza la ejecución manual, el nombre es Manual. Si la ejecución está programada, el nombre es el nombre del desencadenador asociado. Puede tener varios nombres. |
| DataLoadingBehaviorSettings |Carga completa frente a carga diferencial. |
| TaskId | El orden de los objetos que se van a copiar después de TaskId en la tabla de control (ORDER BY [TaskId] DESC). Si tiene grandes cantidades de objetos que se van a copiar, pero solo se permite un número simultáneo limitado de objetos copiados, puede cambiar el TaskId de cada objeto para decidir qué objetos se pueden copiar anteriormente. El valor predeterminado es 0. |
| CopyEnabled | Especifique si el elemento está habilitado en el proceso de ingesta de datos. Valores permitidos: 1 (habilitado), 0 (deshabilitado). El valor predeterminado es 1. |

### <a name="connection-control-table"></a>Tabla de control de conexión
Cada fila de la tabla de control contiene una configuración de conexión para el almacén de datos.

| Nombre de la columna | Descripción | 
|:--- |:--- |
| Nombre | Nombre de la conexión parametrizada en la tabla de control principal. |
| ConnectionSettings | La configuración de la conexión. Puede ser, por ejemplo, el nombre de la base de datos, el nombre del servidor, etc. |

## <a name="pipelines"></a>Canalizaciones
Verá que la herramienta de copia de datos genera tres niveles de canalizaciones.

### <a name="metadatadrivencopytask_xxx_toplevel"></a>MetadataDrivenCopyTask_xxx_TopLevel
Esta canalización calculará el número total de objetos (tablas, etc.) necesarios para copiarse en esta ejecución, aparecerá el número de lotes secuenciales en función de la tarea de copia simultánea máxima permitida y, a continuación, ejecutará otra canalización para copiar distintos lotes secuencialmente. 

#### <a name="parameters"></a>Parámetros
| Nombre del parámetro | Descripción | 
|:--- |:--- |
| MaxNumberOfConcurrentTasks | Siempre puede cambiar el número máximo de actividades de copia simultáneas que se ejecutan antes de la ejecución de la canalización. El valor predeterminado será el que se introduce en la herramienta de copia de datos. |
| MainControlTableName | Nombre de la tabla de control principal. La canalización obtiene los metadatos de esta tabla antes de la ejecución. |
| ConnectionControlTableName | Nombre de la tabla de control de conexión (opcional). La canalización obtiene los metadatos relacionados con la conexión del almacén de datos antes de la ejecución. |
| MaxNumberOfObjectsReturnedFromLookupActivity | Para evitar alcanzar el límite de la actividad de búsqueda de salida, hay una manera de definir el número máximo de objetos devueltos por la actividad de búsqueda. En la mayoría de los casos, no es necesario cambiar el valor predeterminado.  |
| windowStart | Cuando se especifica un valor dinámico (por ejemplo, aaaa/mm/dd) como ruta de acceso de carpeta, el parámetro se usa para pasar el tiempo de desencadenador actual a la canalización con el fin de rellenar la ruta de acceso de la carpeta dinámica. Cuando la canalización se desencadena mediante un desencadenador de programación o un desencadenador de ventanas de saltos de número, los usuarios no necesitan especificar el valor de este parámetro. Valor de ejemplo: 2021-01-25T01:49:28Z |

#### <a name="activities"></a>Actividades
| Nombre de la actividad | Tipo de actividad | Descripción |
|:--- |:--- |:--- |
| GetSumOfObjectsToCopy  | Búsqueda | Calcule el número total de objetos (tablas, etc.) necesarios para copiarse en esta ejecución. |
| CopyBatchesOfObjectsSequentially | ForEach | Se ofrece el número de lotes secuenciales en función del número máximo permitido de tareas de copia simultáneas y, a continuación, se ejecuta otra canalización para copiar lotes diferentes secuencialmente.  |
| CopyObjectsInOneBtach | Execute Pipeline | Ejecute otra canalización para copiar un lote de objetos. Los objetos que pertenecen a este lote se copiarán en paralelo. | 


### <a name="metadatadrivencopytask_xxx_-middlelevel"></a>MetadataDrivenCopyTask_xxx_ MiddleLevel
Esta canalización copiará un lote de objetos. Los objetos que pertenecen a este lote se copiarán en paralelo. 

#### <a name="parameters"></a>Parámetros
| Nombre del parámetro | Descripción | 
|:--- |:--- |
| MaxNumberOfObjectsReturnedFromLookupActivity | Para evitar alcanzar el límite de la actividad de búsqueda de salida, hay una manera de definir el número máximo de objetos devueltos por la actividad de búsqueda.  En la mayoría de los casos, no es necesario cambiar el valor predeterminado.  | 
| TopLayerPipelineName | Nombre de la canalización de nivel superior. | 
| TriggerName | Nombre del desencadenador. | 
| CurrentSequentialNumberOfBatch | Id. del lote secuencial. | 
| SumOfObjectsToCopy | Número total de objetos para copiar. | 
| SumOfObjectsToCopyForCurrentBatch | Número de objetos que se copian en el lote actual. | 
| MainControlTableName | Nombre de la tabla de control principal. |
| ConnectionControlTableName | Nombre de la tabla de control de conexión. |

#### <a name="activities"></a>Actividades
| Nombre de la actividad | Tipo de actividad | Descripción |
|:--- |:--- |:--- |
| DivideOneBatchIntoMultipleGroups | ForEach  | Divida los objetos de un solo lote en varios grupos paralelos para evitar alcanzar el límite de salida de la actividad de búsqueda. |
| GetObjectsPerGroupToCopy | Búsqueda | Obtenga objetos (tablas, etc.) de la tabla de control que se deben copiar en este grupo. El orden de los objetos que se van a copiar después de TaskId en la tabla de control (ORDER BY [TaskId] DESC). |
| CopyObjectsInOneGroup | Execute Pipeline | Ejecute otra canalización para copiar objetos de un grupo. Los objetos que pertenecen a este lote se copiarán en paralelo. |


### <a name="metadatadrivencopytask_xxx_-bottomlevel"></a>MetadataDrivenCopyTask_xxx_ BottomLevel
Esta canalización copiará objetos de un grupo. Los objetos que pertenecen a este lote se copiarán en paralelo.  

#### <a name="parameters"></a>Parámetros
| Nombre del parámetro | Descripción | 
|:--- |:--- |
| ObjectsPerGroupToCopy | Número de objetos que se copian en el grupo actual. | 
| ConnectionControlTableName | Nombre de la tabla de control de conexión. | 
| windowStart | Se usaba para pasar el tiempo actual del desencadenador a la canalización con el fin de rellenar la ruta de acceso de la carpeta dinámica si el usuario lo configura. | 

#### <a name="activities"></a>Actividades
| Nombre de la actividad | Tipo de actividad | Descripción |
|:--- |:--- |:--- |
| ListObjectsFromOneGroup | ForEach | Enumera los objetos de un grupo e itera cada uno de ellos en actividades de nivel inferior. |
| RouteJobsBasedOnLoadingBehavior | Conmutador | Compruebe el comportamiento de carga de cada objeto. Si es el caso predeterminado o FullLoad, realice la carga completa. Si se trata del caso DeltaLoad, realice una carga incremental a través de la columna de marca de agua para identificar los cambios. |
| FullLoadOneObject | Copiar | Tome una instantánea completa en este objeto y cópiela en el destino. |
| DeltaLoadOneObject | Copiar | Copie los datos modificados solo desde la última vez mediante la comparación del valor de la columna de marca de agua para identificar los cambios. |
| GetMaxWatermarkValue | Búsqueda | Consulte el objeto de origen para obtener el valor máximo de la columna de marca de agua. |
| UpdateWatermarkColumnValue | StoreProcedure | Reescriba el nuevo valor de marca de agua en la tabla de control que se usará la próxima vez. |

### <a name="known-limitations"></a>Limitaciones conocidas
- La herramienta copiar datos no admite la ingesta controlada por metadatos para copiar incrementalmente archivos nuevos solo actualmente. Pero puede traer sus propias canalizaciones parametrizadas para lograrlo.
- El nombre de IR, el tipo de base de datos y el tipo de formato de archivo no se pueden parametrizar en ADF. Por ejemplo, si desea ingerir datos de Oracle Server y SQL Server, necesitará dos canalizaciones con parámetros diferentes. Sin embargo, dos conjuntos de canalizaciones pueden compartir la tabla de control única. 
- OPENJSON se usa en scripts SQL generados por la herramienta Copiar datos. Si usa SQL Server para hospedar la tabla de control, debe ser SQL Server 2016 (13.x) y versiones posteriores para admitir la función OPENJSON.


## <a name="next-steps"></a>Pasos siguientes
Pruebe estos tutoriales que usan la herramienta Copy Data:

- [Inicio rápido: Creación de factoría de datos con la herramienta Copy Data](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Copia de datos en Azure mediante la herramienta Copy Data](tutorial-copy-data-tool.md) 
- [Tutorial: Copia de datos locales en Azure con la herramienta Copy Data](tutorial-hybrid-copy-data-tool.md)
