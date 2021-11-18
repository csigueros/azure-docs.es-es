---
title: Configuración y uso de Azure Synapse Link para Azure Cosmos DB (versión preliminar)
description: Aprenda a habilitar Synapse Link para las cuentas de Azure Cosmos DB, crear un contenedor con el almacén analítico habilitado, conectar la base de datos de Azure Cosmos a un área de trabajo de Synapse y ejecutar consultas.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
ms.openlocfilehash: 6baedff1ef084940b91c40b57572844f4b63de4b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319403"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Configuración y uso de Azure Synapse Link para Azure Cosmos DB (versión preliminar)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link para Azure Cosmos DB](synapse-link.md) es una funcionalidad de procesamiento analítico y transaccional híbrido (HTAP) nativo de nube que permite ejecutar análisis casi en tiempo real de datos operativos en Azure Cosmos DB. Synapse Link crea una integración perfecta y sin contratiempos entre Azure Cosmos DB y Azure Synapse Analytics.

Azure Synapse Link está disponible para cuentas de API de SQL de Azure Cosmos DB o de API de Azure Cosmos DB para Mongo DB. Siga estos pasos para ejecutar consultas analíticas con Azure Synapse Link para Azure Cosmos DB:

* [Habilitación de Azure Synapse Link para las cuentas de Azure Cosmos DB](#enable-synapse-link)
* [Crear un contenedor habilitado para el almacén analítico.](#create-analytical-ttl)
* [Habilitación del almacén analítico en un contenedor existente](#update-analytical-ttl)
* [Opcional: Actualización del TTL del almacén analítico de un contenedor](#update-analytical-ttl)
* [Conexión de la base de datos de Azure Cosmos a un área de trabajo de Azure Synapse](#connect-to-cosmos-database)
* [Consulta del almacén analítico mediante un grupo de Spark de Azure Synapse](#query-analytical-store-spark)
* [Consulta del almacén analítico mediante un grupo de SQL sin servidor de Azure Synapse](#query-analytical-store-sql-on-demand)
* [Uso de un grupo de SQL sin servidor de Azure Synapse para analizar y visualizar datos en Power BI](#analyze-with-powerbi)

También puede consultar el módulo de información sobre cómo [configurar Azure Synapse Link para Azure Cosmos DB](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/).

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Habilitación de Azure Synapse Link para cuentas de Azure Cosmos DB

> [!NOTE]
> Si quiere usar claves administradas por el cliente con Azure Synapse Link, debe configurar la identidad administrada de la cuenta en la directiva de acceso de Azure Key Vault antes de habilitar Synapse Link en la cuenta. Para más información, consulte el artículo [Configuración de claves administradas por el cliente para una cuenta de Azure Cosmos con Azure Key Vault](how-to-setup-cmk.md#using-managed-identity).

> [!NOTE]
> Si desea usar el esquema de fidelidad total para las cuentas de API de SQL (CORE), no puede usar Azure Portal para habilitar Synapse Link. Esta opción no se puede cambiar después de habilitar Synapse Link en su cuenta y, para establecerla, debe usar la CLI de Azure o PowerShell. Para obtener más información, consulte la [documentación de representación del esquema del almacén analítico](analytical-store-introduction.md#schema-representation). 

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

1. [Cree una cuenta de Azure](create-sql-api-dotnet.md#create-account), o bien seleccione una cuenta de Azure Cosmos DB ya existente.

1. Vaya a la cuenta de Azure Cosmos DB y abra el panel **Características**.

1. Seleccione **Synapse Link** en la lista de características.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Búsqueda de la característica Synapse Link":::

1. A continuación, se le pedirá que habilite Synapse Link en su cuenta. Seleccione **Habilitar**. Este proceso puede tardar de 1 a 5 minutos en completarse.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Habilitación de la característica Synapse Link":::

1. Ahora su cuenta está habilitada para usar Synapse Link. A continuación, consulte cómo crear contenedores habilitados para el almacén analítico para iniciar de forma automática la replicación de sus datos operativos desde el almacén transaccional hacia el almacén analítico.

> [!NOTE]
> La activación de Synapse Link no activa automáticamente el almacén analítico. Una vez que habilite Synapse Link en la cuenta de Cosmos DB, habilite el almacén analítico en contenedores para empezar a usar Synapse Link. 

### <a name="command-line-tools"></a>Herramientas de línea de comandos

Habilite Synapse Link en la cuenta de API de SQL de Cosmos DB o de API de MongoDB mediante la CLI de Azure o PowerShell.

#### <a name="azure-cli"></a>Azure CLI

Use `--enable-analytical-storage true` para operaciones de **creación** o **actualización**. También debe elegir el tipo de esquema de representación. En las cuentas de API de SQL puede usar `--analytical-storage-schema-type` con los valores `FullFidelity` o `WellDefined`. En las cuentas de API de MongoDB, use siempre `--analytical-storage-schema-type FullFidelity`.

* [Creación de una nueva cuenta de Azure Cosmos DB con Synapse Link habilitado](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [Actualización de una cuenta de Azure Cosmos DB existente para habilitar Synapse Link](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

#### <a name="powershell"></a>PowerShell

Use `EnableAnalyticalStorage true` para operaciones de **creación** o **actualización**. También debe elegir el tipo de esquema de representación. En las cuentas de API de SQL puede usar `--analytical-storage-schema-type` con los valores `FullFidelity` o `WellDefined`. En las cuentas de API de MongoDB, use siempre `-AnalyticalStorageSchemaType FullFidelity`.

* [Creación de una nueva cuenta de Azure Cosmos DB con Synapse Link habilitado](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [Actualización de una cuenta de Azure Cosmos DB existente para habilitar Synapse Link](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


## <a name="create-an-analytical-store-enabled-container"></a><a id="create-analytical-ttl"></a> Creación de un contenedor habilitado para el almacén analítico

Puede activar el almacén analítico al crear un contenedor de Azure Cosmos DB mediante una de las siguientes opciones.

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) o en el [Explorador de Azure Cosmos DB](https://cosmos.azure.com/).

1. Vaya a la cuenta de Azure Cosmos DB y abra la pestaña **Explorador de datos**.

1. Seleccione **Nuevo contenedor** y escriba un nombre para la base de datos, el contenedor, la clave de partición y los detalles de capacidad de proceso. Active la opción **Analytical store** (Almacén analítico). Después de habilitar el almacén analítico, se crea un contenedor con la propiedad `analytical TTL` establecida en el valor predeterminado de -1 (retención de datos infinita). Este almacén analítico conserva todas las versiones históricas de registros y se puede modificar más adelante.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Activación de un almacén analítico para un contenedor de Azure Cosmos DB":::

1. Si no ha habilitado Synapse Link anteriormente en esta cuenta, se le pedirá que lo haga, ya que se trata de un requisito previo para crear un contenedor habilitado para almacén analítico. Si se le solicita, seleccione **Enable Synapse Link** (Habilitar Synapse Link). Este proceso puede tardar de 1 a 5 minutos en completarse.

1. Seleccione **Aceptar** para crear un contenedor de Azure Cosmos DB habilitado para el almacén analítico.

1. Una vez creado el contenedor, compruebe que se ha habilitado el almacén analítico; para ello, haga clic en **Configuración**, justo debajo de Documentos en Data Explorer y compruebe si está activada la opción **Período de vida del almacén analítico**.

### <a name="azure-cosmos-db-sdks"></a>SDK de Azure Cosmos DB

Establezca la propiedad `analytical TTL` en el valor necesario para crear un contenedor habilitado para el almacén analítico. Para obtener la lista de valores permitidos, vea el artículo [Valores admitidos del TTL analítico](analytical-store-introduction.md#analytical-ttl).

#### <a name="net-sdk"></a>.NET SDK

En el código siguiente se crea un contenedor con un almacén analítico mediante el SDK de .NET. Establezca la propiedad `AnalyticalStoreTimeToLiveInSeconds` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

#### <a name="java-v4-sdk"></a>SDK de Java V4

En el código siguiente se crea un contenedor con un almacén analítico mediante el SDK de Java V4. Establezca la propiedad `AnalyticalStoreTimeToLiveInSeconds` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.


```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

#### <a name="python-v4-sdk"></a>SDK para Python v4

El código siguiente crea un contenedor con almacén analítico mediante el SDK de Python V4. Establezca la propiedad `analytical_storage_ttl` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos as cosmos
import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

# Client
client = cosmos_client.CosmosClient(HOST,  KEY )

# Database client
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled
try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```
### <a name="command-line-tools"></a>Herramientas de línea de comandos

Establezca la propiedad `analytical TTL` en el valor necesario para crear un contenedor habilitado para el almacén analítico. Para obtener la lista de valores permitidos, vea el artículo [Valores admitidos del TTL analítico](analytical-store-introduction.md#analytical-ttl).

#### <a name="azure-cli"></a>Azure CLI

Las siguientes opciones crean un contenedor con almacén analítico mediante la CLI de Azure. Establezca la propiedad `--analytical-storage-ttl` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.

* [Creación de una colección de MongoDB de Azure Cosmos DB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Creación de un contenedor de API de SQL de Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) 

#### <a name="powershell"></a>PowerShell

Las siguientes opciones crean un contenedor con almacén analítico mediante PowerShell. Establezca la propiedad `-AnalyticalStorageTtl` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.

* [Creación de una colección de MongoDB de Azure Cosmos DB](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Creación de un contenedor de API de SQL de Azure Cosmos DB](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer)


## <a name="enable-analytical-store-on-an-existing-container"></a><a id="update-analytical-ttl"></a> Habilitación del almacén analítico en un contenedor existente

> [!NOTE]
> Debido a restricciones de capacidad a corto plazo, debe registrarse para habilitar Synapse Link en los contenedores existentes. En función de las solicitudes pendientes, la aprobación de esta solicitud puede tardar entre un día y una semana. A continuación se proporcionan instrucciones para comprobar el estado de la solicitud. Si tiene algún problema o pregunta, póngase en contacto con [cosmosdbsynapselink@microsoft.com](mailto:cosmosdbsynapselink@microsoft.com). Este paso es necesario una vez por suscripción, y todas las cuentas de base de datos nuevas también tienen esta capacidad habilitada.

> [!NOTE]
> Puede activar el almacén analítico en contenedores existentes de API de SQL de Azure Cosmos DB. Esta capacidad está disponible con carácter general y se puede usar en cargas de trabajo de producción.

 Tenga en cuenta los siguientes detalles al habilitar Synapse Link en los contenedores existentes:

* El mismo aislamiento de rendimiento del proceso de sincronización automática del almacén analítico se aplica a la sincronización inicial, y no hay ningún impacto en el rendimiento en la carga de trabajo de OLTP.

* La sincronización inicial de un contenedor con el tiempo total del almacén analítico varía en función del volumen de datos y de la complejidad de los documentos. Este proceso puede tardar desde unos segundos hasta varios días. Use Azure Portal para supervisar el progreso de la migración.

* El rendimiento del contenedor, o la cuenta de base de datos, también influye en el tiempo total de sincronización inicial. Aunque las RU/s no se usan en esta migración, el total de RU/s disponibles influye en el rendimiento del proceso. Puede aumentar temporalmente las RU disponibles del entorno para acelerar el proceso.

* No va a poder consultar el almacén analítico de un contenedor existente mientras Synapse Link se está habilitando en ese contenedor. La carga de trabajo de OLTP no se ve afectada, y puede seguir leyendo los datos con normalidad. Los datos ingeridos después del inicio de la sincronización inicial se combinan en el almacén analítico mediante el proceso de sincronización automática normal del almacén analítico.

* Actualmente no se admiten colecciones de API de MongoDB existentes. La alternativa es migrar los datos a una nueva colección, creada con el almacén analítico activado.
 
> [!NOTE]
> Actualmente no es posible desactivar el almacén analítico de un contenedor. Haga clic [aquí](analytical-store-introduction.md#analytical-store-pricing) para obtener más información sobre los precios del almacén analítico.

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) o en el [Explorador de Azure Cosmos DB](https://cosmos.azure.com/).
2. Vaya a la cuenta de Azure Cosmos DB y abra la pestaña **Synapse Link"** de la sección **Integraciones**. En esta pestaña, puede:
3. Hacer clic en **Registrar** para solicitar la aprobación de la suscripción. Para ver el estado de la solicitud, vuelva a este mismo panel del portal.
4. Cuando se apruebe, verá la lista de contenedores de la cuenta y podrá seleccionar los que tengan habilitado el almacén analítico.
5. Opcionalmente, puede ir a la pestaña **Power BI**, en la sección **Integraciones**, para crear paneles de Power BI en los contenedores habilitados para Synapse Link.


### <a name="command-line-tools"></a>Herramientas de línea de comandos

Establezca la propiedad `analytical TTL` en el valor necesario para crear un contenedor habilitado para el almacén analítico. Para obtener la lista de valores permitidos, vea el artículo [Valores admitidos del TTL analítico](analytical-store-introduction.md#analytical-ttl).


### <a name="azure-cli"></a>Azure CLI

Siga estos pasos para habilitar el almacén analítico en un contenedor existente mediante la CLI de Azure. Establezca la propiedad `--analytical-storage-ttl` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.

* [Registre para aprobación](/cli/azure/feature/registration) mediante `az feature registration create --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration`. 
* [Compruebe el estado de la solicitud](/cli/azure/feature/registration) mediante `az feature registration show --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration`.
* [Actualice el TTL analítico](/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_update&preserve-view=true) a `-1` después de la aprobación de la solicitud.
* Compruebe el estado de la migración en Azure Portal.

### <a name="powershell"></a>PowerShell

Use los pasos siguientes para habilitar el almacén analítico en un contenedor existente mediante PowerShell. Establezca la propiedad `-AnalyticalStorageTtl` en el valor necesario en segundos o use `-1` para una retención infinita. Este valor se puede cambiar más adelante.

* [Registre para aprobación](/powershell/module/az.resources/register-azproviderfeature) mediante `Register-AzProviderFeature -ProviderName "Microsoft.DocumentDB" -FeatureName "AnalyticalStoreMigration"`.
* [Compruebe el estado de la solicitud](/powershell/module/az.resources/get-azproviderfeature).
* [Actualice el TTL analítico](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer) a `-1` después de la aprobación de la solicitud.
* Compruebe el estado de la migración en Azure Portal.



## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Opcional: actualización del período de vida del almacén analítico

Una vez habilitado el almacén analítico con un valor de TTL determinado, es posible que quiera actualizarlo a otro valor válido. Puede actualizar el valor desde Azure Portal, mediante la CLI de Azure, desde PowerShell o con los SDK de Cosmos DB. Para obtener información sobre las distintas opciones de configuración de TTL analítico, consulte el artículo [valores de TTL analítico admitidos](analytical-store-introduction.md#analytical-ttl).


### <a name="azure-portal"></a>Azure portal

Si ha creado un contenedor habilitado para el almacén analítico mediante Azure Portal, contiene un valor predeterminado `analytical TTL` de `-1`. Siga estos pasos para actualizar dicho valor:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) o en el [Explorador de Azure Cosmos DB](https://cosmos.azure.com/).
1. Vaya a la cuenta de Azure Cosmos DB y abra la pestaña **Explorador de datos**.
1. Seleccione un contenedor existente que tenga habilitado el almacén analítico. Expanda el contenedor y modifique los valores siguientes:
   1. Abra la ventana **Escala y configuración**.
   1. En **Configuración**, busque **Período de vida de almacenamiento analítico**.
   1. Seleccione **Activado (valor no predeterminado)** o seleccione **Activado** y establezca un valor para el período de vida.
   1. Haga clic en **Guardar** para guardar los cambios.


### <a name="net-sdk"></a>.NET SDK

En el código siguiente se muestra cómo actualizar el TTL para el almacén analítico mediante el SDK de .NET:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>SDK de Java V4

En el código siguiente se muestra cómo actualizar el TTL para el almacén analítico mediante el SDK de Java V4:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>SDK para Python v4

Actualmente no se admite.


### <a name="azure-cli"></a>Azure CLI

Los vínculos siguientes muestran cómo actualizar el TTL analítico de los contenedores mediante la CLI de Azure:

* [API de Azure Cosmos DB para Mongo DB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [API de SQL de Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

Los vínculos siguientes muestran cómo actualizar el TTL analítico de los contenedores mediante PowerShell:

* [API de Azure Cosmos DB para Mongo DB](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [API de SQL de Azure Cosmos DB](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Conexión a un área de trabajo de Synapse

Siga las instrucciones del artículo [Conexión a Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sobre cómo acceder a una base de datos de Azure Cosmos DB desde Azure Synapse Analytics Studio con Azure Synapse Link.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Consulta del almacén analítico mediante Apache Spark para Azure Synapse Analytics

Siga las instrucciones que se indican en el artículo [Consulta del almacén analítico de Azure Cosmos DB mediante Spark 3](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md) para obtener información sobre cómo realizar consultas con Spark 3 en Synapse. En este artículo se ofrecen algunos ejemplos sobre cómo puede interactuar con el almacén analítico desde los gestos de Synapse. Dichos gestos se muestran cuando se hace clic con el botón secundario en un contenedor. Con ellos, puede generar código rápidamente y adaptarlo a sus necesidades. También son ideales para detectar datos con un solo clic.

Para la integración de Spark 2, use la instrucción del artículo [Consulta del almacén analítico de Azure Cosmos DB mediante Spark 2](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md).

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Consultar el almacén analítico mediante un grupo de SQL sin servidor en Azure Synapse Analytics

El grupo de SQL sin servidor permite consultar y analizar los datos de los contenedores de Azure Cosmos DB que están habilitados con Azure Synapse Link. Se pueden analizar los datos casi en tiempo real sin que afecte al rendimiento de las cargas de trabajo transaccionales. Ofrece una sintaxis T-SQL familiar para consultar los datos del almacén analítico y la conectividad integrada en una amplia gama de herramientas de consulta ad hoc y de BI a través de la interfaz de T-SQL. Para obtener más información, vea el artículo [Consulta del almacén analítico mediante un grupo de SQL sin servidor](../synapse-analytics/sql/query-cosmos-db-analytical-store.md).

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Uso de un grupo de SQL sin servidor para analizar datos en Power BI y visualizarlos

Se puede crear una base de datos de grupo de SQL sin servidor y vistas a través de Synapse Link para Azure Cosmos DB. Más adelante puede consultar los contenedores de Azure Cosmos DB y compilar un modelo con Power BI en función de esas vistas para reflejar esa consulta. No hay ningún impacto en el rendimiento ni en el costo de las cargas de trabajo transaccionales y tampoco existe ninguna complejidad en la administración de canalizaciones de ETL. Puede usar los modos [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) o [importación](/power-bi/connect-data/service-dataset-modes-understand#import-mode). Para obtener más información, vea el artículo sobre cómo usar un [grupo de SQL sin servidor para analizar los datos de Azure Cosmos DB con Synapse Link](synapse-link-power-bi.md).

## <a name="configure-custom-partitioning"></a>Configuración de particiones personalizadas

La creación de particiones personalizadas permite crear particiones de los datos del almacén analítico en campos que se usan normalmente como filtros en consultas analíticas, lo que mejora el rendimiento de las consultas.Para más información, vea los artículos de [introducción a la creación de particiones personalizadas](custom-partitioning-analytical-store.md) y sobre [cómo configurar la creación de particiones personalizadas](configure-custom-partitioning.md).

## <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure

La [plantilla de Azure Resource Manager](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) crea una cuenta de Azure Cosmos DB habilitada para Synapse Link para la API de SQL. Esta plantilla crea una cuenta de Core (SQL) API en una región con un contenedor configurado con TTL analítico habilitado y una opción para usar la capacidad de proceso manual o de escalado automático. Para implementar esta plantilla, haga clic en **Implementar en Azure** en la página Léame.

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Introducción a Azure Synapse Link: ejemplos

Puede encontrar ejemplos para empezar a trabajar con Azure Synapse Link en [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Estos presentan soluciones de un extremo a otro con escenarios de IoT y de venta minorista. También puede encontrar los ejemplos correspondientes a Azure Cosmos DB API para MongoDB en el mismo repositorio en la carpeta [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples). 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la siguiente documentación:

* También puede consultar el módulo de información sobre cómo [configurar Azure Synapse Link para Azure Cosmos DB](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/).

* [Introducción al almacén analítico de Azure Cosmos DB.](analytical-store-introduction.md)

* [Preguntas frecuentes sobre Synapse Link para Azure Cosmos DB.](synapse-link-frequently-asked-questions.yml)

* [Apache Spark en Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Compatibilidad del entorno de ejecución del grupo de SQL sin servidor con Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
