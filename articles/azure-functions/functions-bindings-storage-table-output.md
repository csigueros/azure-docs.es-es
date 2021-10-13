---
title: Enlaces de salida de Azure Table Storage para Azure Functions
description: Descubra cómo usar los enlaces de salida de Azure Table Storage en Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b30fbbca1bfabfdbf9836e00ee2b2d585308774a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129611774"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Enlaces de salida de Azure Table Storage para Azure Functions

Use un enlace de salida de Azure Table Storage para escribir entidades en una tabla de una cuenta de Azure Storage.

> [!NOTE]
> Este enlace de salida no permite actualizar las entidades existentes. Use la operación `TableOperation.Replace`[del SDK de Azure Storage](../cosmos-db/table/table-support.md) para actualizar una entidad existente.

## <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que usa un desencadenador HTTP para escribir una única fila de la tabla.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

En el ejemplo siguiente se muestra un enlace de salida de la tabla en un archivo *function.json* y código de [script de C#](functions-reference-csharp.md) que usa el enlace. La función escribe varias entidades de tabla.

Este es el archivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#configuration) se explican estas propiedades.

Este es el código de script de C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="java"></a>[Java](#tab/java)

En el ejemplo siguiente se muestra una función de Java que usa un desencadenador HTTP para escribir una única fila de la tabla.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

En el ejemplo siguiente se muestra una función de Java que usa un desencadenador HTTP para escribir una varias filas de tabla.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

En el ejemplo siguiente se muestra un enlace de salida de la tabla en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función escribe varias entidades de tabla.

Este es el archivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

En el siguiente ejemplo se muestra cómo escribir varias entidades en una tabla a partir de una función.

Configuración de enlace en _function.json_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Código de PowerShell en _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

En el ejemplo de código siguiente se muestra cómo utilizar el enlace de la salida de Table Storage. El enlace de `table` se configura en *function.json* asignando valores a `name`, `tableName`, `partitionKey`y `connection`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

La función siguiente genera un UUI único para el valor `rowKey` y guarda el mensaje en Table Storage.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Atributos y anotaciones

# <a name="c"></a>[C#](#tab/csharp)

En las [bibliotecas de clase C#](functions-dotnet-class-library.md), use [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

El constructor del atributo toma el nombre de la tabla. El atributo se puede usar en un parámetro `out` o en el valor devuelto de la función, como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Para obtener un ejemplo completo, consulte el [ejemplo de C#](#example).

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Entrada: atributos](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

El script de C# no admite atributos.

# <a name="java"></a>[Java](#tab/java)

En la [biblioteca del entorno de ejecución de funciones de Java](/java/api/overview/azure/functions/runtime), utilice la anotación [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) en los parámetros para escribir valores en Table Storage.

Consulte el [ejemplo para más detalles](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript no admite atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell no admite atributos.

# <a name="python"></a>[Python](#tab/python)

Python no admite atributos.

---

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Table`.

|Propiedad de function.json | Propiedad de atributo |Descripción|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `table`. Esta propiedad se establece automáticamente cuando se crea el enlace en Azure Portal.|
|**direction** | N/D | Se debe establecer en `out`. Esta propiedad se establece automáticamente cuando se crea el enlace en Azure Portal. |
|**name** | N/D | Nombre de la variable que se usa en el código de la función que representa la tabla o entidad. Se establece en `$return` para hacer referencia al valor devuelto de la función.| 
|**tableName** |**TableName** | Nombre de la tabla.| 
|**partitionKey** |**PartitionKey** | Clave de partición de la entidad de tabla que se va a escribir. Consulte la [sección acerca del uso](#usage) para obtener información acerca de cómo usar esta propiedad.| 
|**rowKey** |**RowKey** | Clave de fila de la entidad de tabla que se va a escribir. Consulte la [sección acerca del uso](#usage) para obtener información acerca de cómo usar esta propiedad.| 
|**connection** |**Connection** | El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno de ejecución de Functions busca una configuración de aplicación denominada "MyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Acceda a la entidad de tabla de salida con un parámetro de método `ICollector<T> paramName` o `IAsyncCollector<T> paramName` donde `T` incluye las propiedades `PartitionKey` y `RowKey`. Estas propiedades suelen ir acompañadas de la implementación de `ITableEntity` o la herencia de `TableEntity`.

Una alternativa consiste en usar un parámetro del método `CloudTable` para escribir en la tabla mediante Azure Storage SDK. Si intenta enlazar a `CloudTable` y obtiene un mensaje de error, asegúrese de que tiene una referencia a [la versión correcta del SDK de Storage](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

Acceda a la entidad de tabla de salida con un parámetro de método `ICollector<T> paramName` o `IAsyncCollector<T> paramName` donde `T` incluye las propiedades `PartitionKey` y `RowKey`. Estas propiedades suelen ir acompañadas de la implementación de `ITableEntity` o la herencia de `TableEntity`. El valor `paramName` se especific en la propiedad `name` de *function.json*.

Una alternativa consiste en usar un parámetro del método `CloudTable` para escribir en la tabla mediante Azure Storage SDK. Si intenta enlazar a `CloudTable` y obtiene un mensaje de error, asegúrese de que tiene una referencia a [la versión correcta del SDK de Storage](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Hay dos opciones para la generación de una fila de Table Storage desde una función mediante la anotación [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput):

- **Valor devuelto**: Al aplicar la anotación a la propia función, el valor devuelto de la función se conserva como una fila de Table Storage.

- **Imperativa**: Para establecer explícitamente el valor del mensaje, aplique la anotación a un parámetro específico del tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), donde `T` incluye las propiedades `PartitionKey` y `RowKey`. Estas propiedades suelen ir acompañadas de la implementación de `ITableEntity` o la herencia de `TableEntity`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acceda al evento de salida mediante `context.bindings.<name>`, donde `<name>` es el valor especificado en la propiedad `name` de *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para escribir en los datos de la tabla, use el cmdlet `Push-OutputBinding`, establezca el parámetros `-Name TableBinding` y el parámetro `-Value` igual que los datos de la fila. Consulte el [ejemplo de PowerShell](#example) para más detalles.

# <a name="python"></a>[Python](#tab/python)

Hay dos opciones para la generación de un mensaje de fila de Table Storage desde una función:

- **Valor devuelto**: Establezca la propiedad `name` de *function.json* en `$return`. Con esta configuración, el valor devuelto de la función se conserva como una fila de Table Storage.

- **Imperativa**: Pase un valor al método [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) del parámetro declarado como tipo [Out](/python/api/azure-functions/azure.functions.out). El valor pasado a `set` se conserva como mensaje del centro de eventos.

---

## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace | Referencia |
|---|---|
| Tabla | [Códigos de error de tabla](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, tabla, cola | [Códigos de error de almacenamiento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabla, cola | [Solución de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
