---
title: Enlace de entrada de Azure SQL para Functions
description: Aprenda a usar el enlace de entrada de Azure SQL en Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 11/9/2021
ms.author: drskwier
ms.openlocfilehash: 45afc0cc6ccf42711b878d3fccd7912d2bdc750d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349986"
---
# <a name="azure-sql-input-binding-for-azure-functions"></a>Enlace de entrada de Azure SQL para Azure Functions

El enlace de entrada de Azure SQL recupera datos de una base de datos y los pasa al parámetro de entrada de la función.

Para obtener información sobre los detalles de instalación y configuración, vea la [información general](./functions-bindings-azure-sql.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

En esta sección se incluyen los ejemplos siguientes:

* [Desencadenador de HTTP, buscar identificador de cadena de consulta](#http-trigger-look-up-id-from-query-string-c)
* [Desencadenador HTTP, obtener varios documentos de los datos de ruta](#http-trigger-get-multiple-items-from-route-data-c)

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

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Desencadenador de HTTP, buscar identificador a partir de una cadena de consulta

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que recupera un registro individual. La función la desencadena una solicitud HTTP que utiliza una cadena de consulta para especificar el identificador. Ese identificador se usa para recuperar un registro `ToDoItem` con la consulta especificada.

> [!NOTE]
> El parámetro de la cadena de consulta HTTP distingue mayúsculas de minúsculas.
>

```cs
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItem
    {
        [FunctionName("GetToDoItem")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitem")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where Id = @Id",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Id={Query.id}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItem)
        {
            return new OkObjectResult(toDoItem.FirstOrDefault());
        }
    }
}
```

<a id="http-trigger-get-multiple-items-from-route-data-c"></a>

### <a name="http-trigger-get-multiple-items-from-route-data"></a>Desencadenador HTTP, obtener varios elementos de los datos de ruta

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que recupera documentos devueltos por la consulta. La función la desencadena una solicitud HTTP que utiliza los datos de la ruta para especificar el valor de un parámetro de consulta. Ese parámetro se usa para filtrar los registros `ToDoItem` de la consulta especificada.

```cs
using System.Collections.Generic;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItems
    {
        [FunctionName("GetToDoItems")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
        {
            return new OkObjectResult(toDoItems);
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

El constructor del atributo toma el texto del comando SQL, el tipo de comando, los parámetros y el nombre del valor de la cadena de conexión. El comando puede ser una consulta Transact-SQL (T-SQL) con el tipo de comando `System.Data.CommandType.Text` o el nombre del procedimiento almacenado con el tipo de comando `System.Data.CommandType.StoredProcedure`. El nombre de la configuración de la cadena de conexión se corresponde con la configuración de la aplicación (en `local.settings.json`para el desarrollo local) que contiene la [cadena de conexión](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) a la instancia de Azure SQL o SQL Server.

A continuación, se muestra un ejemplo del atributo `Sql` en una signatura de método:

```csharp
    [FunctionName("GetToDoItems")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
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

- [Guardado de datos en una base de datos (enlace de salida)](./functions-bindings-azure-sql-output.md)
