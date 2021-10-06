---
title: Carga de datos de SAP Business Warehouse
titleSuffix: Azure Data Factory & Azure Synapse
description: Copia de datos de SAP Business Warehouse (BW) con Azure Data Factory o Synapse Analytics
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/09/2021
ms.openlocfilehash: 70db6de930ad60da87180fdf28555e542a458ecf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824611"
---
# <a name="copy-data-from-sap-business-warehouse-with-azure-data-factory-or-synapse-analytics"></a>Copia de datos de SAP Business Warehouse con Azure Data Factory o Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se muestra cómo usar las canalizaciones de Azure Data Factory o Synapse Analytics para copiar datos en Azure Data Lake Storage Gen2 desde una instancia de SAP Business Warehouse (BW) con Open Hub. Puede usar un proceso similar para copiar datos a otros [almacenes de datos receptores admitidos](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Para obtener información general sobre cómo copiar datos desde SAP BW, incluida la Integración de Open Hub con SAP BW y el flujo de extracción delta, consulte [Copia de datos desde SAP Business Warehouse con Open Hub en Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Requisitos previos

- **Área de trabajo de Azure Data Factory o Synapse**: si no tiene una, siga los pasos para [crear una factoría de datos](quickstart-create-data-factory-portal.md#create-a-data-factory) o [crear un área de trabajo de Synapse](../synapse-analytics/quickstart-create-workspace.md).

- **Destino de Open Hub para SAP BW (OHD) con el tipo de destino "Tabla de base de datos"** : para crear un OHD o comprobar que el OHD está configurado correctamente para la integración con el servicio, consulte la sección [Configuraciones de destino de Open Hub para SAP BW](#sap-bw-open-hub-destination-configurations) de este artículo.

- **El usuario de SAP BW necesita los siguientes permisos**:

  - Autorización para las llamadas a funciones remotas (RFC) y SAP BW.
  - Permisos para la actividad “Execute” del objeto de autorización **S_SDSAUTH**.

- **Un [entorno de ejecución de integración autohospedado (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) con el conector de SAP para .NET 3.0**. Siga estos pasos de configuración:

  1. Instale y registre el entorno de ejecución de integración autohospedado, versión 3.13 o posterior. (Este proceso se describe más adelante en este artículo).

  2. Descargue el [conector de SAP de 64 bits para Microsoft.NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) del sitio web de SAP e instálelo en la misma máquina que el IR autohospedado. Durante la instalación, asegúrese de que selecciona **Install Assemblies to GAC** (Instalar ensamblados en GAC) en el cuadro de diálogo **Optional setup steps** (Pasos de configuración opcionales), como se muestra en la siguiente imagen:

     :::image type="content" source="media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png" alt-text="Cuadro de diálogo de configuración del conector de SAP para .NET":::

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Realización de una copia completa de Open Hub para SAP BW

En Azure Portal, vaya al servicio. Seleccione **Abrir** en el icono **Open Azure Data Factory Studio** (Abrir Azure Data Factory Studio) u **Open Synapse Studio** (Abrir Synapse Studio) para abrir la interfaz de usuario del servicio en una pestaña independiente.

1. En la página principal, seleccione **Ingerir** para iniciar la herramienta Copiar datos.

2. En la página **Propiedades**, elija **Built-in copy task** (Tarea de copia integrada) en **Tipo de tarea** y elija **Run once now** (Ejecutar una vez ahora) en **Task cadence or task schedule** (Cadencia de tareas o programación de tareas). A continuación, seleccione **Siguiente**.

3. En la página **Almacén de datos de origen**, haga clic en **+ Nueva conexión**. Seleccione **SAP BW Open Hub** en la galería de conectores, y después seleccione **Continuar**. Para filtrar los conectores puede escribir **SAP** en el cuadro de búsqueda.

4. En la página **Nueva conexión (SAP BW Open Hub)** , siga estos pasos para crear una nueva conexión.

   1. Desde la lista **Connect via integration runtime** (Conectar a través del entorno de ejecución de integración), seleccione un IR autohospedado existente. O si no tiene ninguno, seleccione la creación de uno.

      Para crear un nuevo IR autohospedado, seleccione **+Nuevo** y, a continuación, seleccione **Autohospedado**. Escriba un **Nombre** y, a continuación, seleccione **Siguiente**. Seleccione **Configuración rápida** para instalar en el equipo actual, o siga en **Configuración manual** los pasos que se proporcionan.

      Como se mencionó en los [Requisitos previos](#prerequisites), asegúrese de que tiene el conector de SAP para Microsoft .NET 3.0 instalado en el mismo equipo en el que se ejecuta el IR autohospedado.

   2. Rellene los datos de **Nombre del servidor**, **Número del sistema**, **Id. de cliente,** **Idioma** (si no es **EN**), **Nombre de usuario** y **Contraseña** de SAP BW.

   3. Seleccione **Probar conexión** para validar la configuración y, después, seleccione **Crear**.

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png" alt-text="Página de creación de servicio vinculado de Open Hub para SAP BW":::

   4. En la página **Almacén de datos de origen**, seleccione la conexión recién creada en el bloque **Conexión**.

   5. En la sección de selección de los destinos de Open, explore los destinos de Open Hub que están disponibles en su SAP BW. Para obtener una vista previa de los datos de cada destino, puede seleccionar el botón de vista previa al final de cada fila. Seleccione el destino de Open Hub para copiar los datos y, a continuación, seleccione **Siguiente**.
   
   :::image type="content" source="./media/load-sap-bw-data/source-data-store-page.png" alt-text="Captura de pantalla que muestra la página Almacén de datos de origen.":::

5. Especifique un filtro, si lo necesita. Si el destino de Open Hub solo contiene datos de una única ejecución de un proceso de transferencia de datos (DTP) con un identificador de solicitud único, o está seguro de que el proceso de transferencia de datos ha finalizado y quiere copiar los datos, desactive la casilla **Exclude Last Request** (Excluir la última solicitud) en la sección **Opciones avanzadas**. Para obtener una vista previa de los datos, puede seleccionar el botón **Vista previa de los datos**.

   Más información acerca de estos valores en la sección [Configuraciones de destino de Open Hub para SAP BW](#sap-bw-open-hub-destination-configurations) de este artículo. Luego, seleccione **Siguiente**.

   :::image type="content" source="media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png" alt-text="Configuración del filtro de Open Hub para SAP BW":::

6. En la página **Almacén de datos de destino**, seleccione **+ Nueva conexión** > **Azure Data Lake Storage Gen2** > **Continuar**.

7. En la página **Nueva conexión (Azure Data Lake Storage Gen2)** , siga estos pasos para crear una conexión.
   1. Seleccione la cuenta habilitada para Data Lake Storage Gen2 en la lista desplegable **Nombre**.
   2. Seleccione **Create** (Crear) para crear la conexión.

   :::image type="content" source="media/load-sap-bw-data/create-adls-gen2-linked-service.png" alt-text="Página de creación de servicio vinculado de ADLS Gen2":::

8. En la página **Almacén de datos de destino**, seleccione la conexión recién creada en la sección **Conexión**, y escriba **copyfromopenhub** como nombre de la carpeta de salida. Luego, seleccione **Siguiente**.

   :::image type="content" source="./media/load-sap-bw-data/destination-data-store-page.png" alt-text="Captura de pantalla que muestra la página Almacén de datos de destino.":::

9. En la página **File format setting** (Configuración del formato de archivo), seleccione **Next** (Siguiente) para usar la configuración predeterminada.

    :::image type="content" source="media/load-sap-bw-data/specify-sink-format.png" alt-text="Página de especificación del formato de receptor":::

10. En la página **Configuración**, especifique **Nombre de tarea** y seleccione **Opciones avanzadas**. Escriba un valor para **Degree of copy parallelism** (Grado de paralelismo de la copia) como por ejemplo 5 para cargar desde SAP BW en paralelo. Luego, seleccione **Siguiente**.

    :::image type="content" source="media/load-sap-bw-data/configure-copy-settings.png" alt-text="Configuración de la copia":::

11. Revise la configuración en la página **Summary** (Resumen). Luego, seleccione **Siguiente**.

    :::image type="content" source="./media/load-sap-bw-data/summary-page.png" alt-text="Captura de pantalla de la página Resumen.":::

12. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización.

13. Observe que la pestaña **Monitor** (Supervisión) a la izquierda de la página, se selecciona automáticamente. Puede usar los vínculos de la columna **Nombre de la canalización** en la página **Ejecuciones de canalización** para ver los detalles de la actividad y volver a ejecutar la canalización.

14. Para ver las ejecuciones de actividad asociadas a la ejecución de la canalización, seleccione los vínculos en la columna **Nombre de canalización**. Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **Todas las ejecuciones de la canalización** al comienzo. Seleccione **Refresh** (Actualizar) para actualizar la lista.

    :::image type="content" source="media/load-sap-bw-data/activity-monitoring.png" alt-text="Pantalla de supervisión de actividades":::

15. Para supervisar los detalles de la ejecución de cada actividad de copia, seleccione el vínculo **Detalles**, que es un icono de gafas, en la misma fila de cada actividad de copia en la vista de supervisión de la actividad. Los detalles disponibles incluyen el volumen de datos copiados del origen al receptor, el rendimiento de los datos, los pasos de ejecución y su duración y las configuraciones utilizadas.

    :::image type="content" source="media/load-sap-bw-data/activity-monitoring-details.png" alt-text="Detalles de la supervisión de la actividad":::

16. Para ver el **identificador de solicitud máxima** de cada actividad de copia, vuelva a la vista de supervisión de actividades y seleccione **Salida** en la misma fila de cada actividad de copia.

    :::image type="content" source="media/load-sap-bw-data/activity-output.png" alt-text="Pantalla de salida de la actividad":::

    :::image type="content" source="media/load-sap-bw-data/activity-output-details.png" alt-text="Vista de detalles de salida de la actividad":::

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Copia incremental de Open Hub para SAP BW

> [!TIP]
> Consulte el [flujo de extracción delta del conector de Open Hub para SAP BW](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para obtener información sobre cómo el conector de Open Hub para SAP BW copia los datos incrementales desde SAP BW. Este artículo también puede ayudarle a entender la configuración básica del conector.

Ahora, vamos a seguir configurando la copia incremental de Open Hub para SAP BW.

La copia incremental utiliza un mecanismo de "límite máximo" que se basa en el **Identificador de solicitud**. El DTP genera ese identificador automáticamente en el destino de Open Hub para SAP BW. En el siguiente diagrama se ilustra este flujo de trabajo:

:::image type="content" source="media/load-sap-bw-data/incremental-copy-workflow.png" alt-text="Diagrama del flujo de trabajo de copia incremental":::

En la página principal, seleccione **Plantillas de canalización** en la sección **Descubrir más** para usar la plantilla integrada.

1. Busque **SAP BW** para encontrar y seleccionar la plantilla **Incremental copy from SAP BW to Azure Data Lake Storage Gen2** (Copia incremental de SAP BW en Azure Data Lake Storage Gen2). Esta plantilla copia los datos en Azure Data Lake Storage Gen2. Puede usar un flujo de trabajo similar para copiar a otros tipos de receptor.

2. En la página principal de la plantilla, seleccione o cree las siguientes tres conexiones y, a continuación, seleccione **Usar esta plantilla** en la esquina inferior derecha de la ventana.

   - **Azure Blob Storage**: En este tutorial, usamos Azure Blob Storage para almacenar el límite máximo, que es el *Identificador de solicitud máxima copiada* .
   - **Open Hub para SAP BW**: Este es el origen desde el que se copian los datos. Consulte el tutorial anterior de copia completa para la configuración detallada.
   - **Azure Data Lake Storage Gen2**: Este es el receptor en el que se van a copiar los datos. Consulte el tutorial anterior de copia completa para la configuración detallada.

   :::image type="content" source="media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png" alt-text="Plantilla de la copia incremental de Open Hub para SAP BW":::

3. Esta plantilla genera una canalización con las siguientes tres actividades y hace que se encadenen cuando se ejecutan correctamente: *Lookup*, *Copy Data* y *Web*.

   Acuda a la pestaña **Parámetros** de la canalización. Verá todas las configuraciones que tiene que proporcionar.

   :::image type="content" source="media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png" alt-text="Configuración de la copia incremental de Open Hub para SAP BW":::

   - **SAPOpenHubDestinationName**: Especifique el nombre de la tabla de Open Hub de la que se van a copiar los datos.

   - **Data_Destination_Container**: especifique el contenedor de destino de Azure Data Lake Storage Gen2 en el que se van a copiar los datos. Si no existe el contenedor, la actividad de copia crea uno durante la ejecución.
  
   - **Data_Destination_Directory**: especifique la ruta de acceso de la carpeta en el contenedor de Azure Data Lake Storage Gen2 en el que se van a copiar los datos. Si no existe la ruta de acceso, la actividad de copia crea una ruta de acceso durante la ejecución.
  
   - **HighWatermarkBlobContainer**: especifique el contenedor para almacenar el valor de marca de agua máximo.

   - **HighWatermarkBlobDirectory**: especifique la ruta de acceso de la carpeta en el contenedor para almacenar el valor de marca de agua máximo.

   - **HighWatermarkBlobName**: Especifique el nombre del blob para almacenar el valor de límite máximo, como `requestIdCache.txt`. En el Blob Storage, vaya a la ruta de acceso correspondiente de HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, como *container/path/requestIdCache.txt*. Cree un blob con contenido 0.

      :::image type="content" source="media/load-sap-bw-data/blob.png" alt-text="Blob content"::: (Contenido del blob)

   - **LogicAppURL**: En esta plantilla, se usa WebActivity para llamar a Azure Logic Apps para establecer el valor de límite máximo en Blob Storage. También puede usar Azure SQL Database para almacenarlo. Use una actividad de procedimiento almacenado para actualizar el valor.

      En primer lugar tiene que crear una aplicación lógica, como se muestra en la siguiente imagen. A continuación, pegue la **URL de HTTP POST**.

      :::image type="content" source="media/load-sap-bw-data/logic-app-config.png" alt-text="Configuración de la aplicación lógica":::

      1. Vaya a Azure Portal. Seleccione un nuevo servicio de **Logic Apps**. Seleccione **+Aplicación lógica en blanco** para ir al **Diseñador de Logic Apps**.

      2. Cree un desencadenador de **Cuando se recibe una solicitud HTTP**. Especifique el cuerpo de la solicitud HTTP como sigue:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Agregue una acción **Crear blob**. Para **Ruta de acceso a la carpeta** y **Nombre del blob**, utilice los mismos valores que ha configurado previamente en *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* y *HighWatermarkBlobName*.

      4. Seleccione **Guardar**. Después, copie el valor de la **URL de HTTP POST** a usar en la canalización.

4. Después de proporcionar los parámetros de canalización, seleccione **Depurar** > **Finalizar** para invocar una ejecución para validar la configuración. O también puede seleccionar **Publicar** para publicar todos los cambios y, a continuación, seleccionar **Agregar desencadenador** para realizar una ejecución.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configuraciones de destino de Open Hub para SAP BW

Esta sección presenta la configuración del lado de SAP BW para usar el conector de Open Hub para SAP BW para copiar los datos.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configuración de la extracción delta en SAP BW

Si necesita tanto la copia histórica como la incremental o solo la copia incremental, configure la extracción delta en SAP BW.

1. Cree el destino de Open Hub. Puede crear el destino de Open Hub en SAP transacción RSA1, que crea automáticamente la transformación necesaria y el proceso de transferencia de datos. Use la configuración siguiente:

   - **ObjectType**: Puede usar cualquier tipo de objeto. En este caso, usamos **InfoCube** como ejemplo.
   - **Tipo de destino**: Seleccione **Database Table** (Tabla de base de datos).
   - **Key of the Table** (Clave de la tabla): Seleccione **Technical Key** (Clave técnica).
   - **Extracción**: Seleccione **Keep Data and Insert Records into Table** (Mantener los datos e insertar registros en la tabla).

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta.png" alt-text="Cuadro de diálogo de creación de una extracción delta de destino de Open Hub para SAP BW":::

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta2.png" alt-text="Cuadro de diálogo de creación de una extracción delta2 de destino de Open Hub para SAP BW":::

   Puede aumentar el número de procesos de trabajo de SAP que se ejecutan en paralelo para el DTP:

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta3.png" alt-text="Captura de pantalla que muestra la configuración para el procesamiento en paralelo, donde puede seleccionar el número de procesos paralelos para el DTP.":::

2. Programe el DTP en cadenas de proceso.

   Un DTP delta para un cubo solo funciona si no se han comprimido las filas necesarias. Asegúrese de que no se está ejecutando compresión del cubo BW antes del DTP en la tabla de Open Hub. La manera más fácil de hacerlo es integrar el DTP en las cadenas de proceso existentes. En el ejemplo siguiente, el DTP (al destino de Open Hub) se inserta en la cadena de proceso entre los pasos *Adjust* (acumulación agregada) y *Collapse* (compresión de cubo).

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-process-chain.png" alt-text="Gráfico del diagrama del flujo de la cadena de proceso de creación de SAP BW":::

### <a name="configure-full-extraction-in-sap-bw"></a>Configuración de la extracción completa en SAP BW

Además de la extracción delta, puede ser conveniente una extracción completa del mismo InfoProvider de SAP BW. Normalmente, esto se aplica si desea realizar la copia completa, pero no incremental, o si desea una [extracción delta resync](#resync-delta-extraction).

No puede tener más de un DTP para el mismo destino de Open Host. Por lo tanto, tiene que crear un destino de Open Host adicional antes de la extracción delta.

:::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-full.png" alt-text="Creación de un destino de Open Host para SAP BW completo":::

Para un destino de Open Host de carga completa, elija opciones distintas a las de la extracción delta:

- En el destino de Open Host: Establezca la opción **Extraction** (Extracción) **Delete Data and Insert Records** (Eliminar datos e insertar registros). En caso contrario, se extraerán datos muchas veces cuando se repita el DTP en una cadena de proceso BW.

- En el DTP: Establezca **Extraction Mode** (Modo de extracción) en **Full** (Completo). Tiene que cambiar el DTP que se creó automáticamente desde **Delta** a **Full** (Completo) inmediatamente después de que se haya creado el destino de Open Host, como se muestra en esta imagen:

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-full2.png" alt-text="Cuadro de diálogo Create SAP BW OHD (Crear OHD para SAP BW) configurado para la extracción &quot;Full&quot; (Completa)":::

- En el conector de Open Hub de BW: desactive **Exclude last request**(Excluir la última solicitud). En caso contrario, no se extraerá nada.

Normalmente, ejecutará manualmente el DTP. O bien, puede crear una cadena de proceso para el DTP completo. Suele ser una cadena aparte que es independiente de las cadenas de proceso existentes. En cualquier caso, *asegúrese de que ha finalizado el DTP antes de iniciar la extracción mediante la copia*. En caso contrario, se copiarán solo datos parciales.

### <a name="run-delta-extraction-the-first-time"></a>Ejecute la extracción delta por primera vez

La primera extracción delta es técnicamente una *extracción completa*. De forma predeterminada, el conector de Open Hub para SAP BW excluye la última solicitud, cuando copia los datos. Para la primera extracción delta, no se extraen datos mediante la actividad de copia hasta que un DTP posterior genera datos delta en la tabla con un identificador de solicitud independiente. Existen dos formas de evitar este escenario:

- Desactivar la opción **Exclude last request** (Excluir la última solicitud) para la primera extracción delta. Asegúrese de que el primer DTP delta ha finalizado antes de iniciar la extracción delta por primera vez.
-  Usar el procedimiento para volver a sincronizar la extracción delta, tal como se describe en la sección siguiente.

### <a name="resync-delta-extraction"></a>Resincronización de la extracción delta

Los siguientes escenarios cambian los datos en los cubos de SAP BW, pero el DTP no los tiene en cuenta:

- Eliminación selectiva de SAP BW (de filas mediante el uso de cualquier condición de filtro)
- Eliminación de la solicitud de SAP BW (de solicitudes defectuosa)

Un destino de Open Hub para SAP no es un destino de datos controlados data mart (en todos los paquetes de soporte técnico de SAP BW desde 2015). Por lo tanto, puede eliminar datos de un cubo sin cambiar los datos en el destino de Open Host. Tiene que volver a sincronizar los datos del cubo con el servicio:

1. Ejecute una extracción completa en el servicio (con un DTP completo en SAP).
2. Elimine todas las filas de la tabla de Open Hub para el DTP delta.
3. Establezca el estado del DTP delta en **Fetched** (Obtenido).

Después de esto, todos los DTP delta y las extracciones delta funcionan según lo previsto.

Para establecer el estado de los DTP delta en **Fetched** (Obtenido), puede usar la siguiente opción para ejecutar manualmente el DTP delta:

*Sin transferencia de datos. Estado Delta en origen: Fetched* (Obtenido)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la compatibilidad del conector de Open Hub para SAP BW:

> [!div class="nextstepaction"]
>[Conector de Open Hub para SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
