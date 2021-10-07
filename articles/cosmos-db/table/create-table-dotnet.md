---
title: 'Inicio rápido: Table API con .NET: Azure Cosmos DB'
description: En esta guía de inicio rápido, se muestra cómo acceder a Table API de Azure Cosmos DB desde una aplicación de .NET mediante el SDK Azure.Data.Tables.
author: DavidCBerry13
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 08/25/2021
ms.author: daberry
ms.custom: devx-track-csharp
ms.openlocfilehash: 66907bcaef5818990d5a4e855f4cbc98624e2b90
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081099"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Inicio rápido: creación de una aplicación de Table API con el SDK de .NET y Azure Cosmos DB

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

En esta guía de inicio rápido, se muestra cómo acceder a [Table API](introduction.md) de Azure Cosmos DB desde una aplicación de .NET.  Table API de Cosmos DB es un almacén de datos sin esquema que permite a las aplicaciones almacenar datos NoSQL estructurados en la nube.  Dado que los datos se almacenan en un diseño sin esquema, las nuevas propiedades (columnas) se agregan automáticamente a la tabla cuando se agrega un objeto con un nuevo atributo a la tabla.

Las aplicaciones de .NET pueden acceder a Table API de Cosmos DB mediante el paquete NuGet [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/).  El paquete [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) es una biblioteca de [.NET Standard 2.0](/dotnet/standard/net-standard) que funciona con aplicaciones de .NET Framework (4.7.2 y versiones posteriores) y .NET Core (2.0 y versiones posteriores).

## <a name="prerequisites"></a>Requisitos previos

La aplicación de ejemplo está escrita en [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet/3.1), aunque los principios se aplican tanto a las aplicaciones de .NET Framework como a las de .NET Core.  Puede usar [Visual Studio](https://www.visualstudio.com/downloads/), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) o [Visual Studio Code](https://code.visualstudio.com/) como IDE.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="sample-application"></a>Aplicación de ejemplo

La aplicación de ejemplo de este tutorial se puede clonar o descargar desde el repositorio [https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet](https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet).  En el repositorio de ejemplo se incluyen tanto una aplicación de inicio como una completa.

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet
```

La aplicación de ejemplo usa datos meteorológicos como ejemplo para demostrar las funcionalidades de Table API. Los objetos que representan observaciones meteorológicas se almacenan y recuperan mediante Table API, incluido el almacenamiento de objetos con propiedades adicionales para mostrar las funcionalidades sin esquema de Table API.

:::image type="content" source="./media/create-table-dotnet/table-api-app-finished-application-720px.png" alt-text="Captura de pantalla de la aplicación finalizada que muestra los datos almacenados en una tabla de Cosmos DB mediante Table API." lightbox="./media/create-table-dotnet/table-api-app-finished-application.png":::

## <a name="1---create-an-azure-cosmos-db-account"></a>1 - Creación de una cuenta de Azure Cosmos DB

En primer lugar, debe crear una cuenta de Table API de Cosmos DB que contendrá las tablas usadas en la aplicación.  Esto se puede realizar mediante Azure Portal, la CLI de Azure o Azure PowerShell.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Inicie sesión en [Azure Portal](https://portal.azure.com/) y siga estos pasos para crear una cuenta de Cosmos DB.

| Instrucciones    | Instantánea |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="Captura de pantalla que muestra cómo usar el cuadro de búsqueda de la barra de herramientas superior para buscar cuentas de Cosmos DB en Azure." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="Captura de pantalla que muestra la ubicación del botón Crear en la página de cuentas de Cosmos DB en Azure." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="Captura de pantalla que muestra la opción Tabla de Azure como opción correcta para seleccionar." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-4.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="Captura de pantalla que muestra cómo rellenar los campos de la página de creación de la cuenta de Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Las cuentas de Cosmos DB se crean con el comando [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create). Debe incluir la opción `--capabilities EnableTable` para habilitar en almacenamiento de tablas en Cosmos DB.  Como todos los recursos de Azure deben estar incluidos en un grupo de recursos, el siguiente fragmento de código también crea un grupo de recursos para la cuenta de Cosmos DB.

Los nombres de cuenta de Cosmos DB deben tener entre 3 y 44 caracteres y solo pueden contener letras minúsculas, números y el carácter de guion (-).  Los nombres de cuenta de Cosmos DB también deben ser únicos en Azure.

Los comandos de la CLI de Azure se pueden ejecutar en [Azure Cloud Shell](https://shell.azure.com) o en una estación de trabajo con la [CLI de Azure instalada](/cli/azure/install-azure-cli).

Normalmente, el proceso de creación de la cuenta de Cosmos DB tarda varios minutos en completarse.

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Las cuentas de Azure Cosmos DB se crean mediante el cmdlet [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount). Debe incluir la opción `-ApiKind "Table"` para habilitar en almacenamiento de tablas en Cosmos DB.  Como todos los recursos de Azure deben estar incluidos en un grupo de recursos, el siguiente fragmento de código también crea un grupo de recursos para la cuenta de Azure Cosmos DB.

Los nombres de cuenta de Azure Cosmos DB deben tener entre 3 y 44 caracteres y solo pueden contener letras minúsculas, números y el carácter de guion (-).  Los nombres de cuenta de Azure Cosmos DB también deben ser únicos en Azure.

Los comandos de Azure PowerShell se pueden ejecutar en [Azure Cloud Shell](https://shell.azure.com) o en una estación de trabajo con [Azure PowerShell instalado](/powershell/azure/install-az-ps).

Normalmente, el proceso de creación de la cuenta de Cosmos DB tarda varios minutos en completarse.

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## <a name="2---create-a-table"></a>2 - Creación de una tabla

A continuación, debe crear una tabla dentro de la cuenta de Cosmos DB para que la aplicación la use.  A diferencia de una base de datos tradicional, solo es necesario especificar el nombre de la tabla, no las propiedades (columnas) de la tabla.  A medida que se cargan datos en la tabla, las propiedades (columnas) se crean automáticamente según sea necesario.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

En [Azure Portal](https://portal.azure.com/), complete los pasos siguientes para crear una tabla dentro de la cuenta de Cosmos DB.

| Instrucciones    | Instantánea |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](<./includes/create-table-dotnet/create-cosmos-table-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="Captura de pantalla que muestra cómo usar el cuadro de búsqueda de la barra de herramientas superior para buscar la cuenta de Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](<./includes/create-table-dotnet/create-cosmos-table-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="Captura de pantalla que muestra la ubicación del botón Agregar tabla." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](<./includes/create-table-dotnet/create-cosmos-table-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="Captura de pantalla que muestra cómo usar el cuadro de diálogo Nueva tabla para una tabla de Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3.png":::           |

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Las tablas de Cosmos DB se crean con el comando [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create).

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Las tablas de Cosmos DB se crean con el cmdlet [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable).

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## <a name="3---get-cosmos-db-connection-string"></a>3 - Obtención de la cadena de conexión de Cosmos DB

Para acceder a las tablas de Cosmos DB, la aplicación necesitará la cadena de conexión de la tabla para la cuenta de almacenamiento de CosmosDB.  La cadena de conexión se puede recuperar mediante Azure Portal, la CLI de Azure o Azure PowerShell.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

| Instrucciones    | Instantánea |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](<./includes/create-table-dotnet/get-cosmos-connection-string-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="Captura de pantalla que muestra la ubicación del vínculo de cadenas de conexión en la página de Cosmos DB." lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](<./includes/create-table-dotnet/get-cosmos-connection-string-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="Captura de pantalla que muestra la cadena de conexión que se va a seleccionar y usar en la aplicación." lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para obtener la cadena de conexión de almacenamiento de la tabla principal mediante la CLI de Azure, use el comando [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) con la opción `--type connection-strings`.  Este comando usa una [consulta JMESPath](https://jmespath.org/) para mostrar solo la cadena de conexión de la tabla principal.

```azurecli
# This gets the primary Table connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para obtener la cadena de conexión de almacenamiento de la tabla principal con Azure PowerShell, use el cmdlet [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey).

```azurepowershell
# This gets the primary Table connection string  
 $(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

---

La cadena de conexión de la cuenta de Cosmos DB se considera un secreto de aplicación y se debe proteger como cualquier otro secreto de aplicación o contraseña.  En este ejemplo, se usa la [herramienta Administrador de secretos](/aspnet/core/security/app-secrets#secret-manager) para almacenar la cadena de conexión durante el desarrollo y hacer que esté disponible para la aplicación.  Se puede acceder a la herramienta Administrador de secretos desde Visual Studio o desde la CLI de .NET.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para abrir la herramienta Administrador de secretos desde Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Administrar secretos de usuario** en el menú contextual.  Se abrirá el archivo *secrets.json* del proyecto.  Reemplace el contenido del archivo por el código JSON siguiente, sustituyendo la cadena de conexión de la tabla de Cosmos DB.

```json
{
  "ConnectionStrings": {
    "CosmosTableApi": "<cosmos db table connection string>"
  }  
}
```

### <a name="net-cli"></a>[CLI de .NET](#tab/netcore-cli)

Para usar el Administrador de secretos, primero debe inicializarlo para el proyecto mediante el comando `dotnet user-secrets init`.

```dotnetcli
dotnet user-secrets init
```

A continuación, use el comando `dotnet user-secrets set` para agregar la cadena de conexión de la tabla de Cosmos DB como un secreto.

```dotnetcli
dotnet user-secrets set "ConnectionStrings:CosmosTableApi" "<cosmos db table connection string>"
```

---

## <a name="4---install-azuredatatables-nuget-package"></a>4 - Instalación del paquete NuGet Azure.Data.Tables

Para acceder a Table API de Cosmos DB desde una aplicación de .NET, instale el paquete NuGet [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```PowerShell
Install-Package Azure.Data.Tables
```

### <a name="net-cli"></a>[CLI de .NET](#tab/netcore-cli)

```dotnetcli
dotnet add package Azure.Data.Tables
```

---

## <a name="5---configure-the-table-client-in-startupcs"></a>5 - Configuración del cliente de Table en el archivo Startup.cs

Azure SDK se comunica con Azure mediante objetos de cliente para ejecutar diferentes operaciones en Azure.  El objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) es el objeto que se usa para comunicarse con Table API de Cosmos DB.

Normalmente, una aplicación creará un único objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) por cada tabla que se usará en toda la aplicación.  Para ello, se recomienda usar la inserción de dependencias (DI) y registrar el objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) como singleton.  Para más información sobre el uso de la inserción de dependencias con Azure SDK, consulte [Inserción de dependencias con Azure SDK para .NET](/dotnet/azure/sdk/dependency-injection).

En el archivo `Startup.cs` de la aplicación, edite el método ConfigureServices() para que coincida con el siguiente fragmento de código:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddMvcOptions(options =>
        {
            options.Filters.Add(new ValidationFilter());
        });
    
    var connectionString = Configuration.GetConnectionString("CosmosTableApi");
    services.AddSingleton<TableClient>(new TableClient(connectionString, "WeatherData"));
    
    services.AddSingleton<TablesService>();
}
```

También tendrá que agregar la siguiente instrucción using en la parte superior del archivo Startup.cs.

```csharp
using Azure.Data.Tables;
```

## <a name="6---implement-cosmos-db-table-operations"></a>6 - Implementación de las operaciones de tablas de Cosmos DB

Todas las operaciones de tablas de Cosmos DB de la aplicación de ejemplo se implementan en la clase `TableService` ubicada en el directorio *Services*.  Tendrá que importar los espacios de nombres `Azure` y `Azure.Data.Tables` en la parte superior de este archivo para trabajar con los objetos del paquete `Azure.Data.Tables` del SDK.

```csharp
using Azure;
using Azure.Data.Tables;
```

Al principio de la clase `TableService`, agregue una variable de miembro para el objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) y un constructor para permitir que el objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) se inyecte en la clase.

```csharp
private TableClient _tableClient;

public TablesService(TableClient tableClient)
{
    _tableClient = tableClient;
}
```

### <a name="get-rows-from-a-table"></a>Obtención de filas de una tabla

La clase [TableClient](/dotnet/api/azure.data.tables.tableclient) contiene un método llamado [Query](/dotnet/api/azure.data.tables.tableclient.query) que permite seleccionar filas de la tabla.  En este ejemplo, dado que no se pasan parámetros al método, se seleccionarán todas las filas de la tabla.

El método también toma un parámetro genérico de tipo [ITableEntity](/dotnet/api/azure.data.tables.itableentity) que especifica cómo se devolverán los datos de la clase del modelo. En este caso, se usa la clase integrada [TableEntity](/dotnet/api/azure.data.tables.itableentity), lo que significa que el método `Query` devolverá una colección `Pageable\<TableEntity\>` como resultado.

```csharp
public IEnumerable<WeatherDataModel> GetAllRows()
{
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>();

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

La clase [TableEntity](/dotnet/api/azure.data.tables.itableentity) definida en el paquete `Azure.Data.Tables` tiene propiedades para los valores de clave de partición y clave de fila de la tabla.  Juntos, estos dos valores forman una clave única para la fila de la tabla.  En esta aplicación de ejemplo, el nombre de la estación meteorológica (ciudad) se almacena en la clave de partición y la fecha y hora de la observación se almacenan en la clave de fila.  Todas las demás propiedades (temperatura, humedad, velocidad del viento) se almacenan en un diccionario en el objeto `TableEntity`.

Es habitual asignar un objeto [TableEntity](/dotnet/api/azure.data.tables.tableentity) a un objeto de su propia definición.  La aplicación de ejemplo define la clase `WeatherDataModel` en el directorio *Models* para este propósito.  Esta clase tiene propiedades para el nombre de la estación y la fecha de observación a las que se asignarán la clave de partición y la clave de fila, lo que proporciona nombres de propiedad más significativos para estos valores.  A continuación, usa un diccionario para almacenar todas las demás propiedades en el objeto.  Se trata de un patrón común al trabajar con el almacenamiento de tablas, ya que una fila puede tener un número arbitrario de propiedades y queremos que nuestros objetos del modelo puedan capturar todas ellas.  Esta clase también contiene métodos para enumerar las propiedades de la clase.

```csharp
public class WeatherDataModel 
{
    // Captures all of the weather data properties -- temp, humidity, wind speed, etc
    private Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public string Etag { get; set; }

    public object this[string name] 
    { 
        get => ( ContainsProperty(name)) ? _properties[name] : null; 
        set => _properties[name] = value; 
    }
    
    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);       
}
```

El método `MapTableEntityToWeatherDataModel` se usa para asignar un objeto [TableEntity](/dotnet/api/azure.data.tables.itableentity) a un objeto `WeatherDataModel`.  El objeto [TableEntity](/dotnet/api/azure.data.tables.itableentity) contiene la propiedad [Keys](/dotnet/api/azure.data.tables.tableentity.keys) para obtener todos los nombres de propiedad incluidos en la tabla para el objeto (de hecho, los nombres de columna de esta fila de la tabla).  El método `MapTableEntityToWeatherDataModel` asigna directamente las propiedades `PartitionKey`, `RowKey`, `Timestamp` y `Etag` y, a continuación, usa la propiedad `Keys` para recorrer en iteración las demás propiedades del objeto `TableEntity` y asignar esas propiedades al objeto `WeatherDataModel`, menos las propiedades que ya se han asignado directamente.

Edite el código del método `MapTableEntityToWeatherDataModel` para que coincida con el siguiente bloque de código.

```csharp
public WeatherDataModel MapTableEntityToWeatherDataModel(TableEntity entity)
{
    WeatherDataModel observation = new WeatherDataModel();
    observation.StationName = entity.PartitionKey;
    observation.ObservationDate = entity.RowKey;
    observation.Timestamp = entity.Timestamp;
    observation.Etag = entity.ETag.ToString();

    var measurements = entity.Keys.Where(key => !EXCLUDE_TABLE_ENTITY_KEYS.Contains(key));
    foreach (var key in measurements)
    {
        observation[key] = entity[key];
    }
    return observation;            
}
```

### <a name="filter-rows-returned-from-a-table"></a>Filtrado de las filas devueltas de una tabla

Para filtrar las filas devueltas de una tabla, puede pasar una cadena de filtro de estilo OData al método [Query](/dotnet/api/azure.data.tables.tableclient.query). Por ejemplo, si quisiera obtener todas las lecturas meteorológicas de Chicago entre la medianoche del 1 de julio de 2021 y la medianoche del 2 de julio de 2021 (ambos incluidos), pasaría la siguiente cadena de filtro.

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

Puede ver todos los operadores de filtrado de OData en el sitio web de OData, en la sección [Opción de consulta del sistema de filtrado](https://www.odata.org/documentation/odata-version-2-0/uri-conventions/).

En la aplicación de ejemplo, el objeto `FilterResultsInputModel` está diseñado para capturar los criterios de filtrado proporcionados por el usuario.

```csharp
public class FilterResultsInputModel : IValidatableObject
{
    public string PartitionKey { get; set; }
    public string RowKeyDateStart { get; set; }
    public string RowKeyTimeStart { get; set; }
    public string RowKeyDateEnd { get; set; }
    public string RowKeyTimeEnd { get; set; }
    [Range(-100, +200)]
    public double? MinTemperature { get; set; }
    [Range(-100,200)]
    public double? MaxTemperature { get; set; }
    [Range(0, 300)]
    public double? MinPrecipitation { get; set; }
    [Range(0,300)]
    public double? MaxPrecipitation { get; set; }
}
```

Cuando se pasa este objeto al método `GetFilteredRows` de la clase `TableService`, crea una cadena de filtrado para cada valor de propiedad que no sea NULL.  A continuación, crea una cadena de filtrado combinada combinando todos los valores con una cláusula "and".  Esta cadena de filtrado combinada se pasa al método [Query](/dotnet/api/azure.data.tables.tableclient.query) en el objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) y solo se devolverán las filas que coincidan con la cadena de filtrado.  Puede usar un método similar en el código para construir cadenas de filtrado adecuadas según sea necesario para la aplicación.

```csharp
public IEnumerable<WeatherDataModel> GetFilteredRows(FilterResultsInputModel inputModel)
{
    List<string> filters = new List<string>();

    if (!String.IsNullOrEmpty(inputModel.PartitionKey))
        filters.Add($"PartitionKey eq '{inputModel.PartitionKey}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateStart) && !String.IsNullOrEmpty(inputModel.RowKeyTimeStart))
        filters.Add($"RowKey ge '{inputModel.RowKeyDateStart} {inputModel.RowKeyTimeStart}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateEnd) && !String.IsNullOrEmpty(inputModel.RowKeyTimeEnd))
        filters.Add($"RowKey le '{inputModel.RowKeyDateEnd} {inputModel.RowKeyTimeEnd}'");
    if (inputModel.MinTemperature.HasValue)
        filters.Add($"Temperature ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxTemperature.HasValue)
        filters.Add($"Temperature le {inputModel.MaxTemperature.Value}");
    if (inputModel.MinPrecipitation.HasValue)
        filters.Add($"Precipitation ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxPrecipitation.HasValue)
        filters.Add($"Precipitation le {inputModel.MaxTemperature.Value}");

    string filter = String.Join(" and ", filters);
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>(filter);

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

### <a name="insert-data-using-a-tableentity-object"></a>Inserción de datos mediante un objeto TableEntity

La manera más sencilla de agregar datos a una tabla es mediante el uso de un objeto [TableEntity](/dotnet/api/azure.data.tables.itableentity).  En este ejemplo, los datos se asignan desde un objeto del modelo de entrada a un objeto [TableEntity](/dotnet/api/azure.data.tables.itableentity).  Las propiedades del objeto de entrada que representan el nombre de la estación meteorológica y la fecha y hora de observación se asignan a las propiedades [PartitionKey](/dotnet/api/azure.data.tables.tableentity.partitionkey) y [RowKey](/dotnet/api/azure.data.tables.tableentity.rowkey) respectivamente, que juntas forman una clave única para la fila de la tabla.  A continuación, las propiedades adicionales del objeto del modelo de entrada se asignan a las propiedades del diccionario en el objeto TableEntity.  Por último, se usa el método [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) del objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) para insertar los datos en la tabla.

Modifique la clase `InsertTableEntity` de la aplicación de ejemplo para que contenga el código siguiente.

```csharp
public void InsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.AddEntity(entity);
}
```

### <a name="upsert-data-using-a-tableentity-object"></a>Actualizar/insertar (upsert) datos mediante un objeto TableEntity

Si intenta insertar una fila en una tabla con una combinación de clave de partición y clave de fila que ya existe en esa tabla, recibirá un error.  Por este motivo, a menudo es preferible usar [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) en lugar del método AddEntity al agregar filas a una tabla.  Si la combinación de clave de partición y clave de fila especificada ya existe en la tabla, el método [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) actualizará la fila existente.  De lo contrario, se agregará la fila a la tabla.

```csharp
public void UpsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.UpsertEntity(entity);
}
```

### <a name="insert-or-upsert-data-with-variable-properties"></a>Insertar o actualizar/insertar (upsert) datos con propiedades variables

Una de las ventajas de usar Table API de Cosmos DB es que, si un objeto que se carga en una tabla contiene nuevas propiedades, esas propiedades se agregan automáticamente a la tabla y los valores almacenados en Cosmos DB.  No es necesario ejecutar instrucciones DDL como ALTER TABLE para agregar columnas como en una base de datos tradicional.

Este modelo proporciona flexibilidad a la aplicación cuando se trabaja con orígenes de datos que pueden agregar o modificar qué datos se deben capturar a lo largo del tiempo o cuando distintas entradas proporcionan datos diferentes a la aplicación. En la aplicación de ejemplo, podemos simular una estación meteorológica que envía no solo los datos meteorológicos básicos, sino también algunos valores adicionales. Cuando un objeto con estas nuevas propiedades se almacena en la tabla por primera vez, las propiedades correspondientes (columnas) se agregarán automáticamente a la tabla.

En la aplicación de ejemplo, la clase `ExpandableWeatherObject` se basa en un diccionario interno para admitir cualquier conjunto de propiedades en el objeto.  Esta clase representa un patrón típico para los casos en los que un objeto debe contener un conjunto arbitrario de propiedades.

```csharp
public class ExpandableWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

Para insertar o actualizar/insertar (upsert) este tipo de objeto mediante Table API, asigne las propiedades del objeto ampliable a un objeto [TableEntity](/dotnet/api/azure.data.tables.tableentity) y use los métodos [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) o [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) en el objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) según corresponda.

```csharp
public void InsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.AddEntity(entity);
}

        
public void UpsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.UpsertEntity(entity);
}

```
  
### <a name="update-an-entity"></a>Actualización de una entidad

Las entidades se pueden actualizar llamando al método [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) en el objeto [TableClient](/dotnet/api/azure.data.tables.tableclient).  Dado que una entidad (fila) almacenada mediante Table API podría contener cualquier conjunto arbitrario de propiedades, a menudo resulta útil crear un objeto de actualización basado en un objeto de diccionario similar al objeto `ExpandableWeatherObject` mencionado anteriormente.  En este caso, la única diferencia es la adición de la propiedad `Etag`, que se usa para el control de la simultaneidad durante las actualizaciones.

```csharp
public class UpdateWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }
    public string ObservationDate { get; set; }
    public string Etag { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

En la aplicación de ejemplo, este objeto se pasa al método `UpdateEntity` de la clase `TableService`.  Este método carga primero la entidad existente de Table API mediante el método [GetEntity](/dotnet/api/azure.data.tables.tableclient.getentity) de [TableClient](/dotnet/api/azure.data.tables.tableclient).  A continuación, actualiza ese objeto de entidad y usa el método `UpdateEntity` para guardar las actualizaciones en la base de datos.  Observe cómo el método [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) toma la etiqueta ETag actual del objeto para garantizar que el objeto no haya cambiado desde que se cargó inicialmente.  Si desea actualizar la entidad independientemente, puede pasar un valor de `Etag.Any` al método `UpdateEntity`.

```csharp
public void UpdateEntity(UpdateWeatherObject weatherObject)
{
    string partitionKey = weatherObject.StationName;
    string rowKey = weatherObject.ObservationDate;

    // Use the partition key and row key to get the entity
    TableEntity entity = _tableClient.GetEntity<TableEntity>(partitionKey, rowKey).Value;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }

    _tableClient.UpdateEntity(entity, new ETag(weatherObject.Etag));
}
```

### <a name="remove-an-entity"></a>Eliminación de una entidad

Para quitar una entidad de una tabla, llame al método [DeleteEntity](/dotnet/api/azure.data.tables.tableclient.deleteentity) del objeto [TableClient](/dotnet/api/azure.data.tables.tableclient) con la clave de partición y la clave de fila del objeto.

```csharp
public void RemoveEntity(string partitionKey, string rowKey)
{
    _tableClient.DeleteEntity(partitionKey, rowKey);           
}
```

## <a name="7---run-the-code"></a>7 - Ejecución del código

Ejecute la aplicación de ejemplo para interactuar con Table API de Cosmos DB.  La primera vez que ejecute la aplicación, no habrá datos porque la tabla está vacía.  Use cualquiera de los botones de la parte superior de la aplicación para agregar datos a la tabla.

:::image type="content" source="./media/create-table-dotnet/table-api-app-data-insert-buttons-480px.png" alt-text="Captura de pantalla de la aplicación que muestra la ubicación de los botones usados para insertar datos en Cosmos DB mediante Table A P I." lightbox="./media/create-table-dotnet/table-api-app-data-insert-buttons.png":::

Al seleccionar el botón **Insert using Table Entity** (Insertar mediante TableEntity), se abre un cuadro de diálogo que le permite insertar o actualizar/insertar (upsert) una fila nueva mediante un objeto `TableEntity`.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-table-entity-480px.png" alt-text="Captura de pantalla de la aplicación que muestra el cuadro de diálogo utilizado para insertar datos mediante un objeto TableEntity." lightbox="./media/create-table-dotnet/table-api-app-insert-table-entity.png":::

Al seleccionar el botón **Insert using Expandable Data** (Insertar mediante datos ampliables), se abre un cuadro de diálogo que le permite insertar un objeto con propiedades personalizadas, lo que muestra cómo Table API de Cosmos DB agrega automáticamente propiedades (columnas) a la tabla cuando es necesario.  Use el botón *Add Custom Field* (Agregar campo personalizado) para agregar una o varias propiedades nuevas y demostrar esta funcionalidad.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-expandable-entity-480px.png" alt-text="Captura de pantalla de la aplicación que muestra el cuadro de diálogo usado para insertar datos mediante un objeto con campos personalizados." lightbox="./media/create-table-dotnet/table-api-app-insert-expandable-entity.png":::

Use el botón **Insert Sample Data** (Insertar datos de ejemplo) para cargar algunos datos de ejemplo en la tabla de Cosmos DB.

:::image type="content" source="./media/create-table-dotnet/table-api-app-sample-data-insert-480px.png" alt-text="Captura de pantalla de la aplicación que muestra la ubicación del botón para insertar datos de ejemplo." lightbox="./media/create-table-dotnet/table-api-app-sample-data-insert.png":::

Seleccione el elemento **Filter Results** (Filtrar resultados) en el menú superior para ir a la página Filter Results (Filtrar resultados).  En esta página, rellene los criterios de filtrado para mostrar cómo se puede crear y pasar una cláusula de filtro a Table API de Cosmos DB.

:::image type="content" source="./media/create-table-dotnet/table-api-app-filter-data-480px.png" alt-text="Captura de pantalla de la aplicación que muestra la página Filtrar resultados y resalta el elemento de menú que se usa para ir a la página." lightbox="./media/create-table-dotnet/table-api-app-filter-data.png":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con la aplicación de ejemplo, debe quitar todos los recursos de Azure relacionados con este artículo de la cuenta de Azure.  Para ello, elimine el grupo de recursos.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Se puede eliminar un grupo de recursos mediante [Azure Portal](https://portal.azure.com/) haciendo lo siguiente.

| Instrucciones    | Instantánea |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](<./includes/create-table-dotnet/remove-resource-group-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-1-240px.png" alt-text="Captura de pantalla que muestra cómo buscar un grupo de recursos." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-1.png"::: |
| [!INCLUDE [Delete resource group step 2](<./includes/create-table-dotnet/remove-resource-group-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-2-240px.png" alt-text="Captura de pantalla que muestra la ubicación del botón Eliminar grupo de recursos." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-2.png"::: |
| [!INCLUDE [Delete resource group step 3](<./includes/create-table-dotnet/remove-resource-group-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-3-240px.png" alt-text="Captura de pantalla que muestra el cuadro de diálogo de confirmación para eliminar un grupo de recursos." lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-3.png"::: |

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para eliminar un grupo de recursos mediante la CLI de Azure, use el comando [az group delete](/cli/azure/group#az_group_delete) con el nombre del grupo de recursos que se va a eliminar.  La eliminación de un grupo de recursos eliminará también todos los recursos de Azure contenidos en él.

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para eliminar un grupo de recursos mediante Azure PowerShell, use el comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) con el nombre del grupo de recursos que se va a eliminar.  La eliminación de un grupo de recursos eliminará también todos los recursos de Azure contenidos en él.

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha obtenido información sobre cómo crear una cuenta de Azure Cosmos DB, crear una tabla mediante el Explorador de datos y ejecutar una aplicación.  Ahora ya puede consultar los datos mediante Table API.  

> [!div class="nextstepaction"]
> [Importación de datos de tabla a Table API](table-import.md)
