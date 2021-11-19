---
title: Uso de la biblioteca cliente de Azure Tables para Java
description: La biblioteca cliente de Azure Tables para Java permite almacenar datos estructurados en la nube.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: 7b390f4be49c8785c7ee8a2dca588ee3cfb7d571
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719587"
---
# <a name="how-to-use-the-azure-tables-client-library-for-java"></a>Uso de la biblioteca cliente de Azure Tables para Java

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

En este artículo se muestra cómo crear tablas, almacenar datos y realizar operaciones CRUD en ellos. Los ejemplos están escritos en Java y usan la [biblioteca cliente de Azure Tables para Java][Azure Tables client library for Java]. Entre los escenarios descritos se incluyen **crear**, **enumerar** y **eliminar** tablas, así como **insertar**, **consultar**, **modificar** y **eliminar** entidades de una tabla. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#next-steps) .

> [!IMPORTANT]
> La última versión de la biblioteca cliente de Azure Tables que admite Table Storage y Cosmos DB Table es la [12+][Azure Tables client library for Java].

## <a name="create-an-azure-service-account"></a>Creación de una cuenta de servicio de Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../includes/cosmos-db-create-azure-service-account.md)]

**Creación de una cuenta de Azure Storage**

[!INCLUDE [cosmos-db-create-storage-account](../includes/cosmos-db-create-storage-account.md)]

**Creación de una cuenta de Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Creación de una aplicación Java

Para usar los ejemplos de este artículo:
1. Instale el [kit de desarrollo de Java (JDK)](/azure/developer/java/fundamentals/java-support-on-azure#supported-java-versions-and-update-schedule).
2. Cree una cuenta de Azure Storage o de Azure Cosmos DB en su suscripción de Azure.
3. Compruebe que su sistema de desarrollo cumple los requisitos mínimos y las dependencias que se indican en el repositorio de la [biblioteca cliente de Azure Tables para Java][Azure Tables client library for Java] en GitHub.
4. Siga las instrucciones para descargar e instalar las bibliotecas de Azure Storage para Java en el sistema desde ese repositorio.
5. Cree una aplicación de Java que use los ejemplos de este artículo.

## <a name="configure-your-app-to-access-table-storage"></a>Configuración de una aplicación para que acceda a Table Storage

En el archivo *pom.xml*, agregue la siguiente entrada a la sección `dependencies`:

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-data-tables</artifactId>
  <version>12.1.1</version>
</dependency>
```

Luego, agregue las siguientes instrucciones `import` al principio del archivo Java en el que desea utilizar las API de Azure Tables para acceder a las tablas:

```java
// Include the following imports to use table APIs
import com.azure.data.tables.TableClient;
import com.azure.data.tables.TableClientBuilder;
import com.azure.data.tables.TableServiceClient;
import com.azure.data.tables.TableServiceClientBuilder;
import com.azure.data.tables.models.ListEntitiesOptions;
import com.azure.data.tables.models.TableEntity;
import com.azure.data.tables.models.TableEntityUpdateMode;
import com.azure.data.tables.models.TableTransactionAction;
import com.azure.data.tables.models.TableTransactionActionType;
```

## <a name="add-your-connection-string"></a>Incorporación de la cadena de conexión

Puede conectarse a la cuenta de almacenamiento de Azure o a la de Table API de Azure Cosmos DB. Obtenga la cadena de conexión en función del tipo de cuenta que esté usando.

### <a name="add-an-azure-storage-connection-string"></a>Adición de una cadena de conexión de Azure Storage

Un cliente de Azure Tables puede utilizar una cadena de conexión de almacenamiento para almacenar puntos de conexión y credenciales con el fin de acceder a los servicios de administración de datos. Si se ejecuta en una aplicación cliente, debe proporcionar la cadena de conexión de Storage en el siguiente formato, y usar el nombre de su cuenta de Storage y la clave de acceso principal de la cuenta de Storage que se muestra en [Azure Portal](https://portal.azure.com) para los valores **AccountName** y **AccountKey**.

En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

```java
// Define the connection-string with your values.
public final String connectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=core.windows.net;
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Adición de una cadena de conexión de Table API de Azure Cosmos DB

Una cuenta de Azure Cosmos DB utiliza una cadena de conexión para almacenar el punto de conexión de la tabla y sus credenciales. Si se ejecuta en una aplicación cliente, debe proporcionar la cadena de conexión de Azure Cosmos DB en el siguiente formato y usar el nombre de su cuenta de Azure Cosmos DB y la clave de acceso principal de la cuenta que se muestra en [Azure Portal](https://portal.azure.com) para los valores **AccountName** y **AccountKey**.

En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión de Azure Cosmos DB:

```java
public final String connectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

En una aplicación que se ejecute dentro de un rol de Azure, esta cadena se puede almacenar en el archivo de configuración del servicio, *ServiceConfiguration.cscfg*. Puede acceder a él con una llamada al método `System.getenv`. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **Configuración** denominado *ConnectionString* en el archivo de configuración del servicio:

```java
// Retrieve storage account from connection-string.
String connectionString = System.getenv("ConnectionString");
```

También puede almacenar la cadena de conexión en el archivo config.properties del proyecto:

```java
connectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

En los ejemplos siguientes se supone que usó uno de estos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="create-a-table"></a>Creación de una tabla

Un objeto `TableServiceClient` permite interactuar con el servicio Tables para crear, enumerar y eliminar tablas. El siguiente código crea un objeto `TableServiceClient` y lo usa para crear un objeto `TableClient`, que representa una tabla llamada `Employees`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Create the table if it not exists.
    TableClient tableClient = tableServiceClient.createTableIfNotExists(tableName);

}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Enumeración de las tablas

Para obtener una lista de las tablas, llame al método `TableServiceClient.listTables` para recuperar una lista que se puede iterar de nombres de tablas.

```java
try
{
    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Loop through a collection of table names.
    tableServiceClient.listTables().forEach(tableItem -> 
        System.out.printf(tableItem.getName())
    );
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

El código siguiente crea una instancia `TableEntity` con algunos datos del cliente que se van a almacenar. El código llama al método `upsertEntity` en el objeto `TableClient`. El método inserta la nueva entidad del cliente en la tabla `Employees` o reemplaza la entidad si ya existe.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
     TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new employee TableEntity.
    String partitionKey = "Sales";
    String rowKey = "0001";
    Map<String, Object> personalInfo= new HashMap<>();
    personalInfo.put("FirstName", "Walter");
    personalInfo.put("LastName", "Harp");
    personalInfo.put("Email", "Walter@contoso.com");
    personalInfo.put("PhoneNumber", "425-555-0101");
    TableEntity employee = new TableEntity(partitionKey, rowKey).setProperties(personalInfo);
        
    // Upsert the entity into the table
    tableClient.upsertEntity(employee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Inserción de un lote de entidades

Puede insertar un lote de entidades en el servicio Tabla mediante una operación de escritura. El siguiente código crea un objeto `List<TableTransactionAction>` y le agrega tres operaciones upsert. Cada operación se agrega creando objeto `TableEntity`, estableciendo sus propiedades y, después, llamando al método `submitTransaction` en el objeto `TableClient`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    String partitionKey = "Sales";
    List<TableTransactionAction> tableTransactionActions = new ArrayList<>();
    
    Map<String, Object> personalInfo1 = new HashMap<>();
    personalInfo1.put("FirstName", "Jeff");
    personalInfo1.put("LastName", "Smith");
    personalInfo1.put("Email", "Jeff@contoso.com");
    personalInfo1.put("PhoneNumber", "425-555-0104");
    
    // Create an entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0001")
            .setProperties(personalInfo1)
    ));
    
    Map<String, Object> personalInfo2 = new HashMap<>();
    personalInfo2.put("FirstName", "Ben");
    personalInfo2.put("LastName", "Johnson");
    personalInfo2.put("Email", "Ben@contoso.com");
    personalInfo2.put("PhoneNumber", "425-555-0102");
    
    // Create another entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0002")
            .setProperties(personalInfo2)
    ));
    
    Map<String, Object> personalInfo3 = new HashMap<>();
    personalInfo3.put("FirstName", "Denise");
    personalInfo3.put("LastName", "Rivers");
    personalInfo3.put("Email", "Denise@contoso.com");
    personalInfo3.put("PhoneNumber", "425-555-0103");
    
    // Create a third entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0003")
            .setProperties(personalInfo3)
    ));

    // Submit transaction on the "Employees" table.
    tableClient.submitTransaction(tableTransactionActions);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Algunos aspectos que cabe tener en cuenta acerca de las operaciones por lotes:

* Puede ejecutar cualquier combinación de 100 operaciones de inserción, eliminación, combinación, reemplazo, inserción o combinación e inserción o reemplazo en un único lote.
* Una operación por lotes puede tener una operación de recuperación, si es que es la única operación del lote.
* Todas las entidades de la misma operación por lotes deben compartir la misma clave de partición.
* Una operación por lotes se limita a una carga de datos de 4 MB.

## <a name="retrieve-all-entities-in-a-partition"></a>todas las entidades de una partición

Para consultar una tabla a fin de obtener las entidades de una partición, use un objeto `ListEntitiesOptions`. Llame a `ListEntitiesOptions.setFilter` para crear una consulta en una tabla concreta que devuelva un tipo de resultado específico. El código siguiente especifica un filtro para las entidades en las que `Sales` es la clave de partición. Cuando la consulta se ejecuta con una llamada a `listEntities` en el objeto `TableClient`, devuelve un `Iterator` de `TableEntity`. A continuación, puede usar el `Iterator` devuelto para cada bucle "ForEach" para consumir los resultados. En este código, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales'");

    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperación de un rango de entidades de una partición

Si no quiere consultar todas las entidades de una partición, especifique un rango mediante el uso de operadores de comparación en un filtro. El código siguiente combina dos filtros para obtener todas las entidades de la partición `Sales` con una clave de fila entre "0001" y "0004". A continuación, imprime los resultados de la consulta. Si usa las entidades agregadas a la tabla en la sección de inserción por lotes de esta guía, esta vez solo se devolverán dos entidades en este momento (Ben y Denise Smith).

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales' AND " + ROW_KEY + " lt '0004' AND ROW_KEY + " gt '0001'");
    
    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>una sola entidad

Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente llama a `TableClient.getEntity` con los parámetros de clave de partición y clave de fila para recuperar la entidad del empleado "Jeff Smith", en lugar de crear un elemento `ListEntitiesOptions` y usar filtros para realizar la misma operación. Cuando se ejecuta, la operación de recuperación devuelve solo una entidad, en lugar de una colección de entidades. Si no coincide exactamente la clave de fila y de partición de ninguna entidad, se devuelve el valor `null`. La forma más rápida de recuperar una sola entidad de Table service es especificar claves tanto de partición como de fila en las consultas.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getProperty("FirstName") +
            "\t" + specificEntity.getProperty("LastName"));
            "\t" + specificEntity.getProperty("Email") +
            "\t" + specificEntity.getProperty("PhoneNumber"));
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Modificación de una entidad

Para modificar una entidad, recupérela del servicio Tabla, realice los cambios en el objeto de entidad y vuelva a guardar los cambios en dicho servicio con una operación de reemplazo o combinación. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a `tableClient.upsertEntity` como hicimos para insertar, este código llama a `tableClient.updateEntity`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Specify a new phone number
    specificEntity.getProperties().put("PhoneNumber", "425-555-0105");

    // Update the specific entity
    tableClient.updateEntity(specificEntity, TableEntityUpdateMode.REPLACE);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Consulta de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del siguiente código usa el método `ListEntitiesOptions.setSelect` para devolver solo las direcciones de correo electrónico de las entidades de la tabla.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition that retrieves only the Email property.
    List<String> attributesToRetrieve = new ArrayList<>();
    attributesToRetrieve.add("Email");
    
    ListEntitiesOptions options = new ListEntitiesOptions().setSelect(attributesToRetrieve);

    // Loop through the results, displaying the Email values.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getProperty("Email"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Inserción o reemplazo de una entidad

En ocasiones, es posible que desee agregar una entidad a una tabla sin saber si ya existe en la tabla. Las operaciones de inserción o reemplazo permiten realizar una única solicitud. Esa solicitud insertará la entidad si no existe o reemplazará la existente, en caso de que exista. Según los ejemplos anteriores, el siguiente código inserta o reemplaza la entidad de "Walter Harp". Después de crear una entidad, este código llama al método `TableClient.upsertEntity`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new table entity.
    Map<String, Object> properties = new HashMap<>();
    properties.put("FirstName", "Walter");
    properties.put("LastName", "Harp");
    properties.put("Email", "Walter@contoso.com");
    properties.put("PhoneNumber", "425-555-0101");
        
    TableEntity newEmployee = new TableEntity("Sales", "0004")
        .setProperties(properties);
        
    // Add the new customer to the Employees table.
    tableClient.upsertEntity(newEmployee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Eliminación de una entidad

Para eliminar una entidad, especifique su clave de partición y su clave de fila a través de `TableClient.deleteEntity`.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    Delete the entity for partition key 'Sales' and row key '0001' from the table.
    tableClient.deleteEntity("Sales", "0001");
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Eliminar una tabla

Por último, el siguiente código elimina una tabla de una cuenta. Aproximadamente 40 segundos después de eliminar una tabla, no puede volver a crearla.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Delete the table and all its data.
    tableClient.deleteTable();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Getting Started with Azure Table service in Java](https://github.com/Azure-Samples/storage-table-java-getting-started) (Introducción a Azure Table service en Java)
* El [Explorador de Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
* [Biblioteca cliente de Azure Tables para Java][Azure Tables client library for Java]
* [Referencia de biblioteca cliente de Azure Tables][Azure Tables client library reference documentation]
* [API REST de Azure Tables][Azure Tables REST API]
* [Blog del equipo de Azure Tables][Azure Tables Team Blog]

Para más información, visite [Azure para desarrolladores de Java](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Tables client library for Java]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/tables/azure-data-tables
[Azure Tables client library reference documentation]: https://azure.github.io/azure-sdk-for-java/tables.html
[Azure Tables REST API]: ../../storage/tables/table-storage-overview.md
[Azure Tables Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/