---
title: Herramienta Copiar datos
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre la herramienta Copiar datos de la interfaz de usuario de Azure Data Factory y Synapse Analytics.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: yexu
ms.openlocfilehash: d4ae44d3c3121ff5cc0240ea9dac0ed11e1580ce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760322"
---
# <a name="copy-data-tool-in-azure-data-factory-and-synapse-analytics"></a>Herramienta Copiar datos en Azure Data Factory y Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La herramienta Copiar datos facilita y optimiza el proceso de ingerir datos en una instancia de Data Lake, que es normalmente uno de los primeros pasos en un escenario de integración de datos de extremo a extremo.  Ahorra tiempo, en especial cuando usa el servicio para ingerir datos de un origen de datos por primera vez. Algunas de las ventajas de usar esta herramienta son:

- Al usar la herramienta Copiar datos, no es necesario comprender las definiciones de servicio de servicios vinculados, conjuntos de datos, canalizaciones, actividades y desencadenadores. 
- El flujo de la herramienta Copy Data es intuitivo para la carga de datos en una instancia de Data Lake. La herramienta crea automáticamente todos los recursos para copiar datos del almacén de datos de origen seleccionado en el almacén de datos de destino/receptor seleccionado. 
- La herramienta Copy Data le ayuda a validar los datos que se ingieren en el momento de creación, lo que contribuye a evitar posibles errores al principio.
- Si necesita implementar lógica empresarial compleja para cargar datos en una instancia de Data Lake, puede editar los recursos creados por la herramienta Copiar datos mediante la opción de creación por actividad de la interfaz de usuario. 

En la tabla siguiente se proporcionan instrucciones sobre cuándo usar la herramienta Copiar datos frente a la creación por actividad de la interfaz de usuario: 

| Herramienta Copiar datos | Creación por actividad (actividad de copia) |
| -------------- | -------------------------------------- |
| Quiere crear fácilmente una tarea de carga de datos sin saber nada sobre las entidades (servicios vinculados, conjuntos de datos, canalizaciones, etc.) | Quiere implementar una lógica compleja y flexible para cargar datos en Data Lake. |
| Quiere cargar rápidamente un gran número de artefactos de datos en una instancia de Data Lake. | Quiere encadenar la actividad de copia con las actividades posteriores de limpieza o de procesamiento de los datos. |

Para iniciar la herramienta Copiar datos, haga clic en el icono **Ingerir** de la página principal de la interfaz de usuario de Data Factory o Synapse Studio.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Captura de pantalla que muestra el vínculo a la herramienta Copiar datos en la página principal":::
# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="./media/doc-common-process/get-started-page-synapse.png" alt-text="Captura de pantalla que muestra el vínculo a la herramienta Copiar datos en la página principal":::

---
Tras abrir la herramienta Copiar datos, verá dos tipos de tareas: una es una **tarea de copia integrada** y la otra, una **tarea de copia basada en metadatos**. Con la tarea de copia integrada se crea una canalización en cinco minutos para replicar datos sin tener que conocer ninguna entidad.  Por su parte, la tarea de copia basada en metadatos simplifica el proceso de creación de canalizaciones parametrizadas y de una tabla de control externa que permitan controlar las operaciones de copia de grandes cantidades de objetos (por ejemplo, miles de tablas) a escala. Encontrará más detalles al respecto en [Creación de canalizaciones de copia de datos a gran escala con un enfoque basado en metadatos](copy-data-tool-metadata-driven.md).

## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Flujo intuitivo para cargar datos en una instancia de Data Lake
Esta herramienta le permite mover fácilmente datos de una amplia variedad de orígenes a destinos en cuestión de minutos con un flujo intuitivo:  

1. Configure los valores del **origen**.
2. Configure los valores del **destino**. 
3. Realice la **configuración avanzada** para la operación de copia, como la asignación de columnas, la configuración de rendimiento y la configuración de tolerancia a errores. 
4. Especifique una **programación** para la tarea de carga de datos. 
5. Revise el **resumen** de entidades que se van a crear. 
6. **Edite** la canalización para actualizar la configuración de la actividad de copia según sea necesario. 

   La herramienta está diseñada pensando en los macrodatos desde el principio, con compatibilidad para diversos datos y tipos de objeto. Puede usarla para mover cientos de carpetas, archivos o tablas. La herramienta admite la vista previa automática de datos, captura de esquema y asignación automática y también el filtrado de datos.

:::image type="content" source="./media/copy-data-tool/copy-data-tool.png" alt-text="Herramienta Copiar datos":::

## <a name="automatic-data-preview"></a>Vista previa de datos automática
Puede obtener una vista previa de parte de los datos del almacén de datos de origen seleccionado, que le permite validar los datos que se van a copiar. Además, si los datos de origen están en un archivo de texto, la herramienta Copy Data analiza dicho archivo para detectar automáticamente los delimitadores de fila y columna y el esquema.

:::image type="content" source="./media/copy-data-tool/file-format-settings.png" alt-text="Configuración de archivo":::

Después de la detección, seleccione **Vista previa de los datos**:

:::image type="content" source="./media/copy-data-tool/after-detection.png" alt-text="Configuración de archivo detectado y vista previa":::

## <a name="schema-capture-and-automatic-mapping"></a>Captura de esquema y asignación automática
En muchos casos, el esquema del origen de datos no puede ser igual que el esquema de destino de los datos. En este escenario, es preciso asignar columnas del esquema de origen a columnas del esquema de destino.

La herramienta Copy Data supervisa y aprende su comportamiento cuando asigna columnas entre los almacenes de origen y destino. Después de seleccionar una o varias columnas del almacén de datos de origen y asignarlas al esquema de destino, la herramienta Copy Data comienza a analizar el patrón de los pares de columnas seleccionados de ambos lados. A continuación, se aplica el mismo patrón al resto de las columnas. Por lo tanto, verá que todas las columnas se han asignado al destino de la forma elegida después de varios clics.  Si no está satisfecho con la opción de asignación de columna proporcionada por la herramienta Copy Data, puede omitirla y continuar con la asignación manual de las columnas. Mientras tanto, la herramienta Copy Data aprende y actualiza el patrón constantemente y, en última instancia, alcanza el patrón correcto para la asignación de columnas que desea conseguir. 

> [!NOTE]
> Al copiar datos de SQL Server o Azure SQL Database a Azure Synapse Analytics, si la tabla no existe en el almacén de destino, la herramienta Copy Data admite la creación de la tabla automáticamente mediante el esquema de origen. 

## <a name="filter-data"></a>Filtrado de los datos
Puede filtrar los datos de origen para seleccionar solo aquellos que deben copiarse en el almacén de datos receptor. El filtrado reduce el volumen de los datos se copian en el almacén de datos del receptor, por lo que mejora el rendimiento de la operación de copia. La herramienta Copy Data proporciona una manera flexible de filtrar los datos en una base de datos relacional mediante el lenguaje de consulta SQL o los archivos de una carpeta de blobs de Azure. 

### <a name="filter-data-in-a-database"></a>Filtrado de datos en una base de datos
En la siguiente captura de pantalla se muestra una consulta SQL para filtrar los datos.

:::image type="content" source="./media/copy-data-tool/filter-data-in-database.png" alt-text="Filtrado de datos en una base de datos":::

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrar datos en una carpeta de blobs de Azure
Puede usar variables en la ruta de acceso a la carpeta para copiar datos de una carpeta. Las variables admitidas son: **{year}** , **{month}** , **{day}** , **{hour}** y **{minute}** . Por ejemplo: carpetaDeEntrada/{year}/{month}/{day}. 

Supongamos que tiene carpetas de entrada con el siguiente formato: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Haga clic en el botón **Examinar** de **Archivo o carpeta**, vaya a una de estas carpetas (por ejemplo, 2016->03->01->02) y haga clic en **Elegir**. En el cuadro de texto, debe aparecer 2016/03/01/02. 

A continuación, sustituya **2016** por **{year}** , **03** por **{month}** , **01** por **{day}** y **02** por **{hour}** y presione la tecla **Tabulación**. Al seleccionar **Incremental load: time-partitioned folder/file names** (Carga incremental: nombres de archivos o carpetas con particiones de tiempo) en la sección **File loading behavior** (Comportamiento de la carga de archivos) y seleccionar **Programación** o **Tumbling window** (Ventana de saltos de tamaño constante) en la página **Propiedades**, debería ver listas desplegables para seleccionar el formato de estas cuatro variables:

:::image type="content" source="./media/copy-data-tool/filter-file-or-folder.png" alt-text="Filtrado de archivos o carpetas":::

La herramienta Copy Data genera parámetros con expresiones, funciones y variables del sistema que se pueden usar para representar los valores de {year}, {month}, {day}, {hour} y {minute} al crear una canalización.

## <a name="scheduling-options"></a>Opciones de programación
Puede ejecutar la operación de copia una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden usar para los conectores en diferentes entornos, ya sean locales y en la nube, así como para el escritorio local. 

Una operación de copia única permite el movimiento de datos desde un origen a un destino solamente una vez. Se aplica a los datos de cualquier tamaño y en cualquier formato admitido. La copia programada permite copiar datos con una recurrencia especificada. Puede utilizar las opciones avanzadas (como el reintento, el tiempo de espera y las alertas) para configurar la copia programada.

:::image type="content" source="./media/copy-data-tool/scheduling-options.png" alt-text="Opciones de programación":::


## <a name="next-steps"></a>Pasos siguientes
Pruebe estos tutoriales que usan la herramienta Copy Data:

- [Inicio rápido: Creación de factoría de datos con la herramienta Copy Data](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Copia de datos en Azure mediante la herramienta Copy Data](tutorial-copy-data-tool.md) 
- [Tutorial: Copia de datos locales en Azure con la herramienta Copy Data](tutorial-hybrid-copy-data-tool.md)
