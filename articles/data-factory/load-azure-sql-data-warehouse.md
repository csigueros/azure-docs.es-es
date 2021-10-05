---
title: Carga de datos en Azure Synapse Analytics
titleSuffix: Azure Data Factory & Azure Synapse
description: Use una canalización de Azure Data Factory o Azure Synapse para copiar datos en Azure Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e91d2dbae4f73e99a5e341330c0841e8173cedc8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819911"
---
# <a name="load-data-into-azure-synapse-analytics-using-azure-data-factory-or-a-synapse-pipeline"></a>Carga de datos en Azure Synapse Analytics mediante una canalización de Azure Data Factory o Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) es una base de datos de escalabilidad horizontal y basada en la nube capaz de procesar volúmenes masivos de datos (tanto relacionales como no relacionales). Basada en la arquitectura de procesamiento paralelo masivo (MPP), está mejorada para controlar las cargas de trabajo empresariales. Ofrece elasticidad en la nube con la flexibilidad para escalar almacenamiento y proceso de forma independiente.

Comenzar a trabajar con Azure Synapse Analytics es ahora más fácil que nunca. Azure Data Factory y su característica de canalizaciones equivalente dentro de Azure Synapse proporcionan un servicio de integración de datos basado en la nube totalmente administrado. Puede usar el servicio para rellenar una instancia de Azure Synapse Analytics con los datos del sistema actual y ahorrar tiempo al crear las soluciones de análisis.

Las canalizaciones de Azure Data Factory y Synapse ofrecen los siguientes beneficios a la hora de cargar datos en Azure Synapse Analytics:

* **Fácil de configurar**: con un asistente intuitivo en cinco pasos sin necesidad de scripting.
* **Amplia compatibilidad para el almacenamiento de datos**: compatibilidad integrada para un amplio conjunto de almacenes de datos tanto locales como en la nube. Para una lista detallada, consulte la tabla de [almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro y compatible**: los datos se transfieren a través de HTTPS o ExpressRoute. La presencia del servicio global garantiza que los datos nunca abandonan el límite geográfico.
* **Rendimiento sin precedentes mediante PolyBase**: Polybase es la forma más eficaz de mover datos a Azure Synapse Analytics. Mediante la característica de blob de almacenamiento provisional, puede alcanzar velocidades de carga altas para todos los tipos de almacenes de datos, incluido Azure Blob Storage y Data Lake Store. (Polybase es compatible con Azure Blob Storage y Azure Data Lake Store de forma predeterminada). Para más información, consulte el [rendimiento de la actividad de copia](copy-activity-performance.md).

En este artículo se muestra cómo usar la herramienta Copiar datos para _cargar datos de Azure SQL Database en Azure Synapse Analytics_. Puede seguir los mismos pasos para copiar datos de otros tipos de almacenes de datos.

> [!NOTE]
> Para obtener más información, vea [Copia de datos en o desde Azure Synapse Analytics](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción de Azure: Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Azure Synapse Analytics: Este almacén de datos contiene los datos que se copian de SQL Database. Si no tiene una instancia de Azure Synapse Analytics, consulte las instrucciones de [Creación de una instancia de Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md).
* Azure SQL Database: este tutorial copia los datos del conjunto de datos de ejemplo Adventure Works LT en Azure SQL Database. Puede crear esta base de datos de ejemplo en SQL Database si sigue las instrucciones que aparecen en [Creación de una base de datos de ejemplo en Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Cuenta de almacenamiento de Azure: Azure Storage se usa como blob de _almacenamiento provisional_ en la operación de copia masiva. Si no dispone de una cuenta de almacenamiento de Azure, consulte las instrucciones de [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Crear una factoría de datos

> [!NOTE]
> Puede omitir la creación de una nueva factoría de datos si quiere usar la característica de canalizaciones del área de trabajo de Synapse existente para cargar los datos.  Azure Synapse inserta la funcionalidad de Azure Data Factory en su característica de canalizaciones.

1. En el menú de la izquierda, seleccione **Crear un recurso** > **Datos y análisis** > **Data Factory**:

2. En la página **Nueva factoría de datos**, proporcione valores para los siguientes elementos:

    * **Name**: Escriba *LoadSQLDWDemo* como nombre. El nombre de la factoría de datos debe ser *globalmente único. Si recibe el error "El nombre de la factoría de datos 'LoadSQLDWDemo' no está disponible", escriba otro nombre. Por ejemplo, podría utilizar el nombre _**suNombre**_**ADFTutorialDataFactory**. Intente crear de nuevo la factoría de datos. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Data Factory: reglas de nomenclatura](naming-rules.md).
    * **Suscripción**: seleccione la suscripción de Azure donde desea crear la factoría de datos. 
    * **Grupo de recursos**: seleccione un grupo de recursos existente en la lista desplegable o seleccione la opción **Crear nuevo** y escriba el nombre de un grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).  
    * **Versión**: seleccione **V2**.
    * **Ubicación**: Seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos que las factorías de datos usan pueden estar en otras ubicaciones y regiones. Estos almacenes de datos incluyen Azure Data Lake Store, Azure Storage, Azure SQL Database, etc.

3. Seleccione **Crear**.
4. Una vez completada la creación, vaya a la factoría de datos. Verá la página principal de **Factoría de datos**, tal y como se muestra en la siguiente imagen:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

   Seleccione **Abrir** en el icono **Open Azure Data Factory Studio** (Abrir Azure Data Factory Studio) para iniciar la aplicación de integración de datos en una pestaña independiente.

## <a name="load-data-into-azure-synapse-analytics"></a>Carga de datos en Azure Synapse Analytics

1. En la página principal de Azure Data Factory o el área de trabajo de Azure Synapse, seleccione el icono **Ingerir** para iniciar la herramienta Copiar datos.  Luego elija **Built-in copy task** (Tarea de copia integrada).

2. En la página **Propiedades**, elija **Built-in copy task** (Tarea de copia integrada) en **Tipo de tarea** y seleccione **Siguiente**.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png" alt-text="Página de propiedades":::

3. En la página **Almacén de datos de origen**, realice los pasos siguientes:
    >[!TIP]
    >En este tutorial, usará *Autenticación de SQL* como tipo de autenticación para el almacén de datos de origen, pero puede elegir otros métodos de autenticación compatibles: *Entidad de servicio* e *Identidad administrada* si es necesario. Consulte las secciones correspondientes en [este artículo](./connector-azure-sql-database.md#linked-service-properties) para más información.
    >Para almacenar los secretos de los almacenes de datos de forma segura, también se recomienda usar Azure Key Vault. Consulte [este artículo](./store-credentials-in-key-vault.md) para obtener instrucciones detalladas.

    1. Seleccione **+ Nueva conexión**.

    1. Seleccione **Azure SQL Database** en la galería y seleccione **Continuar**. Puede escribir "SQL" en el cuadro de búsqueda para filtrar los conectores.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png" alt-text="Seleccione Azure SQL DB":::
    
    1. En la página **Nueva conexión (Azure SQL Database)** , seleccione el nombre del servidor y el de la base de datos en la lista desplegable y especifique el nombre de usuario y la contraseña. Seleccione **Probar conexión** para validar la configuración y luego **Crear**.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png" alt-text="Configurar Azure SQL DB":::
    
    1. En la página **Almacén de datos de origen**, seleccione la conexión recién creada como origen en la sección **Conexión**.

    1. En la sección **Tablas de origen**, escriba **SalesLT** para filtrar las tablas. Elija el cuadro **Seleccionar todo** para usar todas las tablas para la copia y, a continuación, seleccione **Siguiente**.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/source-data-store-page.png" alt-text="Captura de pantalla que muestra la configuración de la página &quot;Almacén de datos de origen&quot;.":::

4. En la página **Aplicar filtro**, especifique la configuración o seleccione **Siguiente**. Puede obtener una vista previa de los datos y ver el esquema de los datos de entrada si selecciona el botón **Vista previa de los datos** en esta página. 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/apply-filter.png" alt-text=" Captura de pantalla que muestra la página &quot;Aplicar filtro&quot;.":::

5. En la página **Almacén de datos de destino**, realice los pasos siguientes:
    >[!TIP]
    >En este tutorial, usará *Autenticación de SQL* como tipo de autenticación para el almacén de datos de destino, pero puede elegir otros métodos de autenticación compatibles: *Entidad de servicio* e *Identidad administrada* si es necesario. Consulte las secciones correspondientes en [este artículo](./connector-azure-sql-data-warehouse.md#linked-service-properties) para más información.
    >Para almacenar los secretos de los almacenes de datos de forma segura, también se recomienda usar Azure Key Vault. Consulte [este artículo](./store-credentials-in-key-vault.md) para obtener instrucciones detalladas.

    1. Seleccione **+ Nueva conexión** para agregar una conexión.

    1. Seleccione **Azure Synapse Analytics** de la galería y seleccione **Continuar**.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png" alt-text="Selección de Azure Synapse Analytics":::

    1. En la página **Nueva conexión (Azure Synapse Analytics)** , seleccione el nombre del servidor y el de la base de datos en la lista desplegable y especifique el nombre de usuario y la contraseña. Seleccione **Probar conexión** para validar la configuración y luego **Crear**.

        :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png" alt-text="Configuración de Azure Synapse Analytics":::

    1. En la página **Almacén de datos de destino**, seleccione la conexión recién creada en la sección **Conexión**.

6. En la página **Asignación de tabla**, revise el contenido y seleccione **Siguiente**. Se muestra una asignación de tabla inteligente. Las tablas de origen se asignan a las tablas de destino en función de los nombres de tabla. Si no hay ninguna tabla de origen en el destino, el servicio crea una con el mismo nombre de manera predeterminada. También se puede asignar una tabla de origen a una tabla de destino existente.

   :::image type="content" source="./media/load-azure-sql-data-warehouse/destination-data-store-page.png" alt-text="Captura de pantalla que muestra la configuración de la página &quot;Almacén de datos de destino&quot;.":::

1. En la página **Asignación de columnas**, revise el contenido y seleccione **Siguiente**. La asignación inteligente de tabla se basa en el nombre de las columnas. Si permite que el servicio cree automáticamente las tablas, la conversión de tipos de datos puede producirse cuando haya incompatibilidades entre los almacenes de origen y de destino. Si hay una conversión de tipos de datos no compatibles entre las columnas de origen y de destino, verá un mensaje de error junto a la tabla correspondiente.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/schema-mapping.png" alt-text="Página de asignación de columnas":::

1. En la página **Settings** (Configuración), siga estos pasos:

    1. Especifique **CopyFromSQLToSQLDW** en el campo **Nombre de tarea**.
    1. En la sección **Staging settings** (Configuración de almacenamiento provisional), seleccione **+ Nuevo** para crear un nuevo almacenamiento provisional. El almacenamiento se usa para almacenar provisionalmente los datos antes de cargarlos en Azure Synapse Analytics mediante PolyBase. Una vez que se completa la copia, los datos provisionales en Azure Blob Storage se limpian automáticamente.

    1. En la página **New Linked Service** (Nuevo servicio vinculado), seleccione la cuenta de almacenamiento y luego **Crear** para implementar el servicio vinculado.

    1. Anule la selección de la opción **Use type default** (Usar tipo predeterminado) y luego seleccione **Siguiente**.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/configure-polybase.png" alt-text="Configurar PolyBase":::

8. En la página **Resumen**, revise la configuración y seleccione **Siguiente**.

9. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización (tarea). 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/deployment-complete-page.png" alt-text=" Captura de pantalla que muestra la página de implementación.":::
 
10. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. Cuando la ejecución de canalización finalice correctamente, seleccione el vínculo **CopyFromSQLToSQLDW** en la columna **Nombre de canalización** para ver los detalles de la ejecución de actividad o volver a ejecutar la canalización.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring.png" alt-text="Supervisión de las ejecuciones de canalización":::    

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring-synapse.png" alt-text="Supervisión de las ejecuciones de canalización":::   

--- 

12. Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **Todas las ejecuciones de la canalización**. Seleccione **Refresh** (Actualizar) para actualizar la lista.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/activity-monitoring.png" alt-text="Supervisión de las ejecuciones de actividad":::

1. Para supervisar los detalles de la ejecución de cada actividad de copia, seleccione el vínculo **Detalles** (icono de gafas) en **Nombre de actividad** en la vista de ejecuciones de actividad. Puede supervisar detalles como el volumen de datos copiados desde el origen al receptor, el rendimiento de los datos, los pasos de ejecución con su duración correspondiente y las configuraciones que se utilizan.

    :::image type="content" source="./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png" alt-text="Supervisión de los detalles de la ejecución de actividad en primer lugar":::

    :::image type="content" source="./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png" alt-text="Supervisión de los detalles de la ejecución de actividad en segundo lugar":::

## <a name="next-steps"></a>Pasos siguientes

Continúe al artículo siguiente para obtener información sobre la compatibilidad de Azure Synapse Analytics:

> [!div class="nextstepaction"]
>[Conector de Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
