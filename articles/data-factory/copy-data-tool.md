---
title: Herramienta Copiar datos en Azure Data Factory
description: Se proporciona información sobre la herramienta Copy Data en la interfaz de usuario de Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: yexu
ms.openlocfilehash: 68bfa62817d971ce8c8127f44f43576431a499fc
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372010"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Herramienta Copy Data en Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La herramienta Copy Data de Azure Data Factory facilita y optimiza el proceso de ingerir datos en una instancia de Data Lake, que es normalmente uno de los primeros pasos en un escenario de integración de extremo a extremo.  Ahorra tiempo, en especial cuando se usa Azure Data Factory para ingerir datos de un origen de datos por primera vez. Algunas de las ventajas de usar esta herramienta son:

- Al usar la herramienta Copy Data de Azure Data Factory, no es necesario comprender las definiciones de Data Factory de servicios vinculados, conjuntos de datos, canalizaciones, actividades y desencadenadores. 
- El flujo de la herramienta Copy Data es intuitivo para la carga de datos en una instancia de Data Lake. La herramienta crea automáticamente todos los recursos de Data Factory necesarios para copiar datos del almacén de datos de origen seleccionado en el almacén de datos de destino/receptor seleccionado. 
- La herramienta Copy Data le ayuda a validar los datos que se ingieren en el momento de creación, lo que contribuye a evitar posibles errores al principio.
- Si necesita implementar lógica empresarial compleja para cargar datos en una instancia de Data Lake, puede editar los recursos de Data Factory creados por la herramienta Copy Data mediante la opción de creación por actividad de la interfaz de usuario de Data Factory. 

En la tabla siguiente se proporcionan instrucciones sobre cuándo usar la herramienta Copy Data frente a la creación por actividad de la interfaz de usuario de Data Factory: 

| Herramienta Copiar datos | Creación por actividad (actividad de copia) |
| -------------- | -------------------------------------- |
| Quiere crear fácilmente una tarea de carga de datos sin saber nada sobre las entidades de Azure Data Factory (servicios vinculados, conjuntos de datos, canalizaciones, etc.) | Quiere implementar una lógica compleja y flexible para cargar datos en Data Lake. |
| Quiere cargar rápidamente un gran número de artefactos de datos en una instancia de Data Lake. | Quiere encadenar la actividad de copia con las actividades posteriores de limpieza o de procesamiento de los datos. |

Para iniciar la herramienta Copy Data, haga clic en el icono **Copy Data** de la página principal de la factoría de datos.

![Página de inicio: vínculo a la herramienta Copy Data](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Flujo intuitivo para cargar datos en una instancia de Data Lake
Esta herramienta le permite mover fácilmente datos de una amplia variedad de orígenes a destinos en cuestión de minutos con un flujo intuitivo:  

1. Configure los valores del **origen**.
2. Configure los valores del **destino**. 
3. Realice la **configuración avanzada** para la operación de copia, como la asignación de columnas, la configuración de rendimiento y la configuración de tolerancia a errores. 
4. Especifique una **programación** para la tarea de carga de datos. 
5. Revise el **resumen** de entidades de Data Factory que se van a crear. 
6. **Edite** la canalización para actualizar la configuración de la actividad de copia según sea necesario. 

   La herramienta está diseñada pensando en los macrodatos desde el principio, con compatibilidad para diversos datos y tipos de objeto. Puede usarla para mover cientos de carpetas, archivos o tablas. La herramienta admite la vista previa automática de datos, captura de esquema y asignación automática y también el filtrado de datos.

![Herramienta Copiar datos](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Vista previa de datos automática
Puede obtener una vista previa de parte de los datos del almacén de datos de origen seleccionado, que le permite validar los datos que se van a copiar. Además, si los datos de origen están en un archivo de texto, la herramienta Copy Data analiza dicho archivo para detectar automáticamente los delimitadores de fila y columna y el esquema.

![Configuración de archivo](./media/copy-data-tool/file-format-settings.png)

Después de la detección, seleccione **Vista previa de los datos**:

![Configuración de archivo detectado y vista previa](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Captura de esquema y asignación automática
En muchos casos, el esquema del origen de datos no puede ser igual que el esquema de destino de los datos. En este escenario, es preciso asignar columnas del esquema de origen a columnas del esquema de destino.

La herramienta Copy Data supervisa y aprende su comportamiento cuando asigna columnas entre los almacenes de origen y destino. Después de seleccionar una o varias columnas del almacén de datos de origen y asignarlas al esquema de destino, la herramienta Copy Data comienza a analizar el patrón de los pares de columnas seleccionados de ambos lados. A continuación, se aplica el mismo patrón al resto de las columnas. Por lo tanto, verá que todas las columnas se han asignado al destino de la forma elegida después de varios clics.  Si no está satisfecho con la opción de asignación de columna proporcionada por la herramienta Copy Data, puede omitirla y continuar con la asignación manual de las columnas. Mientras tanto, la herramienta Copy Data aprende y actualiza el patrón constantemente y, en última instancia, alcanza el patrón correcto para la asignación de columnas que desea conseguir. 

> [!NOTE]
> Al copiar datos de SQL Server o Azure SQL Database a Azure Synapse Analytics, si la tabla no existe en el almacén de destino, la herramienta Copy Data admite la creación de la tabla automáticamente mediante el esquema de origen. 

## <a name="filter-data"></a>Filtrado de los datos
Puede filtrar los datos de origen para seleccionar solo aquellos que deben copiarse en el almacén de datos receptor. El filtrado reduce el volumen de los datos se copian en el almacén de datos del receptor, por lo que mejora el rendimiento de la operación de copia. La herramienta Copy Data proporciona una manera flexible de filtrar los datos en una base de datos relacional mediante el lenguaje de consulta SQL o los archivos de una carpeta de blobs de Azure. 

### <a name="filter-data-in-a-database"></a>Filtrado de datos en una base de datos
En la siguiente captura de pantalla se muestra una consulta SQL para filtrar los datos.

![Filtrado de datos en una base de datos](./media/copy-data-tool/filter-data-in-database.png)

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

![Filtrado de archivos o carpetas](./media/copy-data-tool/filter-file-or-folder.png)

La herramienta Copy Data genera parámetros con expresiones, funciones y variables del sistema que se pueden usar para representar los valores de {year}, {month}, {day}, {hour} y {minute} al crear una canalización.

## <a name="scheduling-options"></a>Opciones de programación
Puede ejecutar la operación de copia una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden usar para los conectores en diferentes entornos, ya sean locales y en la nube, así como para el escritorio local. 

Una operación de copia única permite el movimiento de datos desde un origen a un destino solamente una vez. Se aplica a los datos de cualquier tamaño y en cualquier formato admitido. La copia programada permite copiar datos con una recurrencia especificada. Puede utilizar las opciones avanzadas (como el reintento, el tiempo de espera y las alertas) para configurar la copia programada.

![Opciones de programación](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Pasos siguientes
Pruebe estos tutoriales que usan la herramienta Copy Data:

- [Inicio rápido: Creación de factoría de datos con la herramienta Copy Data](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Copia de datos en Azure mediante la herramienta Copy Data](tutorial-copy-data-tool.md) 
- [Tutorial: Copia de datos locales en Azure con la herramienta Copy Data](tutorial-hybrid-copy-data-tool.md)
