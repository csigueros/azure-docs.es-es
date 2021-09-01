---
title: Copia de datos desde Azure Data Lake Storage Gen1 a Gen2
description: Uso de Azure Data Factory para copiar datos de Azure Data Lake Storage Gen1 en Gen2
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/06/2021
ms.openlocfilehash: 5538000573a70340b576e79b3e1dcab9367e7d89
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201925"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copia de datos de Azure Data Lake Storage Gen1 en Gen2 con Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 es un conjunto de funciones dedicadas al análisis de macrodatos compilado en [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Puede usarlo para interactuar con los datos usando el paradigma de sistema de archivos o el de almacenamiento de objetos.

Si actualmente usa Azure Data Lake Storage Gen1, puede evaluar Azure Data Lake Storage Gen2 mediante la copia de datos de Data Lake Storage Gen1 en Gen2 con Azure Data Factory.

Azure Data Factory es un servicio de integración de datos en la nube totalmente administrado. Puede utilizar el servicio para rellenar el lago con datos de un amplio conjunto de almacenes de datos locales y basados en la nube y, de este modo, ahorrar tiempo cuando compile las soluciones de análisis. Para obtener una lista de conectores admitidos, vea la tabla de [Almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory ofrece una solución de movimiento de datos administrados y de escalabilidad horizontal. Debido a la arquitectura con escalabilidad horizontal de Data Factory, puede ingerir datos con un alto rendimiento. Para obtener más información, vea el artículo [Copiar rendimiento de actividad](copy-activity-performance.md).

En este artículo se muestra cómo utilizar la herramienta Copiar datos de Data Factory para copiar datos de Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Puede seguir los mismos pasos para copiar datos de otros tipos de almacenes de datos.

## <a name="prerequisites"></a>Prerrequisitos

* Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Cuenta de Azure Data Lake Storage Gen1 con datos en ella.
* Cuenta de Azure Storage con Data Lake Storage Gen2 habilitado. Si no tiene una cuenta de Storage, [debe crear una](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, seleccione **Crear un recurso** > **Datos y análisis** > **Data Factory**.
   
   ![Captura de pantalla que muestra la selección de Data Factory en el panel Nuevo.](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. En la página **Nueva factoría de datos**, proporcione los valores de los campos que se muestran en la imagen siguiente: 
      
   ![Captura de pantalla que muestra la página Nueva factoría de datos.](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Name**: escriba un nombre único global para la factoría de datos de Azure. Si recibe el error "El nombre de la factoría de datos \"LoadADLSDemo\" no está disponible", escriba uno diferente. Por ejemplo, utilice _**suNombre**_ **ADFTutorialDataFactory**. Cree la factoría de datos otra vez. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Data Factory: reglas de nomenclatura](naming-rules.md).
    * **Suscripción**: seleccione la suscripción de Azure donde desea crear la factoría de datos. 
    * **Grupo de recursos**: Seleccione un grupo de recursos existente de la lista desplegable. También puede seleccionar la opción **Crear** y escribir el nombre de un grupo de recursos. Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md). 
    * **Versión**: seleccione **V2**.
    * **Ubicación**: Seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos que las factorías de datos usan pueden estar en otras ubicaciones y regiones. 

3. Seleccione **Crear**.
4. Una vez completada la creación, vaya a la factoría de datos. Verá la página principal de **Factoría de datos**, tal y como se muestra en la siguiente imagen: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

5. Seleccione **Open** (Abrir) en el icono **Open Azure Data Factory Studio** (Abrir Azure Data Factory Studio) para iniciar la aplicación de integración de datos en una pestaña independiente.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carga de datos en Azure Data Lake Storage Gen2

1. En la página principal, seleccione el icono **Ingest** (Ingerir) para iniciar la herramienta de copia de datos. 

   ![Captura de pantalla que muestra la página principal de ADF.](./media/doc-common-process/get-started-page.png )

2. En la página **Properties** (Propiedades), elija **Built-in copy task** (Tarea de copia integrada) en **Task type** (Tipo de tarea) y elija **Run once now** (Ejecutar una vez ahora) en **Task cadence or task schedule** (Cadencia de tareas o programación de tareas). A continuación, seleccione **Next** (Siguiente).

3. En la página **Source data store** (Almacén de datos de origen), haga clic en **+ New connection** (+ Nueva conexión).
    
4. Seleccione **Azure Data Lake Storage Gen1** en la galería de conectores y seleccione **Continuar**.
    
    ![Captura de pantalla que muestra la página de selección de la conexión de Azure Data Lake Storage Gen1.](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. En la página **New Connection (Azure Data Lake Storage Gen1)** (Nueva conexión [Azure Data Lake Storage Gen1]), siga estos pasos:
   1. Seleccione su instancia de Data Lake Storage Gen1 para el nombre de cuenta y especifique o valide el **inquilino**.
   1. Seleccione **Prueba de conexión** para validar la configuración. Seleccione **Crear**.
  
   > [!IMPORTANT]
   > En este tutorial se utilizará una identidad administrada para recursos de Azure con el fin de autenticar la cuenta de Azure Data Lake Storage Gen1. Para conceder a la identidad administrada los permisos adecuados en Azure Data Lake Storage Gen1, siga [estas instrucciones](connector-azure-data-lake-store.md#managed-identity).
   
   ![Captura de pantalla que muestra la configuración de la conexión de Azure Data Lake Storage Gen1.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. En la página **Source data store** (Almacén de datos de origen), realice los pasos siguientes: 
    1. Seleccione la conexión recién creada en la sección **Connection** (Conexión).
    1. En **File or folder** (Archivo o carpeta), vaya a la carpeta y el archivo que quiere copiar. Seleccione la carpeta o el archivo y, después, **OK** (Aceptar).
    1. Elija el comportamiento de copia; para ello, seleccione las opciones **Recursively** (De forma recursiva) y **Binary copy** (Copia binaria). Seleccione **Siguiente**.
    
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png" alt-text="Captura de pantalla que muestra la página del almacén de datos de origen.":::
    
7. En la página **Destination data store** (Almacén de datos de destino), seleccione **+ New connection** > **Azure Data Lake Storage Gen2** > **Continue** (+ Nueva conexión > Azure Data Lake Storage Gen2 > Continuar).

    ![Captura de pantalla que muestra la página de selección de la conexión de Azure Data Lake Storage Gen2.](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-store-page-adls-gen2.png)

8. En la página **New Connection (Azure Data Lake Storage Gen2)** (Nueva conexión [Azure Data Lake Storage Gen2]), siga estos pasos:
   1. Seleccione la cuenta habilitada para Data Lake Storage Gen2 en la lista desplegable **Nombre de la cuenta de almacenamiento**.
   1. Seleccione **Create** (Crear) para crear la conexión. 

   ![Captura de pantalla que muestra la configuración de la conexión de Azure Data Lake Storage Gen2.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. En la página **Destination data store** (Almacén de datos de destino), realice los pasos siguientes: 
    1. Seleccione la conexión recién creada en el bloque **Conexión**. 
    1. En la página **Folder path** (Ruta de la carpeta) escriba **copyfromadlsgen1** como nombre de la carpeta de salida y seleccione **Next** (Siguiente). Si no existen el sistema de archivos de Azure Data Lake Storage Gen2 correspondiente y las subcarpetas, Data Factory los crea durante la copia.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-store-page.png" alt-text="Captura de pantalla que muestra la página de almacén de datos de destino.":::

10. En la página **Settings** (Configuración), especifique **CopyFromADLSGen1ToGen2** en el campo **Task name** (Nombre de la tarea) y seleccione **Next** (Siguiente) para usar la configuración predeterminada.


11. En la página **Resumen**, revise la configuración y seleccione **Siguiente**.

    ![Captura de pantalla de la página de resumen.](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)

12. En la página **Implementación**, seleccione **Supervisión** para supervisar la canalización.

    ![Captura de pantalla que muestra la página de implementación.](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)

13. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. La columna **Pipeline name** (Nombre de canalización) incluye los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización.

    ![Captura de pantalla que muestra la página de supervisión de ejecuciones de canalización.](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. Para ver las ejecuciones de actividad asociadas a la ejecución de la canalización, seleccione el vínculo en la columna **Pipeline name** (Nombre de canalización). Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **All pipeline runs** (Todas las ejecuciones de la canalización) en el menú de la ruta de navegación en la parte superior. Seleccione **Refresh** (Actualizar) para actualizar la lista. 

    ![Captura de pantalla que muestra la página de supervisión de ejecuciones de actividad.](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Para supervisar los detalles de la ejecución de cada actividad de copia, seleccione el vínculo con un icono de gafas **Details** (Detalles) en la columna **Activity name** (Nombre de actividad) en la vista de supervisión de la actividad. Puede supervisar detalles como el volumen de datos copiados desde el origen al receptor, el rendimiento de los datos, los pasos de ejecución con su duración correspondiente y las configuraciones que se utilizan.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png" alt-text="Captura de pantalla que muestra los detalles de la ejecución de la actividad.":::

16. Verifique que los datos se copian en la cuenta de Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Procedimientos recomendados

Para evaluar la actualización de Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2 en general, vea [Actualizar sus soluciones de análisis de macrodatos de Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). Las secciones siguientes presentan procedimientos recomendados para usar Data Factory para una actualización de datos de Data Lake Storage Gen1 a Data Lake Storage Gen2.

### <a name="historical-data-copy"></a>Copia de datos históricos

#### <a name="performance-tuning-by-proof-of-concept"></a>Ajuste del rendimiento por prueba de concepto

Utilice una prueba de concepto para comprobar la solución de un extremo a otro y probar el rendimiento de la copia en su entorno. Pasos principales de la prueba de concepto: 

1. Crear una canalización de Data Factory con una actividad de copia única para copiar varios TB de datos de Data Lake Storage Gen1 a Data Lake Storage Gen2 con el fin de obtener una base de referencia de rendimiento de copia. Empiece con las [unidades de integración de datos (DIU)](copy-activity-performance-features.md#data-integration-units) en 128. Se recomienda establecer la opción de [copia paralela](copy-activity-performance-features.md#parallel-copy) en **vacío (valor predeterminado)** .
2. En función del rendimiento de copia que se obtenga en el paso 1, calcular el tiempo estimado necesario para toda la migración de datos. Si considera que el rendimiento de copia no es lo suficientemente bueno, identifique y resuelva los cuellos de botella de rendimiento siguiendo los [pasos de optimización del rendimiento](copy-activity-performance.md#performance-tuning-steps).
3. Si ha alcanzado el máximo rendimiento de una sola actividad de copia, pero aún no ha llegado a los límites superiores de rendimiento de su entorno, puede ejecutar varias actividades de copia en paralelo. Cada actividad de copia se puede configurar para copiar una partición a la vez, de modo que varias actividades de copia puedan copiar datos de una sola cuenta de Data Lake Storage Gen1 de forma simultánea. Para hacer particiones de los archivos, se utiliza **rango de nombres: listAfter/listBefore** en la [propiedad de la actividad de copia](connector-azure-data-lake-store.md#copy-activity-properties).

Si el tamaño total de los datos en Data Lake Storage Gen1 es inferior a 30 TB y el número de archivos es menos de 1 millón, puede copiar todos los datos en una ejecución de actividad de copia única. Si tiene una mayor cantidad de datos para copiar, o si quiere flexibilidad para administrar la migración de datos por lotes y hacer que cada uno de estos se complete en un periodo de tiempo determinado, particione los datos. La creación de particiones también reduce el riesgo de se produzca una incidencia inesperada. 


#### <a name="network-bandwidth-and-storage-io"></a>E/S de almacenamiento y ancho de banda de red 

Si ve un número significativo de errores de limitación en la [supervisión de la actividad de copia](copy-activity-monitoring.md#monitor-visually), esto indica que ha alcanzado el límite de capacidad de la cuenta de almacenamiento. ADF volverá a intentar automáticamente superar cada error de limitación para asegurarse de que no se perderá ningún dato, pero un número elevado de reintentos también afecta al rendimiento de la copia. En tal caso, se recomienda reducir el número de actividades de copia que se ejecutan de forma simultánea para evitar que se produzcan muchos errores de limitación. Si ha estado usando una actividad de copia única para copiar datos, se recomienda reducir el número de [unidades de integración de datos (DIU)](copy-activity-performance-features.md#data-integration-units).


### <a name="incremental-copy"></a>Copia incremental 

Puede usar varios métodos para cargar únicamente los archivos nuevos o actualizados desde Data Lake Storage Gen1:

- Cargar archivos nuevos o actualizados mediante una carpeta o nombres de archivo con particiones por hora. Un ejemplo es /2019/05/13/*.
- Cargar archivos nuevos o actualizados por LastModifiedDate. Si va a copiar grandes cantidades de archivos, realice primero particiones para evitar un bajo rendimiento de copia debido a que la actividad de copia única analiza toda la cuenta de Data Lake Storage Gen1 para identificar nuevos archivos. 
- Identificar archivos nuevos o actualizados mediante cualquier herramienta o solución de terceros. Después, pasar el nombre de archivo o carpeta a la canalización de Data Factory a través de un parámetro, o una tabla o archivo. 

La frecuencia adecuada para realizar una carga incremental depende del número total de archivos en Azure Data Lake Storage Gen1 y del volumen de archivos nuevos o actualizados que se van a cargar cada vez. 


### <a name="preserve-acls"></a>Conservación de las ACL

Si quiere replicar las ACL junto con los archivos de datos cuando se actualiza de Data Lake Storage Gen1 a Data Lake Storage Gen2, vea [Conservar las ACL de Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="permissions"></a>Permisos 

En Data Factory, el [conector de Data Lake Storage Gen1](connector-azure-data-lake-store.md) admite la entidad de servicio y la identidad administrada para las autenticaciones de recursos de Azure. El [conector de Data Lake Storage Gen2](connector-azure-data-lake-storage.md) admite la clave de cuenta, la entidad de servicio y la identidad administrada para las autenticaciones de recursos de Azure. Para hacer que Data Factory pueda navegar y copiar todos los archivos o listas de control de acceso (ACL) que necesita, conceda los permisos suficientemente elevados para la cuenta a la que se proporciona acceso, lectura o escritura de todos los archivos y establezca las ACL si quiere. Conceda un rol de superusuario o propietario durante la migración. 


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción de la actividad de copia](copy-activity-overview.md)
> [Conector de Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
> [Conector de Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)