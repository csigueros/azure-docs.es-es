---
title: Migre su aplicación de Amazon DynamoDB a Azure Cosmos DB
description: Aprenda a migrar la aplicación .NET de Amazon DynamoDB a Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: f7f5f5cc775cb9673cbb6f3dd0355887463744c3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114351"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Migre su aplicación de Amazon DynamoDB a Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB es una base de datos totalmente administrada, escalable y distribuida globalmente. Proporciona acceso de baja latencia garantizado a los datos. Para obtener más información sobre Azure Cosmos DB, vea el artículo de [información general](../introduction.md). En este artículo se describe cómo migrar la aplicación .NET de DynamoDB a Azure Cosmos DB con unos cambios mínimos en el código.

## <a name="conceptual-differences"></a>Diferencias conceptuales

A continuación se muestran las diferencias conceptuales clave entre Azure Cosmos DB y DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|No aplicable|  Base de datos |
|Tabla      |  Colección |
|  Elemento |  Documento |
|Atributo|Campo|
|Índice secundario|Índice secundario|
|Clave principal: clave de partición|Partition Key|
|Clave principal: criterio de ordenación| No es obligatorio |
|STREAM|ChangeFeed|
|Unidad de proceso de escritura|Unidad de solicitud (flexible, se puede usar para lecturas o escrituras)|
|Unidad de proceso de lectura    |Unidad de solicitud (flexible, se puede usar para lecturas o escrituras)|
|Tablas globales| No se requiere. Puede seleccionar la región directamente durante el aprovisionamiento de la cuenta de Azure Cosmos (puede cambiar la región más adelante).|

## <a name="structural-differences"></a>Diferencias estructurales

Azure Cosmos DB tiene una estructura JSON más sencilla cuando se compara con la de DynamoDB. En el ejemplo siguiente se muestran las diferencias:

**DynamoDB**:

El siguiente objeto JSON representa el formato de datos de DynamoDB.

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

El siguiente objeto JSON representa el formato de datos de Azure Cosmos DB.

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migración de los datos

Hay varias opciones disponibles para migrar los datos a Azure Cosmos DB. Para más información, consulte el artículo [Opciones para migrar los datos locales o en la nube a Azure Cosmos DB](../cosmosdb-migrationchoices.md).

## <a name="migrate-your-code"></a>Migración del código

El ámbito de este artículo es migrar el código de una aplicación a Azure Cosmos DB, que es el aspecto fundamental de la migración de la base de datos. Para ayudarle a reducir la curva de aprendizaje, en las secciones siguientes se incluye una comparación de código en paralelo entre Amazon DynamoDB y el fragmento de código equivalente de Azure Cosmos DB.

Para descargar el código fuente, clone el siguiente repositorio:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Requisitos previos

- .NET Framework 4.7.2
- Visual Studio 2019
- Acceso a la cuenta de la API de SQL de Azure Cosmos DB.
- Instalación local de Amazon DynamoDB
- Java 8
- Ejecución de la versión descargable de Amazon DynamoDB en el puerto 8000 (puede cambiar y configurar el código)

### <a name="set-up-your-code"></a>Configuración del código

Instalación del siguiente "paquete NuGet" en el proyecto:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Establecimiento de la conexión

**DynamoDB**:

En Amazon DynamoDB, se usa el código siguiente para conectarse:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Para conectarse a Azure Cosmos DB, actualice el código para:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Optimización de la conexión en Azure Cosmos DB**

Con Azure Cosmos DB, puede usar las siguientes opciones para optimizar la conexión:

* **ConnectionMode**: use el modo de conexión directa para conectarse a los nodos de datos del servicio Azure Cosmos DB. Use el modo de puerta de enlace solo para inicializar y almacenar en caché las direcciones lógicas y actualizar en las actualizaciones. Para más información, consulte el artículo sobre los [modos de conectividad](sql-sdk-connection-modes.md).

* **ApplicationRegion**: esta opción se usa para establecer la región de replicación geográfica preferida que se usa para interactuar con Azure Cosmos DB. Para más información, consulte el artículo sobre [distribución global](../distribute-data-globally.md).

* **ConsistencyLevel**: esta opción se usa para invalidar el nivel de coherencia predeterminado. Para más información, consulte el artículo [Niveles de coherencia](../consistency-levels.md).

* **BulkExecutionMode**: esta opción se usa para ejecutar operaciones masivas estableciendo la propiedad *AllowBulkExecution* en true. Para más información, consulte el artículo sobre [importación en bloque](tutorial-sql-api-dotnet-bulk-import.md).

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Aprovisionamiento del contenedor

**DynamoDB**:

Para almacenar los datos en Amazon DynamoDB, primero debe crear la tabla. En este proceso, se define el esquema, el tipo de clave y los atributos, tal y como se muestra en el código siguiente:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

En Amazon DynamoDB, debe aprovisionar las unidades de proceso de lectura y de escritura. Mientras que en Azure Cosmos DB se especifica el rendimiento como [unidades de solicitud (RU/s)](../request-units.md), que se puede usar para cualquier operación de manera dinámica. Los datos se organizan como base de datos -> contenedor -> elemento. Puede especificar el rendimiento en el nivel de base de datos o en el nivel de colección, o en ambos.

Para crear una base de datos:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Para crear el contenedor:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Carga de los datos

**DynamoDB**:

En el código siguiente se muestra cómo cargar los datos en Amazon DynamoDB. MoviesArray consta de una lista de documentos JSON que debe recorrer en iteración y cargar el documento JSON en Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

En Azure Cosmos DB, puede optar por transmitir y escribir con `moviesContainer.CreateItemStreamAsync()`. Sin embargo, en este ejemplo, el archivo JSON se deserializará en el tipo *MovieModel* para mostrar la característica de conversión de tipos. El código es multiproceso, que usará la arquitectura distribuida de Azure Cosmos DB y acelerará la carga:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Creación de un documento

**DynamoDB**:

Escribir un nuevo documento en Amazon DynamoDB no tiene seguridad de tipos, en el ejemplo siguiente se usa newItem como tipo de documento:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB proporciona seguridad de tipos a través del modelo de datos. Usamos el modelo de datos denominado "MovieModel":

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

En Azure Cosmos DB, newItem será MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Lectura de un documento

**DynamoDB**:

Para leer en Amazon DynamoDB, debe definir primitivas:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

Sin embargo, con Azure Cosmos DB la consulta es natural (linq):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

La colección de documentos del ejemplo anterior:

- tendrá seguridad de tipos
- proporcionará una opción de consulta natural

### <a name="update-an-item"></a>Actualización de un elemento

**DynamoDB**: Para actualizar el elemento en Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

En Azure Cosmos DB, la actualización se tratará como la operación Upsert, lo que significa insertar el documento si no existe:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Eliminar un documento

**DynamoDB**:

Para eliminar un elemento en Amazon DynamoDB, debe volver a incluir primitivos:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

En Azure Cosmos DB, podemos obtener el documento y eliminarlo de forma asincrónica:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Consulta de documentos

**DynamoDB**:

En Amazon DynamoDB, se necesitan funciones de API para consultar los datos:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

En Azure Cosmos DB, puede realizar proyecciones y filtrar en una consulta SQL simple:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

En el caso de las operaciones de intervalo, por ejemplo, "between", debe realizar un examen en Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

En Azure Cosmos DB, puede usar la consulta SQL y una instrucción de una sola línea:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Eliminación de un contenedor

**DynamoDB**:

Para eliminar la tabla en Amazon DynamoDB, puede especificar:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Para eliminar la colección en Azure Cosmos DB, puede especificar:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Después, elimine la base de datos si necesita:

```csharp
await cosmosDatabase.DeleteAsync();
```

Como puede ver, Azure Cosmos DB admite las consultas naturales (SQL), las operaciones son asincrónicas y mucho más fáciles. Puede migrar fácilmente el código complejo a Azure Cosmos DB, lo que resulta más sencillo después de la migración.

### <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [optimización del rendimiento](performance-tips.md).
- Más información sobre la [optimización de lecturas y escrituras](../key-value-store-cost.md)
- Más información sobre la [supervisión en Cosmos DB](../monitor-cosmos-db.md)

