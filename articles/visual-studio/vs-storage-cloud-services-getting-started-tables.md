---
title: Introducción al almacenamiento de tablas mediante Visual Studio (servicios en la nube)
description: Cómo empezar a usar el almacenamiento de tablas de Azure en un proyecto de servicio en la nube en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cad09a5359bd5d9bd2041eb92d0e994adf80080
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823093"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introducción al almacenamiento de tablas de Azure y a los servicios conectados de Visual Studio (proyectos de servicios en la nube)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

[!INCLUDE [Cloud Services (classic) deprecation announcement](../cloud-services/includes/deprecation-announcement.md)]


En este artículo se describe cómo empezar a usar el almacenamiento de tablas de Azure en Visual Studio después de haber creado o hecho referencia a una cuenta de almacenamiento de Azure en un proyecto de servicios en la nube mediante el cuadro de diálogo **Agregar servicios conectados** de Visual Studio. La operación **Agregar servicios conectados** instala los paquetes de NuGet adecuados para tener acceso al almacenamiento de Azure en el proyecto y agrega la cadena de conexión para la cuenta de almacenamiento a los archivos de configuración del proyecto.

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.

Para comenzar, necesita crear una tabla en su cuenta de almacenamiento. También le mostraremos cómo crear una tabla de Azure en código, además de cómo realizar operaciones básicas de tabla y de entidad, como agregar, modificar y leer entidades de tabla. Los ejemplos están escritos en código C\# y usan la [biblioteca del cliente de Microsoft Azure Storage para .NET](/previous-versions/azure/dn261237(v=azure.100)).

**NOTA:** Algunas de las API que realizan llamadas al almacenamiento de Azure son asincrónicas. Vea [Programación asincrónica con Async y Await](/previous-versions/hh191443(v=vs.140)) para más información. El código siguiente supone que se están utilizando métodos de programación asincrónica.

* Consulte [Introducción al Almacenamiento de tablas de Azure mediante .NET](../cosmos-db/tutorial-develop-table-dotnet.md) para más información sobre la manipulación de tablas mediante programación.
* Vea [Documentación sobre Almacenamiento](https://azure.microsoft.com/documentation/services/storage/) para información general sobre Azure Storage.
* Vea [Documentación sobre Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) para información general sobre Azure Cloud Services.
* Vea [ASP.NET](https://www.asp.net) para más información sobre la programación de aplicaciones ASP.NET.

## <a name="access-tables-in-code"></a>Acceso a tablas en código
Para obtener acceso a las tablas en los proyectos de servicios en la nube, deberá incluir los elementos siguientes en los archivos de origen C# que acceden al almacenamiento Tabla de Azure.

1. Asegúrese de que las declaraciones del espacio de nombres de la parte superior del archivo de C# incluyen estas instrucciones **using** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Azure.Data.Table;
    using System.Collections.Generic
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Obtenga un objeto **AzureStorageConnectionString** para crear un objeto **TableServiceClient** que realice operaciones de nivel de cuenta, como crear y eliminar tablas.
   
    ```csharp
    string storageConnString = "_AzureStorageConnectionString"
    ```

   > [!NOTE]
   > Use todo el código anterior delante del código que aparece en los ejemplos siguientes.
   
3. Obtenga un objeto **TableServiceClient** para hacer referencia a los objetos de tabla de la cuenta de almacenamiento.
   
    ```csharp
    // Create the table service client.
    TableServiceClient tableServiceClient = new TableServiceClient(storageConnString);
    ```

4. Obtenga un objeto de referencia **TableClient** para hacer referencia a una tabla y entidades especificas.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    TableClient peopleTable = tableServiceClient.GetTableClient("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Crear una tabla en el código
Para crear la tabla de Azure, basta con que agregue una llamada a **CreateIfNotExistsAsync** después de obtener un objeto **TableClient**, tal como se describe en la sección "Acceso a tablas en código".

```csharp
// Create the TableClient if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla
Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad llamada **CustomerEntity** que usa el nombre del cliente como clave de fila y el apellido como clave de partición.

```csharp
public class CustomerEntity : ITableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Las operaciones AddEntity que afectan a las entidades se realizan con el objeto **TableClient** que se creó anteriormente en la sección "Acceso a tablas en código". El método **AddEntity** representa la operación que se hará. En el ejemplo de código siguiente se muestra cómo crear un objeto **TableClient** y un objeto **CustomerEntity**. Para preparar la operación, un objeto **AddEntity** inserta la entidad de cliente en una tabla.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Inserts the customer entity.
peopleTable.AddEntity(customer1)
```


## <a name="insert-a-batch-of-entities"></a>Inserción de un lote de entidades
Puede insertar varias entidades en una tabla mediante una única operación de escritura. En el ejemplo de código siguiente se crean dos objetos de entidad ("Jeff Smith" y "Ben Smith"), se agregan a un objeto **addEntitiesBatch** mediante el método AddRange y, luego, se inicia la operación llamando a **TableClient.SubmitTransactionAsync**.

```csharp
// Create a list of 2 entities with the same partition key.
List<CustomerEntity> entityList = new List<CustomerEntity>
{
    new CustomerEntity("Smith", "Jeff")
    {
        { "Email", "Jeff@contoso.com" },
        { "PhoneNumber", "425-555-0104" }
    },
    new CustomerEntity("Smith", "Ben")
    {
        { "Email", "Ben@contoso.com" },
        { "PhoneNumber", "425-555-0102" }
    },
};

// Create the batch.
List<TableTransactionAction> addEntitiesBatch = new List<TableTransactionAction>();

// Add the entities to be added to the batch.
addEntitiesBatch.AddRange(entityList.Select(e => new TableTransactionAction(TableTransactionActionType.Add, e)));

// Submit the batch.
Response<IReadOnlyList<Response>> response = await peopleTable.SubmitTransactionAsync(addEntitiesBatch).ConfigureAwait(false);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Obtención de todas las entidades en una partición
Para consultar todas las entidades de una partición en una tabla, use un método **Query**. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

```csharp
Pageable<CustomerEntity> queryResultsFilter = peopleTable.Query<CustomerEntity>(filter: "PartitionKey eq 'Smith'");

// Print the fields for each customer.
foreach (CustomerEntity qEntity in queryResultsFilter)
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", qEntity.PartitionKey, qEntity.RowKey, qEntity.Email, qEntity.PhoneNumber);
}
```


## <a name="get-a-single-entity"></a>Obtención de una sola entidad
Puede escribir una consulta para obtener una sola entidad concreta. El código siguiente usa un método **GetEntityAsync** para especificar un cliente llamado "Ben Smith". Este método devuelve solo una entidad, en lugar de una colección, y el valor devuelto en **GetEntityAsync.Result** es un objeto **CustomerEntity**. La forma más rápida de recuperar una sola entidad del servicio **Tabla** es especificar claves tanto de partición como de fila en las consultas.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

// Print the phone number of the result.
if (singleResult.Result != null)
    Console.WriteLine(((CustomerEntity)singleResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Eliminación de una entidad
Puede eliminar fácilmente una entidad después de haberla encontrado. El código siguiente busca una entidad de cliente denominada "Ben Smith" y, si la encuentra, la elimina.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

CustomerEntity deleteEntity = (CustomerEntity)singleResult.Result;

// Delete the entity given the partition and row key.
if (deleteEntity != null)
{
    await peopleTable.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
