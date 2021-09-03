---
title: Conexión de orígenes de datos diferentes a Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Incorporación de fuentes de datos diferentes a Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: mbullwin
ms.openlocfilehash: 2bb387863baffdd014eedb20d94ea1273860b8c4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730609"
---
# <a name="how-to-connect-different-data-sources"></a>Conexión con distintos orígenes de datos

Use este artículo para encontrar la configuración y los requisitos para conectar distintos tipos de orígenes de datos a Azure Metrics Advisor. Para obtener información sobre los datos con Metrics Advisor, vea [Incorporación de los datos](how-tos/onboard-your-data.md). 

## <a name="supported-authentication-types"></a>Tipos de autenticación que se admiten

| Tipos de autenticación | Descripción |
| ---------------------|-------------|
|**Basic** | Necesita proporcionar los parámetros básicos para acceder a los orígenes de datos. Por ejemplo, puede usar una cadena de conexión o una contraseña. Los administradores de fuentes de distribución de datos pueden ver estas credenciales. |
| **Identidad administrada de Azure** | [Identidades administradas](../../active-directory/managed-identities-azure-resources/overview.md) para recursos de Azure es una característica de Azure Active Directory (Azure AD). Proporciona a los servicios de Azure una identidad de sistema administrada automáticamente en Azure AD. Puede usar la identidad para autenticarse en cualquier servicio que admita la autenticación de Azure AD.|
| **Cadena de conexión de Azure SQL**| Almacene la cadena de conexión de Azure SQL como una entidad de credencial en Metrics Advisor y úsela directamente cada vez que importe los datos de métricas. Solo los administradores de la entidad de credencial pueden ver estas credenciales, pero los espectadores autorizados pueden crear fuentes de distribución de datos sin necesidad de conocer los detalles de las credenciales. |
| **Clave compartida de Azure Data Lake Storage Gen2**| Almacene la clave de cuenta del lago de datos como una entidad de credencial en Metrics Advisor y úsela directamente cada vez que importe los datos de métricas. Solo los administradores de la entidad de credencial pueden ver estas credenciales, pero los espectadores autorizados pueden crear fuentes de distribución de datos sin necesidad de conocer los detalles de las credenciales.|
| **Entidad de servicio**| Almacene la [entidad de servicio](../../active-directory/develop/app-objects-and-service-principals.md) como una entidad de credencial en Metrics Advisor y úsela directamente cada vez que importe los datos de métricas. Solo los administradores de la entidad de credencial pueden ver las credenciales, pero los espectadores autorizados pueden crear fuentes de distribución de datos sin necesidad de conocer los detalles de las credenciales.|
| **Entidad de servicio de Key Vault**|Almacene la [entidad de servicio en un almacén de claves](/azure-stack/user/azure-stack-key-vault-store-credentials) como una entidad de credencial en Metrics Advisor y úsela directamente cada vez que importe los datos de métricas. Solo los administradores de un entidad de credencial pueden ver las credenciales, pero los espectadores pueden crear fuentes de distribución de datos sin necesidad de conocer los detalles de las credenciales. |


## <a name="data-sources-and-corresponding-authentication-types"></a>Orígenes de datos y tipos de autenticación correspondientes

| Orígenes de datos | Tipos de autenticación |
|-------------| ---------------------|
|[Application Insights](#appinsights) | Básica |
|[Azure Blob Storage (JSON)](#blob) | Básica<br>Identidad administrada |
|[Azure Cosmos DB (SQL)](#cosmosdb) | Básica |
|[Azure Data Explorer (Kusto)](#kusto) | Básica<br>Identidad administrada<br>Entidad de servicio<br>Entidad de servicio de Key Vault |
|[Azure Data Lake Storage Gen2](#adl) | Básica<br>Clave compartida de Data Lake Storage Gen2<br>Entidad de servicio<br>Entidad de servicio de Key Vault |
|[Azure Event Hubs](#eventhubs) | Básica |
|[Registros de Azure Monitor](#log) | Básica<br>Entidad de servicio<br>Entidad de servicio de Key Vault |
|[Azure SQL Database/SQL Server](#sql) | Básica<br>Identidad administrada<br>Entidad de servicio<br>Entidad de servicio de Key Vault<br>Cadena de conexión de Azure SQL |
|[Azure Table Storage](#table) | Básica | 
|[InfluxDB (InfluxQL)](#influxdb) | Básica |
|[MongoDB](#mongodb) | Básica |
|[MySQL](#mysql) | Básica |
|[PostgreSQL](#pgsql) | Básica|
|[Archivos locales (CSV)](#csv) | Básica|

En las secciones siguientes se especifican los parámetros necesarios para todos los tipos de autenticación en distintos escenarios de orígenes de datos. 

## <a name="span-idappinsightsapplication-insightsspan"></a><span id="appinsights">Application Insights</span>

* **Id. de la aplicación**: se usa para identificar esta aplicación cuando se utiliza la API de Application Insights. Para obtener el identificador de la aplicación, siga estos pasos:

   1. Desde el recurso de Application Insights, seleccione **Acceso de API**.
   
      ![Captura de pantalla que muestra cómo obtener el identificador de la aplicación del recurso de Application Insights.](media/portal-app-insights-app-id.png)

   2. Copie el identificador de la aplicación generado en el campo **Id. de la aplicación** en Metrics Advisor. 

* **Clave de API**: las claves de API las usan las aplicaciones fuera del explorador para acceder a este recurso. Para obtener la clave de API, siga estos pasos:

   1. Desde el recurso de Application Insights, seleccione **Acceso de API**.

   2. Seleccione **Crear clave de API**.

   3. Escriba una descripción breve, seleccione la opción **Leer telemetría** y seleccione **Generar clave**.

      ![Captura de pantalla que muestra cómo obtener la clave de API en Azure Portal.](media/portal-app-insights-app-id-api-key.png)

       > [!IMPORTANT]
       > Copie y guarde esta clave de API. No se volverá a mostrar. Si pierda esta clave, deberá crear una nueva.

   4. Copie la clave de API en el campo **Clave de la API** en Metrics Advisor.

* **Consulta**: los registros de Azure Application Insights se crean en Azure Data Explorer y las consultas de registros de Azure Monitor usan una versión del mismo lenguaje de consulta de Kusto. La [documentación del lenguaje de consulta de Kusto](/azure/data-explorer/kusto/query) debe ser el principal recurso para escribir una consulta en Application Insights. 

    Consulta de ejemplo:

    ``` Kusto
    [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);
    ```
    También puede consultar [Tutorial: Escritura de una consulta válida](tutorials/write-a-valid-query.md) para obtener ejemplos más específicos.
  
## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Cadena de conexión**: hay dos tipos de autenticación para Azure Blob Storage (JSON):

    * **Básica**: vea [Configuración de las cadenas de conexión de Azure Storage](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) para obtener información sobre cómo recuperar esta cadena. Además, puede consultar el recurso de Azure Blob Storage en Azure Portal y buscar la cadena de conexión directamente en **Configuración** > **Claves de acceso**.
    
    * **Identidad administrada**: las identidades administradas para los recursos de Azure pueden autorizar el acceso a los datos de blobs y colas. La característica usa las credenciales de Azure AD de las aplicaciones que se ejecutan en máquinas virtuales (VM) de Azure, aplicaciones de funciones, conjuntos de escalado de máquinas virtuales y otros servicios. 
    
        Puede crear una identidad administrada en Azure Portal para el recurso de Azure Blob Storage. En **Control de acceso (IAM)** , seleccione **Asignaciones de roles** y, después, seleccione **Agregar**. Un tipo de rol sugerido es: **Lector de datos de blobs de almacenamiento**. Para más información, vea [Uso de la identidad administrada para acceder a Azure Storage](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access-1).
    
        ![Captura de pantalla que muestra un blob de identidad administrada.](media/managed-identity-blob.png)
    

* **Contenedor**: Metrics Advisor espera que los datos de series temporales se almacenen como archivos de blob (un blob por marca de tiempo) en un solo contenedor. Este es el campo de nombre de contenedor.

* **Plantilla de blob**: Metrics Advisor usa una ruta de acceso para buscar el archivo JSON en Blob Storage. Este es un ejemplo de una plantilla de archivo de blob, que se usa para buscar el archivo JSON en Blob Storage: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. `%Y/%m` es la ruta de acceso y, si tiene `%d` en la ruta de acceso, puede agregarla después de `%m`. Si se utiliza la fecha para el nombre del archivo JSON, también puede usar `%Y-%m-%d-%h-%M.json`.

   Se admiten los siguientes parámetros:
   
   * `%Y` es el año, con formato `yyyy`.
   * `%m` es el mes, con formato `MM`.
   * `%d` es el día, con formato `dd`.
   * `%h` es la hora, con formato `HH`.
   * `%M` es el minuto, con formato `mm`.
  
   Por ejemplo, en el siguiente conjunto de datos, la plantilla de blob debe ser `%Y/%m/%d/00/JsonFormatV2.json`.
  
   ![Captura de pantalla que muestra la plantilla de blob.](media/blob-template.png)
  

* **Versión de formato JSON**: Define el esquema de datos en los archivos JSON. Metrics Advisor admite las siguientes versiones. Puede elegir una para rellenar el campo:
  
   * **v1** (valor predeterminado)

      Solo se aceptan las métricas *Nombre* y *Valor*. Por ejemplo:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

   * **v2**

      También se aceptan las métricas *Dimensiones* y *Marca de tiempo*. Por ejemplo:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

   Solo se permite una marca de tiempo por cada archivo JSON. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Cadena de conexión**: cadena de conexión para acceder a Azure Cosmos DB. Esto se puede encontrar en el recurso de Azure Cosmos DB en Azure Portal, en **Claves**. Para más información, consulte [Protección del acceso a los datos de Azure Cosmos DB](../../cosmos-db/secure-access-to-data.md).
* **Base de datos**: Base de datos que se va a consultar. En Azure Portal, en **Contenedores**, vaya a **Examinar** para buscar la base de datos.
* **Id. de colección**: Identificador de la colección con el que se va a realizar la consulta. En Azure Portal, en **Contenedores**, vaya a **Examinar** para buscar el identificador de la colección.
* **Consulta SQL**: una consulta SQL para obtener y formular datos en datos de series temporales multidimensionales. Puede usar las variables `@IntervalStart` y `@IntervalEnd` en la consulta. Deben tener el formato siguiente: `yyyy-MM-ddTHH:mm:ssZ`.

    Consulta de ejemplo:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    Para obtener más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Cadena de conexión**: hay cuatro tipos de autenticación para Azure Data Explorer (Kusto): básico, entidad de servicio, entidad de servicio del almacén de claves e identidad administrada. El origen de datos de la cadena de conexión debe tener el formato URI (comienza por "https"). Puede encontrar el URI en Azure Portal.
    
    * **Básico**: Metrics Advisor admite el acceso a Azure Data Explorer (Kusto) mediante la autenticación de la aplicación de Azure AD. Debe crear y registrar una aplicación de Azure AD y, a continuación, autorizarla para acceder a una base de datos de Azure Data Explorer. Para más información, vea [Creación de un registro de aplicación de Azure Active Directory en Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app). Este es un ejemplo de una cadena de conexión:
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client ID=<Application Client ID>;Application Key=<Application Key>;Authority ID=<Tenant ID>
        ```

    * **Entidad de servicio**: una entidad de servicio es una instancia concreta creada a partir del objeto de aplicación. La entidad de servicio hereda determinadas propiedades de ese objeto de aplicación. El objeto de entidad de servicio define lo que la aplicación puede hacer en el inquilino específico, quién puede acceder a la aplicación y a qué recursos tiene acceso la aplicación. Para usar una entidad de servicio en Metrics Advisor:
    
        1. Cree el registro de aplicación de Azure AD. Para más información, vea [Creación de un registro de aplicación de Azure Active Directory en Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app).

        1. Administre los permisos de base de datos de Azure Data Explorer. Para obtener más información, consulte [Administración de permisos de base de datos del Explorador de datos de Azure](/azure/data-explorer/manage-database-permissions). 

        1. Cree una entidad de credencial en Metrics Advisor. Vea cómo [crear una entidad de credencial](how-tos/credential-entity.md) en Metrics Advisor, para que pueda elegir esa entidad al agregar una fuente de distribución de datos para el tipo de autenticación de entidad de servicio. 
        
        Este es un ejemplo de una cadena de conexión:
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Entidad de servicio del almacén de claves**: Azure Key Vault ayuda a proteger las claves criptográficas y los valores secretos que usan los servicios y aplicaciones en la nube. Con Key Vault, puede cifrar valores de claves y secretos. Primero debe crear una entidad de servicio y, a continuación, almacenarla en Key Vault. Para más información, vea [Creación de una entidad de credencial para la entidad de servicio desde Key Vault](how-tos/credential-entity.md#sp-from-kv) para seguir el procedimiento detallado para establecer la entidad de servicio desde el almacén de claves. Este es un ejemplo de una cadena de conexión: 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Identidad administrada**: la identidad administrada para los recursos de Azure pueden autorizar el acceso a los datos de blobs y colas. La identidad administrada usa las credenciales de Azure AD de las aplicaciones que se ejecutan en máquinas virtuales de Azure, aplicaciones de funciones, conjuntos de escalado de máquinas virtuales y otros servicios. Si usa la identidad administrada para recursos de Azure y la autenticación de Azure AD, puede evitar el almacenamiento de credenciales con las aplicaciones que se ejecutan en la nube. Obtenga información sobre cómo [autorizar con una identidad administrada](../../storage/common/storage-auth-aad-msi.md#enable-managed-identities-on-a-vm). 
    
        Puede crear una identidad administrada en Azure Portal para Azure Data Explorer (Kusto). Seleccione **Permisos** > **Agregar**. El tipo de rol sugerido es: **administrador/espectador**.
        
        ![Captura de pantalla que muestra la identidad administrada de Kusto.](media/managed-identity-kusto.png)

        Este es un ejemplo de una cadena de conexión: 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

     
* **Consulta**: para obtener y formular datos en datos de series temporales multidimensionales, vea [Lenguaje de consulta Kusto](/azure/data-explorer/kusto/query). Puede usar las variables `@IntervalStart` y `@IntervalEnd` en la consulta. Deben tener el formato siguiente: `yyyy-MM-ddTHH:mm:ssZ`.

    Consulta de ejemplo:
    
    ``` Kusto
   [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
   ```

    Para obtener más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Nombre de cuenta**: los tipos de autenticación de Azure Data Lake Storage Gen2 son básico, clave compartida de Azure Data Lake Storage Gen2, entidad de servicio y entidad de servicio de Key Vault.
    
    * **Básico**: el **Nombre de cuenta** de Azure Data Lake Storage Gen2. Puede encontrarlo en el recurso de la cuenta de Azure Storage (Azure Data Lake Storage Gen2) en **Claves de acceso**. 

    * **Clave compartida de Azure Data Lake Storage Gen2**: en primer lugar, especifique la clave de cuenta para acceder a Azure Data Lake Storage Gen2 (es la misma que la clave de cuenta del tipo de autenticación básico). Puede encontrarla en el recurso de la cuenta de Azure Storage (Azure Data Lake Storage Gen2) en **Claves de acceso**. A continuación, [cree una entidad de credencial](how-tos/credential-entity.md) para el tipo de clave compartida de Azure Data Lake Storage Gen2 y rellene la clave de cuenta. 

        El nombre de cuenta es el mismo que el tipo de autenticación básico.
    
    * **Entidad de servicio**: una *entidad de servicio* es una instancia concreta creada a partir del objeto de aplicación, que hereda determinadas propiedades de ese objeto de aplicación. La entidad de servicio se crea en cada inquilino donde se usa la aplicación y hace referencia al objeto de aplicación único global. El objeto de entidad de servicio define lo que la aplicación puede hacer en el inquilino específico, quién puede acceder a la aplicación y a qué recursos tiene acceso la aplicación.

        El nombre de cuenta es el mismo que el tipo de autenticación básico.
    
        **Paso 1**: cree y registre una aplicación de Azure AD y, a continuación, autorícela para acceder a la base de datos. Para más información, vea [Creación de un registro de aplicación de Azure Active Directory](/azure/data-explorer/provision-azure-ad-app).

        **Paso 2**: asigne roles.
        
        1. En Azure Portal, vaya al servicio **Cuentas de almacenamiento**.
        
        2. Seleccione la cuenta de Azure Data Lake Storage Gen2 para usarla con este registro de aplicación.

        3. Seleccione **Access Control (IAM)** .

        4. Seleccione **+ Agregar** y seleccione **Agregar asignación de roles** en el menú.

        5. Establezca el campo **Seleccionar** en el nombre de la aplicación de Azure AD y establezca el rol en **Colaborador de datos de blobs de almacenamiento**. Después, seleccione **Guardar**.
        
        ![Captura de pantalla que muestra los pasos para asignar roles.](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

        **Paso 3**: [cree una identidad de credencial](how-tos/credential-entity.md) en Metrics Advisor, para que pueda elegir esa entidad al agregar una fuente de distribución de datos para el tipo de autenticación de entidad de servicio. 
        
    * **Entidad de servicio de Key Vault**: Key Vault ayuda a proteger las claves criptográficas y los valores secretos que usan los servicios y aplicaciones en la nube. Con Key Vault, puede cifrar valores de claves y secretos. Primero cree una entidad de servicio y, a continuación, almacénela en un almacén de claves. Para más información, vea [Creación de una entidad de credencial para la entidad de servicio de Key Vault](how-tos/credential-entity.md#sp-from-kv). El nombre de cuenta es el mismo que el tipo de autenticación básico.

* **Clave de cuenta** (solo necesaria para el tipo de autenticación básico): especifique la clave de cuenta para acceder a Azure Data Lake Storage Gen2. Puede encontrarla en el recurso de la cuenta de Azure Storage (Azure Data Lake Storage Gen2) en **Claves de acceso**.

* **Nombre del sistema de archivos (contenedor)** : para Metrics Advisor, almacena los datos de series temporales como archivos de blob (un blob por marca de tiempo) en un solo contenedor. Este es el campo de nombre de contenedor. Puede encontrarlo en la instancia de la cuenta de almacenamiento de Azure (Azure Data Lake Storage Gen2). En **Data Lake Storage**, seleccione **Contenedores** y, después, verá el nombre del contenedor.

* **Plantilla de directorio**: esta es la plantilla de directorio del archivo de blob. Se admiten los siguientes parámetros:

   * `%Y` es el año, con formato `yyyy`.
   * `%m` es el mes, con formato `MM`.
   * `%d` es el día, con formato `dd`.
   * `%h` es la hora, con formato `HH`.
   * `%M` es el minuto, con formato `mm`.

   Ejemplo de consulta para una métrica diaria: `%Y/%m/%d`.

   Ejemplo de consulta para una métrica por hora: `%Y/%m/%d/%h`.

* **Plantilla de archivo**: Metrics Advisor usa una ruta de acceso para buscar el archivo JSON en Blob Storage. A continuación, se muestra un ejemplo de una plantilla de archivo de blob, que se usa para buscar el archivo JSON en Blob Storage: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. `%Y/%m` es la ruta de acceso y, si tiene `%d` en la ruta de acceso, puede agregarla después de `%m`. 
   
   Se admiten los siguientes parámetros:
   
   * `%Y` es el año, con formato `yyyy`.
   * `%m` es el mes, con formato `MM`.
   * `%d` es el día, con formato `dd`.
   * `%h` es la hora, con formato `HH`.
   * `%M` es el minuto, con formato `mm`.

   Metrics Advisor admite el esquema de datos en los archivos JSON como en el ejemplo siguiente:

   ``` JSON
   [
     {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
     {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
   ]
   ```

## <a name="span-ideventhubsazure-event-hubsspan"></a><span id="eventhubs">Azure Event Hubs</span>

* **Limitaciones**: tenga en cuenta las siguientes limitaciones con la integración.

   * La integración de Metrics Advisor con Event Hubs no admite actualmente más de tres fuentes de distribución de datos activas en una instancia de Metrics Advisor en versión preliminar pública.
   * Metrics Advisor siempre empezará a consumir mensajes del desplazamiento más reciente, incluso al reactivar una fuente de distribución de datos en pausa.
   
      * Se perderán los mensajes durante el período de pausa de la fuente de distribución de datos.
      * La hora de inicio de la ingesta de fuentes de distribución de datos se establece automáticamente en la marca de tiempo de hora universal coordinada actual, cuando se crea la fuente de distribución de datos. Esta hora es solo para fines de referencia.

   * Solo se puede usar una fuente de distribución de datos por cada grupo de consumidores. Para reutilizar un grupo de consumidores de otra fuente de distribución de datos eliminada, debe esperar al menos diez minutos después de la eliminación.
   * La cadena de conexión y el grupo de consumidores no se pueden modificar después de crear la fuente de distribución de datos.
   * Para los mensajes de Event Hubs, solo se admite JSON, y los valores JSON no pueden ser un objeto JSON anidado. El elemento de nivel superior puede ser un objeto JSON o una matriz JSON.
    
    Los mensajes válidos son los siguientes:

    ``` JSON
    Single JSON object 
    {
    "metric_1": 234, 
    "metric_2": 344, 
    "dimension_1": "name_1", 
    "dimension_2": "name_2"
    }
    ```
        
    ``` JSON
    JSON array 
    [
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 12.4,
            "location": "outdoor"
        },
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 24.8,
            "location": "indoor"
        }
    ]
    ```


* **Cadena de conexión**: vaya a la instancia de Event Hubs. A continuación, agregue una nueva directiva o elija una directiva de acceso compartido existente. Copie la cadena de conexión en el panel emergente.
    ![Captura de pantalla de Event Hubs](media/datafeeds/entities-eventhubs.jpg)
    
    ![Captura de pantalla de las directivas de acceso compartido](media/datafeeds/shared-access-policies.jpg)

    Este es un ejemplo de una cadena de conexión: 
    ```
    Endpoint=<Server>;SharedAccessKeyName=<SharedAccessKeyName>;SharedAccessKey=<SharedAccess Key>;EntityPath=<EntityPath>
    ```

* **Grupo de consumidores**: un [grupo de consumidores](../../event-hubs/event-hubs-features.md#consumer-groups) es una vista (estado, posición o desplazamiento) de un centro de eventos completo.
Lo encontrará en el menú **Grupos de consumidores** de una instancia de Azure Event Hubs. Un grupo de consumidores solo puede atender una fuente de distribución de datos. Cree un grupo de consumidores para cada fuente de distribución de datos.
* **Marca de tiempo** (opcional): Metrics Advisor usa la marca de tiempo de Event Hubs como marca de tiempo del evento, si el origen de datos del usuario no contiene un campo de marca de tiempo. El campo de marca de tiempo es opcional. Si no se elige ninguna columna de marca de tiempo, el servicio usa la hora en que se puso en cola como marca de tiempo.

    El campo de marca de tiempo debe coincidir con uno de estos dos formatos:
    
    * `YYYY-MM-DDTHH:MM:SSZ`
    * Número de segundos o milisegundos de la época de `1970-01-01T00:00:00Z`.
    
    La marca de tiempo se alineará a la izquierda con la granularidad. Por ejemplo, si la marca de tiempo es `2019-01-01T00:03:00Z`, la granularidad es de 5 minutos y, a continuación, Metrics Advisor alinea la marca de tiempo con `2019-01-01T00:00:00Z`. Si la marca de tiempo del evento es `2019-01-01T00:10:00Z`, Metrics Advisor usa la marca de tiempo directamente, sin ninguna alineación. 


## <a name="span-idlogazure-monitor-logsspan"></a><span id="log">Registros de Azure Monitor</span>

Los registros de Azure Monitor tienen los siguientes tipos de autenticación: básico, entidad de servicio y entidad de servicio de Key Vault.
* **Básico**: necesita rellenar los campos **Id. de inquilino**, **Id. de cliente**, **Secreto de cliente** e **Id. del área de trabajo**.
   Para obtener la información de **Id. de inquilino**, **Id. de cliente** y **Secreto de cliente**, vea [Registro de aplicación o API web](../../active-directory/develop/quickstart-register-app.md). Puede encontrar el **Id. del área de trabajo** en Azure Portal.
   
    ![Captura de pantalla que muestra dónde encontrar el identificador del área de trabajo en Azure Portal.](media/workspace-id.png)
    
* **Entidad de servicio**: una entidad de servicio es una instancia concreta creada a partir del objeto de aplicación, que hereda determinadas propiedades de ese objeto de aplicación. La entidad de servicio se crea en cada inquilino donde se usa la aplicación y hace referencia al objeto de aplicación único global. El objeto de entidad de servicio define lo que la aplicación puede hacer en el inquilino específico, quién puede acceder a la aplicación y a qué recursos tiene acceso la aplicación.
    
    **Paso 1**: cree y registre una aplicación de Azure AD y, a continuación, autorícela para acceder a una base de datos. Para más información, vea [Creación de un registro de aplicación de Azure Active Directory](/azure/data-explorer/provision-azure-ad-app).

    **Paso 2**: asigne roles.
    1. En Azure Portal, vaya al servicio **Cuentas de almacenamiento**.
    2. Seleccione **Access Control (IAM)** .
    3. Seleccione **+ Agregar** y, después, seleccione **Agregar asignación de roles** en el menú.
    4. Establezca el campo **Seleccionar** en el nombre de la aplicación de Azure AD y establezca el rol en **Colaborador de datos de blobs de almacenamiento**. Después, seleccione **Guardar**.
    
        ![Captura de pantalla que muestra cómo asignar roles.](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

    
    **Paso 3**: [cree una identidad de credencial](how-tos/credential-entity.md) en Metrics Advisor, para que pueda elegir esa entidad al agregar una fuente de distribución de datos para el tipo de autenticación de entidad de servicio. 
        
* **Entidad de servicio de Key Vault**: Key Vault ayuda a proteger las claves criptográficas y los valores secretos que usan los servicios y aplicaciones en la nube. Con Key Vault, puede cifrar valores de claves y secretos. Primero cree una entidad de servicio y, a continuación, almacénela en un almacén de claves. Para más información, vea [Creación de una entidad de credencial para la entidad de servicio de Key Vault](how-tos/credential-entity.md#sp-from-kv). 

* **Consulta**: especifique la consulta. Para más información, vea [Consultas de registro en Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

    Consulta de ejemplo:

    ``` Kusto
    [TableName]
    | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd)
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    Para obtener más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).

## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Cadena de conexión**: los tipos de autenticación para Azure SQL Database y SQL Server son básico, identidad administrada, cadena de conexión de Azure SQL, entidad de servicio y entidad de servicio de Key Vault.
    
    * **Básico**: Metrics Advisor acepta una [cadena de conexión de estilo ADO.NET](/dotnet/framework/data/adonet/connection-string-syntax) para un origen de datos de SQL Server.
    Este es un ejemplo de una cadena de conexión: 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User ID=<user-name>;Password=<password>
        ```
    
    * <span id='jump'>**Identidad administrada**</span>: la identidad administrada para los recursos de Azure pueden autorizar el acceso a los datos de blobs y colas. Para ello, se usan las credenciales de Azure AD de las aplicaciones que se ejecutan en máquinas virtuales de Azure, aplicaciones de funciones, conjuntos de escalado de máquinas virtuales y otros servicios. Si usa la identidad administrada para recursos de Azure y la autenticación de Azure AD, puede evitar el almacenamiento de credenciales con las aplicaciones que se ejecutan en la nube. Para [habilitar la entidad administrada](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md), siga estos pasos:
    1. La habilitación de una identidad administrada asignada por el sistema es una experiencia de un solo clic. En Azure Portal, en el área de trabajo de Metrics Advisor, vaya a **Configuración** > **Identidad** > **Asignada por el sistema**. Establezca el estado como **Activado**. 
    
        ![Captura de pantalla que muestra cómo establecer el estado como activado.](media/datafeeds/set-identity-status.png)

    1. Habilite la autenticación de Azure AD. En Azure Portal, para el origen de datos, vaya a **Configuración** > **Administrador de Active Directory**. Seleccione **Establecer administrador** y seleccione una **cuenta de usuario de Azure AD** para que se convierta en administrador del servidor. A continuación, elija **Seleccionar**.
    
        ![Captura de pantalla que muestra cómo establecer el administrador.](media/datafeeds/set-admin.png)

    1. Habilite la identidad administrada en Metrics Advisor. Puede editar una consulta en la herramienta de administración de bases de datos o en Azure Portal.
    
        **Herramienta de administración**: en la herramienta de administración de bases de datos, seleccione **Active Directory - Universal compatible con MFA** en el campo de autenticación. En el campo **Nombre de usuario**, escriba el nombre de la cuenta de Azure AD que estableció como administradora del servidor en el paso 2. Por ejemplo, podría ser `test@contoso.com`.
    
        ![Captura de pantalla que muestra cómo establecer los detalles de conexión.](media/datafeeds/connection-details.png)
        
        **Azure Portal**: en la base de datos SQL, seleccione **Editor de consultas** e inicie sesión en la cuenta de administrador.
        ![Captura de pantalla que muestra cómo editar la consulta en Azure Portal](media/datafeeds/query-editor.png).

        A continuación, en la ventana de consulta, ejecute lo siguiente (tenga en cuenta que esto es lo mismo para el método de la herramienta de administración):
    
        ```
        CREATE USER [MI Name] FROM EXTERNAL PROVIDER
        ALTER ROLE db_datareader ADD MEMBER [MI Name]
        ```
    
        > [!NOTE]
        > `MI Name` es el nombre de la identidad administrada en Metrics Advisor (para la entidad de servicio, se debe reemplazar por el nombre de la entidad de servicio). Para más información, vea [Autorización con una identidad administrada](../../storage/common/storage-auth-aad-msi.md#enable-managed-identities-on-a-vm). 
            
        Este es un ejemplo de una cadena de conexión: 
       
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
        
    * **Cadena de conexión de Azure SQL**: 
      

        Este es un ejemplo de una cadena de conexión: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;User ID=<user-name>;Password=<password>
        ```
  

    * **Entidad de servicio**: una entidad de servicio es una instancia concreta creada a partir del objeto de aplicación, que hereda determinadas propiedades de ese objeto de aplicación. La entidad de servicio se crea en cada inquilino donde se usa la aplicación y hace referencia al objeto de aplicación único global. El objeto de entidad de servicio define lo que la aplicación puede hacer en el inquilino específico, quién puede acceder a la aplicación y a qué recursos tiene acceso la aplicación.
    
        **Paso 1**: cree y registre una aplicación de Azure AD y, a continuación, autorícela para acceder a una base de datos. Para más información, vea [Creación de un registro de aplicación de Azure Active Directory](/azure/data-explorer/provision-azure-ad-app).

        **Paso 2**: siga los pasos documentados anteriormente en [Identidad administrada en SQL Server](#jump). 

        **Paso 3**: [cree una identidad de credencial](how-tos/credential-entity.md) en Metrics Advisor, para que pueda elegir esa entidad al agregar una fuente de distribución de datos para el tipo de autenticación de entidad de servicio. 

        Este es un ejemplo de una cadena de conexión: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **Entidad de servicio de Key Vault**: Key Vault ayuda a proteger las claves criptográficas y los valores secretos que usan los servicios y aplicaciones en la nube. Con Key Vault, puede cifrar valores de claves y secretos. Primero cree una entidad de servicio y, a continuación, almacénela en un almacén de claves. Para más información, vea [Creación de una entidad de credencial para la entidad de servicio de Key Vault](how-tos/credential-entity.md#sp-from-kv). También puede encontrar la cadena de conexión en el recurso de Azure SQL Server, en **Configuración** > **Cadenas de conexión**.
        
        Este es un ejemplo de una cadena de conexión: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **Consulta**: use una consulta SQL para obtener y formular datos en datos de series temporales multidimensionales. Puede usar `@IntervalStart` y `@IntervalEnd` en la consulta para ayudar a obtener el valor de las métricas esperado en un intervalo. Deben tener el formato siguiente: `yyyy-MM-ddTHH:mm:ssZ`.


    Consulta de ejemplo:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **Cadena de conexión**: cree una dirección URL de firma de acceso compartido (SAS) y rellénela aquí. La manera más sencilla de generar una dirección URL de SAS es con Azure Portal. En primer lugar, en **Configuración**, vaya a la cuenta de almacenamiento a la que quiere acceder. A continuación, seleccione **Firma de acceso compartido**. Seleccione las casillas **Tabla** y **Objeto** y, después, seleccione **Generar la cadena de conexión y SAS**. En el área de trabajo de Metrics Advisor, copie y pegue la **URL de SAS de Table service** en el cuadro de texto.

    ![Captura de pantalla que muestra cómo generar la firma de acceso compartido en Azure Table Storage.](media/azure-table-generate-sas.png)

* **Nombre de la tabla**: Especifique una tabla en la que realizar la consulta. Puede encontrarlo en la instancia de la cuenta de almacenamiento de Azure. En la sección **Table service**, seleccione **Tablas**.

* **Consulta**: puede usar `@IntervalStart` y `@IntervalEnd` en la consulta para ayudar a obtener el valor de las métricas esperado en un intervalo. Deben tener el formato siguiente: `yyyy-MM-ddTHH:mm:ssZ`.

    Consulta de ejemplo:
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    Para más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).


## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Cadena de conexión**: cadena de conexión para acceder a InfluxDB.
* **Base de datos**: Base de datos que se va a consultar.
* **Consultar** Consulta para obtener y formular datos en datos de series temporales multidimensionales para la ingesta.

    Consulta de ejemplo:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
Para obtener más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).

* **Nombre de usuario**: Opcional para la autenticación. 
* **Contraseña**: Opcional para la autenticación. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Cadena de conexión**: cadena de conexión para acceder a MongoDB.
* **Base de datos**: Base de datos que se va a consultar.
* **Consulta**: un comando para obtener y formular datos en datos de series temporales multidimensionales para la ingesta. Compruebe el comando en [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html).

    Consulta de ejemplo:

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Cadena de conexión**: cadena de conexión para acceder a MySQL DB.
* **Consultar** Consulta para obtener y formular datos en datos de series temporales multidimensionales para la ingesta.

    Consulta de ejemplo:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    Para obtener más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Cadena de conexión**: cadena de conexión para acceder a PostgreSQL DB.
* **Consultar** Consulta para obtener y formular datos en datos de series temporales multidimensionales para la ingesta.

    Consulta de ejemplo:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    Para obtener más información, consulte el [tutorial sobre cómo escribir una consulta válida](tutorials/write-a-valid-query.md).
    
## <a name="span-idcsvlocal-files-csvspan"></a><span id="csv">Archivos locales (CSV)</span>

> [!NOTE]
> Esta característica solo se usa para la evaluación rápida del sistema centrada en la detección de anomalías. Solo acepta datos estáticos de un archivo CSV local y realiza la detección de anomalías en datos de serie temporal única. Para analizar métricas multidimensionales, incluida la ingesta de datos en tiempo real, la notificación de anomalías, el análisis de la causa principal y el análisis de incidentes entre métricas, use otros orígenes de datos admitidos.

**Requisitos de los datos en CSV**:
- Tener al menos una columna, que representa las medidas que se analizarán. Para una experiencia de usuario mejorada y más rápida, pruebe un archivo CSV que contenga dos columnas: una columna de marca de tiempo y una columna de métrica. El formato de marca de tiempo debe ser el siguiente: `2021-03-30T00:00:00Z`, y la parte `seconds` es mejor que sea `:00Z`. La granularidad de tiempo entre cada registro debe ser la misma.
- La columna de marca de tiempo es opcional. Si no hay ninguna marca de tiempo, Metrics Advisor usará la marca de tiempo a partir de hoy (hora universal coordinada `00:00:00`). El servicio asigna cada medida de la fila en un intervalo de una hora.
- No se realiza ninguna reordenación ni el relleno de espacios vacíos durante la ingesta de datos. Asegúrese de que los datos del archivo CSV siguen un orden de marca de tiempo **ascendente (ASC)** .
 
## <a name="next-steps"></a>Pasos siguientes

* Mientras espera a que se ingieran los datos de métricas en el sistema, lea sobre [cómo administrar las configuraciones de fuentes de distribución de datos](how-tos/manage-data-feeds.md).
* Cuando se ingieren los datos de métricas, puede [configurar métricas y ajustar la configuración de detección de forma precisa](how-tos/configure-metrics.md).
