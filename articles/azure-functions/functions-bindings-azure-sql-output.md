---
title: Enlace de salida de Azure SQL para Functions
description: Aprenda a usar el enlace de salida de Azure SQL en Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 37a9601f30c64108b079ec8573553a05088c8629
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493274"
---
# <a name="azure-sql-output-binding-for-azure-functions-preview"></a>Enlace de salida de Azure SQL para Azure Functions (versión preliminar)

El enlace de salida de Azure SQL le permite escribir en una base de datos.

Para obtener información sobre los detalles de instalación y configuración, vea la [información general](./functions-bindings-azure-sql.md).


<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

En esta sección se incluyen los ejemplos siguientes:

* [Desencadenador Http, escribir un registro](#http-trigger-write-one-record-c)
* [Desencadenador http, escribir registros mediante IAsyncCollector](#http-trigger-write-records-using-iasynccollector-c)

En este ejemplo se hace referencia a un tipo `ToDoItem` y una tabla de base de datos correspondiente:

```cs
namespace AzureSQLSamples
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public int Priority { get; set; }
        public string Description { get; set; }
    }
}
```

```sql
CREATE TABLE dbo.ToDo (
    [Id] int primary key,
    [Priority] int null,
    [Description] nvarchar(200) not null
)
```

<a id="http-trigger-write-one-record-c"></a>

### <a name="http-trigger-write-one-record"></a>Desencadenador Http, escribir un registro

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que agrega un documento a una base de datos mediante los datos que se proporcionan en el mensaje desde Queue Storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace AzureSQLSamples
{
    public static class WriteOneRecord
    {
        [FunctionName("WriteOneRecord")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            ILogger log,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
        {
            newItem = new ToDoItem
            {
                Id = req.Query["id"],
                Description =req.Query["desc"]
            };

            log.LogInformation($"C# HTTP trigger function inserted one row");
            return new CreatedResult($"/api/addtodo", newItem);
        }
    }
}
```

<a id="http-trigger-write-records-using-iasynccollector-c"></a>

### <a name="http-trigger-write-records-using-iasynccollector"></a>Desencadenador Http, escribir registros mediante IAsyncCollector

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que agrega una colección de registros a una base de datos mediante los datos que se proporcionan en un mensaje en un JSON de cuerpo de HTT POST.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Newtonsoft.Json;
using System.IO;
using System.Threading.Tasks;

namespace AzureSQLSamples
{
    public static class WriteRecordsAsync
    {
        [FunctionName("WriteRecordsAsync")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "addtodo-asynccollector")]
            HttpRequest req,
            [Sql("dbo.Products", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> newItems)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            var incomingItems = JsonConvert.DeserializeObject<ToDoItem[]>(requestBody);
            foreach (ToDoItem newItem in incomingItems)
            {
                await newItems.AddAsync(newItem);
            }
            // Rows are upserted here
            await newItems.FlushAsync();

            return new CreatedResult($"/api/addtodo-asynccollector", "done");
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

El enlace de Azure SQL para Azure Functions no es compatible actualmente con JavaScript.

# <a name="python"></a>[Python](#tab/python)

El enlace de Azure SQL para Azure Functions no es compatible actualmente con Python.

---

## <a name="attributes-and-annotations"></a>Atributos y anotaciones

# <a name="c"></a>[C#](#tab/csharp)

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs).

El constructor del atributo toma el texto del comando SQL y el nombre del valor de la cadena de conexión. Para un enlace de salida, la cadena de comando SQL es un nombre de tabla donde se almacenarán los datos. El nombre de la configuración de la cadena de conexión se corresponde con la configuración de la aplicación (en `local.settings.json`para el desarrollo local) que contiene la [cadena de conexión](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) a la instancia de Azure SQL o SQL Server.

A continuación, se muestra un ejemplo del atributo `Sql` en una signatura de método:

```csharp
    [FunctionName("HTTPtoSQL")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
    {
        ...
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

El enlace de Azure SQL para Azure Functions no es compatible actualmente con JavaScript.

# <a name="python"></a>[Python](#tab/python)

El enlace de Azure SQL para Azure Functions no es compatible actualmente con Python.

---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## <a name="next-steps"></a>Pasos siguientes

- [Lectura de datos de una base de datos (enlace de entrada)](./functions-bindings-azure-sql-input.md)
