---
title: 'Movimiento de datos: puerta de enlace de administración de datos'
description: Configuración de una puerta de enlace para mover datos entre una infraestructura local y la nube. Uso de Data Management Gateway en Azure Data Factory para mover los datos.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: cb3e020f1ac22daa6d0432ad0abaf1aa215d1048
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580703"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Movimiento de datos entre orígenes locales y la nube con Data Management Gateway
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, que se encuentra en versión preliminar, consulte el artículo sobre la [copia de datos a Azure SQL Data Warehouse o desde Azure SQL Data Warehouse mediante Data Factory](../tutorial-hybrid-copy-powershell.md).

Este artículo proporciona información general sobre la integración de los almacenes de datos locales y los almacenes de datos en la nube mediante Data Factory. Este artículo se basa en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) y otros artículos de conceptos básicos de Data Factory: [conjuntos de datos](data-factory-create-datasets.md) y [canalizaciones](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Data Management Gateway
Debe instalar Data Management Gateway en su equipo local para habilitar el movimiento de datos a o desde un almacén de datos local. La puerta de enlace puede instalarse en el mismo equipo que el almacén de datos o en un equipo diferente, siempre que la puerta de enlace pueda conectarse al almacén de datos.

> [!IMPORTANT]
> Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más detalles sobre Data Management Gateway.

El siguiente tutorial muestra cómo crear una factoría de datos con una canalización que mueve los datos de una base de datos de **SQL Server** local a Azure Blob Storage. Como parte del tutorial, instalará y configurará la puerta de enlace de administración de datos en su máquina.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Tutorial: copiar datos locales a la nube
En este tutorial realizará los siguientes pasos:

1. Creación de una factoría de datos.
2. Creación de una instancia de Data Management Gateway.
3. Creación de servicios vinculados para los almacenes de datos de origen y receptor.
4. Creación de conjuntos de datos que representen los datos de entrada y salida.
5. Creación de una canalización con una actividad de copia para mover los datos.

## <a name="prerequisites-for-the-tutorial"></a>Requisitos previos para el tutorial
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

* **Suscripción de Azure**.  Si no tiene una suscripción, puede crear una cuenta de prueba gratuita en tan solo un par de minutos. Consulte el artículo [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) para obtener información.
* **Cuenta de Azure Storage**. Blob Storage se usará como un almacén de datos de **destino o receptor** en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, consulte la sección [Crear una cuenta de almacenamiento](../../storage/common/storage-account-create.md) para ver los pasos para su creación.
* **SQL Server**. En este tutorial, usará una base de datos de SQL Server como almacén de datos de **origen**.

## <a name="create-data-factory"></a>Creación de Data Factory
En este paso, use Azure Portal para crear una instancia de Azure Data Factory denominada **ADFTutorialOnPremDF**.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **Crear un recurso**, en **Inteligencia y análisis** y en **Data Factory**.

   :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png" alt-text="Nuevo->DataFactory":::  
3. En la página **Nueva factoría de datos**, escriba el nombre **ADFTutorialOnPremDF**.

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png" alt-text="Agregar al Panel de inicio":::

   > [!IMPORTANT]
   > El nombre de la instancia de Azure Data Factory debe ser único de forma global. Si recibe el error: **El nombre de la factoría de datos "ADFTutorialOnPremDF" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, sunombreADFTutorialOnPremDF) e intente crearla de nuevo. Use este nombre en lugar de ADFTutorialFactory al realizar los restantes pasos de este tutorial.
   >
   > El nombre de la factoría de datos se puede registrar como un nombre **DNS** en el futuro y, por lo tanto, que sea visible públicamente.
   >
   >
4. Seleccione la **suscripción de Azure** donde desea crear la factoría de datos.
5. Seleccione un **grupo de recursos** existente o cree uno nuevo. Para este tutorial, cree un grupo de recursos llamado **ADFTutorialResourceGroup**.
6. Haga clic en **Crear** en la página **Nueva factoría de datos**.

   > [!IMPORTANT]
   > Para crear instancias de Data Factory, es preciso ser miembro del rol [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) en el nivel de grupo de recursos o suscripción.
   >
   >
7. Una vez completada la creación, puede ver la página **Factoría de datos** que se muestra en la siguiente imagen:

   :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png" alt-text="Página principal de Factoría de datos":::

## <a name="create-gateway"></a>Crear puerta de enlace
1. En la página **Factoría de datos**, haga clic en el icono **Crear e implementar** para iniciar el **Editor** de la factoría de datos.

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png" alt-text="Mosaico Crear e implementar":::
2. En Data Factory Editor, haga clic en **... Más** en la barra de herramientas y, a continuación, haga clic en **Nueva puerta de enlace de datos**. O bien, haga clic con el botón derecho **Puertas de enlace de datos** en la vista de árbol y elija **Nueva puerta de enlace de datos**.

   :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png" alt-text="Nueva puerta de enlace de datos en la barra de herramientas":::
3. En la página **Crear**, escriba **adftutorialgateway** para el **nombre** y haga clic en **Aceptar**.     

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png" alt-text="Página Crear puerta de enlace":::

    > [!NOTE]
    > En este tutorial, creará la puerta de enlace lógica con un único nodo (la máquina de Windows local). Puede escalar horizontalmente una puerta de enlace de administración de datos mediante la asociación de varias máquinas locales con la puerta de enlace. Puede escalar verticalmente aumentando el número de trabajos de movimiento de datos que pueden ejecutarse simultáneamente en un nodo. Esta característica también está disponible para una puerta de enlace lógica con un único nodo. Consulte el artículo [Escalado en Data Management Gateway en Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para más información.  
4. En la página **Configurar**, haga clic en **Instalar directamente en este equipo**. Esta acción descarga el paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.  

   > [!NOTE]
   > Utilice Internet Explorer o un explorador web compatible con Microsoft ClickOnce.
   >
   > Si usa Chrome, vaya a [Chrome Web Store](https://chrome.google.com/webstore/), busque la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instálela.
   >
   > Debe hacer lo mismo para Firefox (instale un complemento). Haga clic en el botón **Abrir menú** de la barra de herramientas (**tres líneas horizontales** en la esquina superior derecha), haga clic en **Complementos**, busque la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instálela.    
   >
   >

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png" alt-text="Puerta de enlace: página Configurar":::

    Esta es la forma más sencilla (un clic) para descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver que la aplicación **Administrador de configuración de Microsoft Data Management Gateway** está instalada en el equipo. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    También puede descargar e instalar la puerta de enlace manualmente con los vínculos de esta página y registrarla usando la clave que se muestra en el cuadro de texto **Nueva clave**.

    Vea el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para obtener todos los detalles sobre la puerta de enlace.

   > [!NOTE]
   > Debe ser administrador del equipo local para instalar y configurar correctamente Data Management Gateway. Puede agregar más usuarios al grupo local de Windows **Usuarios de Data Management Gateway** . Los miembros de este grupo pueden usar la herramienta Administrador de configuración de Data Management Gateway para configurar la puerta de enlace.
   >
   >
5. Espere unos minutos o espere hasta que vea el siguiente mensaje de notificación:

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png" alt-text="Instalación correcta de la puerta de enlace":::
6. Inicie la aplicación **Administrador de configuración de Data Management Gateway** en el equipo. En la ventana **Búsqueda**, escriba **Data Management Gateway** para tener acceso a esta utilidad. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png" alt-text="Administrador de configuración de puertas de enlace":::
7. Confirme que ve el mensaje `adftutorialgateway is connected to the cloud service`. La barra de estado de la parte inferior muestra **Conectado al servicio en la nube** junto con una **marca de verificación verde**.

    En la pestaña **Inicio**, también puede realizar las siguientes operaciones:

   * **Registrar** una puerta de enlace con una clave en Azure Portal mediante el botón Registrar.
   * **Detener** el servicio host de Data Management Gateway que se ejecuta en la máquina de puerta de enlace.
   * **Programar las actualizaciones** que se instalarán en un momento específico del día.
   * Ver cuándo se ha **actualizado por última vez** la puerta de enlace.
   * Especificar la hora a la que se puede instalar una actualización de la puerta de enlace.
8. Cambie a la pestaña **Configuración** . El certificado que se ha especificado en la sección **Certificado** se usa para cifrar o descifrar las credenciales para el almacén de datos local que especifique en el portal. Haga clic en **Cambiar** para usar su propio certificado en su lugar. De forma predeterminada, la puerta de enlace usa el certificado generado automáticamente por el servicio Data Factory.

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png" alt-text="Configuración del certificado de puerta de enlace":::

    También puede realizar las siguientes acciones en la pestaña **Configuración**:

   * Consultar o exportar el certificado que usa la puerta de enlace.
   * Cambiar el punto de conexión HTTPS que usa la puerta de enlace.    
   * Establecer un proxy HTTP que usará la puerta de enlace.     
9. (Opcional) Cambie a la pestaña **Diagnósticos**, active la opción **Habilitar el registro detallado** si quiere habilitar el registro detallado que puede usar para solucionar los problemas de la puerta de enlace. La información de registro se encuentra en el **Visor de sucesos**, en el nodo **Registros de aplicaciones y servicios** -> **Data Management Gateway**.

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png" alt-text="Ficha Diagnóstico":::

    También puede realizar las siguientes acciones en la pestaña **Diagnósticos** :

   * Utilice la sección **Probar conexión** para probar la conexión con un origen de datos local mediante la puerta de enlace.
   * Haga clic en **Ver registros** para ver el registro de Data Management Gateway en una ventana del Visor de eventos.
   * Haga clic en **Enviar registros** para cargar un archivo zip con los registros de los últimos siete días en Microsoft y facilitar así la solución de sus problemas.
10. En la pestaña **Diagnóstico**, en la sección **Probar conexión**, seleccione **SqlServer** para el tipo de almacén de datos; escriba el nombre del servidor de base de datos, el nombre de la base de datos, especifique el tipo de autenticación, escriba el nombre de usuario y la contraseña, y haga clic en **Probar** para comprobar si la puerta de enlace puede conectarse a la base de datos.
11. Cambie al explorador web y, en **Azure Portal**, haga clic en **Aceptar** en la página **Configurar** y, después, en la página **Nueva puerta de enlace de datos**.
12. Debería ver **adftutorialgateway** en **Puertas de enlace de datos** en la vista de árbol de la izquierda.  Si hace clic en ella, debería ver el JSON asociado.

## <a name="create-linked-services"></a>Crear servicios vinculados
En este paso, creará dos servicios vinculados: **AzureStorageLinkedService** y **SqlServerLinkedService**. **SqlServerLinkedService** vincula una base de datos de SQL Server, mientras que **AzureStorageLinkedService** vincula un almacén de blobs de Azure a la factoría de datos. Más adelante en este tutorial creará una canalización que copia datos de la base de datos de SQL Server al almacén de blobs de Azure.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Adición de un servicio vinculado a una base de datos de SQL Server
1. En el **Editor de Data Factory**, haga clic en **Nuevo almacén de datos** en la barra de herramientas y seleccione **SQL Server**.

   :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png" alt-text="Nuevo servicio vinculado de SQL Server":::
2. En el **Editor de JSON** a la derecha, realice los siguientes pasos:

   1. Para **gatewayName**, especifique **adftutorialgateway**.    
   2. En **connectionString**, realice los siguientes pasos:    

      1. En **servername**, escriba el nombre del servidor que hospeda la base de datos de SQL Server.
      2. En **databasename**, escriba el nombre de la base de datos.
      3. Haga clic en el botón **Cifrar** de la barra de herramientas. Verá la aplicación Administrador de credenciales.

         :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png" alt-text="Aplicación Administrador de credenciales":::
      4. En el cuadro de diálogo **Establecer credenciales**, escriba el tipo de autenticación, el nombre de usuario y la contraseña, y haga clic en **Aceptar**. Si la conexión es correcta, se almacenan las credenciales cifradas en el código JSON y se cierra el cuadro de diálogo.
      5. Si no se cierra automáticamente, cierre la pestaña de explorador vacía que inició el cuadro de diálogo y regrese a la pestaña de Azure Portal.

         En la máquina de la puerta de enlace, las credenciales se **cifrarán** mediante un certificado que es propiedad del servicio Data Factory. Si prefiere usar el certificado asociado a la puerta de enlace de administración de datos, consulte Configuración de credenciales y seguridad.    
   3. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado de SQL Server. Verá el servicio vinculado en la vista de árbol.

      :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png" alt-text="Servicio vinculado a SQL Server en la vista de árbol":::    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adición de un servicio vinculado para una cuenta de almacenamiento de Azure
1. En el **Editor de Data Factory**, haga clic en **Nuevo almacén de datos** en la barra de comandos y haga clic en **Azure Storage**.
2. Escriba el nombre de la cuenta de almacenamiento de Azure en **Nombre de cuenta**.
3. Escriba la clave de su cuenta de almacenamiento de Azure en **Clave de cuenta**.
4. Haga clic en **Implementar** para implementar **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => Azure Blob Storage). Antes de crear los conjuntos de datos, realice estos pasos (los pasos detallados se indican después de la lista):

* Cree una tabla con el nombre **emp** en la base de datos de SQL Server que agregó como servicio vinculado a la factoría de datos e inserte un par de entradas de ejemplo en la tabla.
* Cree un contenedor de blobs llamado **adftutorial** en la cuenta de Azure Blob Storage que agregó como un servicio vinculado a la factoría de datos.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparación de la instancia local de SQL Server para el tutorial
1. En la base de datos que ha especificado para el servicio vinculado de SQL Server (**SqlServerLinkedService**), use el siguiente script de SQL para crear la tabla **emp** en la base de datos.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Inserte algún ejemplo en la tabla:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Creación de un conjunto de datos de entrada

1. En **Data Factory Editor**, haga clic en **... Más**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Tabla de SQL Server**.
2. Reemplace el script JSON del panel derecho por el texto siguiente:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```       
   Tenga en cuenta los siguientes puntos:

   * **type** está establecido en **SqlServerTable**.
   * **tablename** está establecido en **emp**.
   * **linkedServiceName** está establecido en **SqlServerLinkedService** (creó este servicio vinculado en anteriormente en este tutorial).
   * Para un conjunto de datos de entrada no generado por otra canalización en Azure Data Factory, tiene que establecer la propiedad **external** en **true**. Indica que los datos de entrada se han producido fuera del servicio Azure Data Factory. Opcionalmente, puede especificar las directivas de datos externos mediante el elemento **externalData** en la sección **Policy**.    

   Para más información acerca de las propiedades de JSON, consulte [Movimiento de datos hacia y desde SQL Server](data-factory-sqlserver-connector.md).
3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos.  

### <a name="create-output-dataset"></a>Creación del conjunto de datos de salida

1. En el **Editor de Data Factory**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Azure Blob Storage**.
2. Reemplace el script JSON del panel derecho por el texto siguiente:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Tenga en cuenta los siguientes puntos:

   * **type** está establecido en **AzureBlob**.
   * **linkedServiceName** está establecido en **AzureStorageLinkedService** (este servicio vinculado se creó en el paso 2).
   * **folderPath** está establecido en **adftutorial/outfromonpremdf**, donde outfromonpremdf es la carpeta del contenedor adftutorial. Cree el contenedor **adftutorial** si este todavía no existe.
   * **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** está establecido en **1**).  El servicio Data Factory generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos de Azure SQL.

   Si no especifica un valor de **fileName** para una **tabla de salida**, los archivos generados en **folderPath** se nombran con el siguiente formato: `Data.<Guid>.txt` (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad partitionedBy. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se está procesando) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Para más información acerca de las propiedades de JSON, consulte [Movimiento de datos hacia y desde Azure Blob Storage](data-factory-azure-blob-connector.md).
3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos. Confirme que ve ambos conjuntos de datos en la vista de árbol.  

## <a name="create-pipeline"></a>Creación de una canalización
En este paso, va a crear una **canalización** con una **actividad de copia** que usa **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como salida.

1. En Data Factory Editor, haga clic en **... Más** y, luego, en **Nueva canalización**.
2. Reemplace el script JSON del panel derecho por el texto siguiente:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día.
   >
   >

   Tenga en cuenta los siguientes puntos:

   * En la sección de actividades, solo hay una actividad cuyo **type** está establecido en **Copy**.
   * La **entrada** de la actividad está establecida en **EmpOnPremSQLTable** y la **salida** de la actividad está establecida en **OutputBlobTable**.
   * En la sección **typeProperties**, **SqlSource** se especifica como el **tipo de origen** y **BlobSink** como el **tipo de receptor**.
   * La consulta SQL `select * from emp` está especificada para la propiedad **sqlReaderQuery** de **SqlSource**.

   Las fechas y horas de inicio y de finalización deben estar en [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de finalización ( **end** ) es opcional, pero se utilizará en este tutorial.

   Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de la propiedad **end**.

   Está definiendo la duración del procesamiento de los segmentos de datos según las propiedades de **disponibilidad** que se definieron para cada conjunto de datos de Azure Data Factory.

   En el ejemplo, hay 24 segmentos de datos, ya que se produce un segmento de datos a la hora.        
3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que la canalización se muestra en la vista de árbol en el nodo **Canalizaciones**.  
4. Ahora, haga clic en **X** dos veces para cerrar la página y volver a la página **Factoría de datos** de **ADFTutorialOnPremDF**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, conjuntos de datos y una canalización, y ha programado la canalización.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Visualización de la factoría de datos en una vista de diagrama
1. En **Azure Portal**, haga clic en el icono **Diagrama** en la página principal de la factoría de datos **ADFTutorialOnPremDF**. :

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png" alt-text="Vínculo de diagrama":::
2. Debería ver un diagrama similar a la siguiente imagen:

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png" alt-text="Vista de diagrama":::

    Puede acercar, alejar, acercar al 100 %, ampliar para ajustar, colocar automáticamente canalizaciones y conjuntos de datos, y mostrar información sobre el linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados).  Puede hacer doble clic en un objeto (conjunto de datos o canalización de entrada o salida) para ver sus propiedades.

## <a name="monitor-pipeline"></a>Supervisión de la canalización
En este paso, usará Azure Portal para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información sobre la supervisión, consulte [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md).

1. En el diagrama, haga doble clic en **EmpOnPremSQLTable**.  

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png" alt-text="Segmentos EmpOnPremSQLTable":::
2. Observe que el estado de todos los segmentos de datos es **Listo** porque la duración de la canalización (hora de inicio a hora de finalización) está en el pasado. Esto ocurre también porque ha insertado los datos en la base de datos de SQL Server y están ahí todo el tiempo. Confirme que no aparecen segmentos en la sección de **segmentos problemáticos** de la parte inferior. Para ver todos los segmentos, haga clic en **Ver más** en la parte inferior de la lista de segmentos.
3. A continuación, en la página **Conjuntos de datos**, haga clic en **OutputBlobTable**.

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png" alt-text="Segmentos OputputBlobTable":::
4. Haga clic en cualquier segmento de datos de la lista y debería ver la página **Segmento de datos**. Verá las ejecuciones de actividad para ese segmento. Normalmente solo se ve una ejecución de actividad.  

    :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png" alt-text="Hoja Segmento de datos":::

    Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.
5. Haga clic en la **ejecución de la actividad** de la lista en la parte inferior para ver los **detalles de la ejecución de la actividad**.

   :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png" alt-text="Página Detalles de ejecución de actividad":::

   Verá información como el rendimiento, la duración y la puerta de enlace que se usa para transferir los datos.
6. Haga clic en **X** para cerrar todas las páginas hasta que
7. regrese a la página principal de **ADFTutorialOnPremDF**.
8. (Opcional) Haga clic en **Canalizaciones**, elija **ADFTutorialOnPremDF** y obtenga detalles de los conjuntos de datos de entrada (**Consumido**) o los conjuntos de datos de salida (**Producido**).
9. Use herramientas como el [Explorador de Microsoft Storage](https://storageexplorer.com/) para comprobar que se crea un blob o archivo cada hora.

   :::image type="content" source="./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png" alt-text="Explorador de Azure Storage":::

## <a name="next-steps"></a>Pasos siguientes
* Vea el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para obtener todos los detalles sobre Data Management Gateway.
* Consulte [Copia de datos de Azure Blob en Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) si necesita más información sobre el uso de la actividad de copia para mover datos desde un almacén de datos de origen a un almacén de datos receptor.
