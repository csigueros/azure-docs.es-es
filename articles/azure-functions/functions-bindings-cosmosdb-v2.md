---
title: Enlaces de Azure Cosmos DB para Functions 2.x y posteriores
description: Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure Cosmos DB en Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.openlocfilehash: dbeec528e22d3622b374d6cfee2d51a61b989aac
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661167"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Información general sobre el desencadenador y los enlaces de Azure Cosmos DB para Azure Functions 2.x y versiones superiores

> [!div class="op_single_selector" title1="Seleccione la versión del entorno de ejecución de Azure Functions que usa: "]
> * [Versión 1](functions-bindings-cosmosdb.md)
> * [Versión 2 y superiores](functions-bindings-cosmosdb-v2.md)

En este conjunto de artículos se explica cómo trabajar con enlaces de [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) en Azure Functions 2.x y versiones superiores. Azure Functions enlaces de desencadenador, de entrada y de salida para Azure Cosmos DB.

| Acción | Tipo |
|---------|---------|
| Ejecución de una función cuando se crea o modifica un documento de Azure Cosmos DB | [Desencadenador](./functions-bindings-cosmosdb-v2-trigger.md) |
| Lectura de un documento de Azure Cosmos DB | [Enlace de entrada](./functions-bindings-cosmosdb-v2-input.md) |
| Guardar cambios en un documento de Azure Cosmos DB  |[Enlace de salida](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Esta referencia va dirigida a [Azure Functions, versión 2.x y superiores](functions-versions.md).  Para obtener información acerca del uso de estos enlaces en Functions 1.x, consulte [Enlaces de Azure Cosmos DB para Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> Este enlace se llamaba originalmente DocumentDB. En Functions, versión 2.x y superiores, tanto el desencadenador, como los enlaces y el paquete se denominan Cosmos DB.

## <a name="supported-apis"></a>API admitidas

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Adición a la aplicación de Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x y superiores

Para trabajar con el desencadenador y los enlaces, es necesario hacer referencia al paquete adecuado. En las bibliotecas de clases de .NET se usa el paquete NuGet, mientras que en los demás tipos de aplicaciones se emplea el conjunto de extensiones.

| Idioma                                        | Agregar mediante...                                   | Observaciones 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalación del [paquete NuGet], versión 3.x | |
| Script de C#, Java, JavaScript, Python, PowerShell | Registro de [conjunto de extensiones]          | Se recomienda usar la [extensión Azure Tools] con Visual Studio Code. |
| Script de C# (solo en línea en Azure Portal)         | Adición de un enlace                            | Para actualizar extensiones de enlace existentes sin tener que volver a publicar la aplicación de funciones, consulte [Actualización de las extensiones]. |

[Paquete NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[conjunto de extensiones]: ./functions-bindings-register.md#extension-bundles
[Actualización de las extensiones]: ./functions-bindings-register.md
[Extensión Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="cosmos-db-extension-4x-and-higher"></a>Extensión de Cosmos DB 4.x y posteriores

Hay disponible una nueva versión de la extensión de enlaces de Cosmos DB en un [paquete NuGet en versión preliminar](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB/4.0.0-preview1). Esta versión preliminar presenta la posibilidad de [conectarse con una identidad en lugar de un secreto](./functions-reference.md#configure-an-identity-based-connection). En el caso de las aplicaciones .NET, también cambian los tipos con los que se puede enlazar; así, los tipos del SDK v2 `Microsoft.Azure.DocumentDB` se reemplazan por otros tipos más recientes del SDK v3 [Microsoft.Azure.Cosmos](../cosmos-db/sql/sql-api-sdk-dotnet-standard.md). Obtenga más información sobre en qué se diferencian estos nuevos tipos y cómo migrarlos en la [guía de migración de SDK](../cosmos-db/sql/migrate-dotnet-v3.md) y en los ejemplos de [desencadenador](./functions-bindings-cosmosdb-v2-trigger.md), [enlace de entrada](./functions-bindings-cosmosdb-v2-input.md) y [enlace de salida](./functions-bindings-cosmosdb-v2-output.md).

> [!NOTE]
> Actualmente, la autenticación con una identidad en lugar de un secreto mediante la extensión en versión preliminar 4.x solo está disponible para los planes elásticos Premium. 

### <a name="functions-1x"></a>Functions 1.x

Las aplicaciones de Functions 1.x tienen automáticamente una referencia al paquete NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versión 2.x.

## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace | Referencia |
|---|---|
| CosmosDB | [Códigos de error de CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>configuración de host.json

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Propiedad  |Valor predeterminado |Descripción |
|----------|--------|------------|
|GatewayMode|Puerta de enlace|Modo de conexión que usa la función al conectarse al servicio de Azure Cosmos DB. Las opciones son `Direct` y `Gateway`|
|Protocolo|Https|Protocolo de conexión que usa la función al conectarse al servicio de Azure Cosmos DB. Lea [este documento para obtener una explicación de los dos modos](../cosmos-db/performance-tips.md#networking). <br><br> Esta configuración no está disponible en la [versión 4.x de la extensión](#cosmos-db-extension-4x-and-higher). |
|leasePrefix|N/D|Prefijo de concesión que se usará en todas las funciones de una aplicación. <br><br> Esta configuración no está disponible en la [versión 4.x de la extensión](#cosmos-db-extension-4x-and-higher).|

## <a name="next-steps"></a>Pasos siguientes

- [Ejecución de una función cuando se crea o modifica un documento de Azure Cosmos DB (desencadenador)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Lectura de un documento de Azure Cosmos DB (enlace de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Guardar los cambios en un documento de Azure Cosmos DB (enlace de salida)](./functions-bindings-cosmosdb-v2-output.md)