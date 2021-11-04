---
title: Introducción a la actualización parcial de documentos de Azure Cosmos DB
description: En este artículo se proporciona un ejemplo de cómo usar la actualización parcial de documentos con los SDK de .NET, Java y Node.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/25/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74f55120eb7a6ceeb1891be44e50344fe71b4de5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093591"
---
# <a name="azure-cosmos-db-partial-document-update-getting-started"></a>Actualización parcial de documentos de Azure Cosmos DB: introducción
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En este artículo se proporcionan ejemplos de cómo usar la actualización parcial de documentos con los SDK de .NET, Java y Node, junto con los errores comunes que puede encontrar. Se han proporcionado ejemplos de código para los escenarios siguientes:

- Ejecución de una sola operación de revisión
- Combinación de varias operaciones de revisión
- Sintaxis de revisión condicional basada en el predicado de filtro
- Ejecución de la operación de revisión como parte de una transacción

## <a name="net"></a>.NET

Puede descargar el [SDK de .NET v3 de Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) desde la [galería de NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)

> [!NOTE]
> El ejemplo completo se puede encontrar en el [repositorio de ejemplos de .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3) en GitHub.

**Ejecución de una sola operación de revisión**

```csharp
ItemResponse<SalesOrder> response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/TotalDue", 0) });

SalesOrder updated = response.Resource;
```

**Combinación de varias operaciones de revisión**

```csharp
List<PatchOperation> patchOperations = new List<PatchOperation>();
patchOperations.Add(PatchOperation.Add("/nonExistentParent/Child", "bar"));
patchOperations.Add(PatchOperation.Remove("/cost"));
patchOperations.Add(PatchOperation.Increment("/taskNum", 6));
patchOperations.Add(patchOperation.Set("/existingPath/newproperty",value));

container.PatchItemAsync<item>(
                id: 5,
                partitionKey: new PartitionKey("task6"),
                patchOperations: patchOperations );
```

**Sintaxis de revisión condicional basada en el predicado de filtro**

```csharp
PatchItemRequestOptions patchItemRequestOptions = new PatchItemRequestOptions
{
    FilterPredicate = "from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))"
};
response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/ShippedDate", DateTime.UtcNow) },
    patchItemRequestOptions);

SalesOrder updated = response.Resource;
```

**Ejecución de la operación de revisión como parte de una transacción**


```csharp
List<PatchOperation> patchOperationsUpdateTask = new List<PatchOperation>()
            {
                PatchOperation.Add("/children/1/pk", "patched"),
                PatchOperation.Remove("/description"),
                PatchOperation.Add("/taskNum", 8),
                PatchOperation.Replace("/taskNum", 12)
            };

TransactionalBatchPatchItemRequestOptions requestOptionsFalse = new TransactionalBatchPatchItemRequestOptions()
            {
                FilterPredicate = "from c where c.taskNum = 3"
            };

TransactionalBatchInternal transactionalBatchInternalFalse = (TransactionalBatchInternal)containerInternal.CreateTransactionalBatch(new Cosmos.PartitionKey(testItem.pk));
transactionalBatchInternalFalse.PatchItem(id: testItem1.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.PatchItem(id: testItem2.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.ExecuteAsync());
```

## <a name="java"></a>Java

Puede agregar el [SDK de Java SDK v4 de Azure Cosmos DB ](sql-api-sdk-java-v4.md) (versión `4.20.0` o superior) a la lista de dependencias en su `pom.xml` o descargarlas directamente desde [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos).

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.20.0</version>
</dependency>
```

> [!NOTE]
> El ejemplo completo se puede encontrar en el [repositorio de ejemplos del SDK de Java v4](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/) en GitHub.

**Ejecución de una sola operación de revisión**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true);

CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Combinación de varias operaciones de revisión**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true)
                     .replace("/parents/0/familyName", "Doe");
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Sintaxis de revisión condicional basada en el predicado de filtro**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
                                                                   .add("/vaccinated", true);
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();
options.setFilterPredicate("from f where f.registered = true");

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Ejecución de la operación de revisión como parte de una transacción**

```java
CosmosBatch batch = CosmosBatch.createCosmosBatch(new PartitionKey(family.getLastName()));
batch.createItemOperation(family);

CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create().add("/registered", false);
batch.patchItemOperation(family.getId(), cosmosPatchOperations);

CosmosBatchResponse response = container.executeCosmosBatch(batch);
if (response.isSuccessStatusCode()) {
    // if transactional batch succeeds   
}
```

## <a name="node"></a>Nodo

**Ejecución de una sola operación de revisión**

```javascript
const patchSource = itemDefList[1];

const replaceOperation: PatchOperation[] = 
    [{ 
      op: "replace", 
      path: "/lastName", 
      value: "Martin" 
    }];

const { resource: patchSource1 } = await container.item(patchSource.lastName).patch(replaceOperation); 
console.log(`Patched ${patchSource1.lastName} to new ${patchSource1.lastName}.`); 
```

**Combinación de varias operaciones de revisión**

```javascript
const multipleOperations: PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/aka", 
      value: "MeFamily" 
    }, 
    { 
      op: "add", 
      path: "/years", 
      value: 12 
    }, 
    { 
      op: "replace", 
      path: "/lastName", 
      value: "Jose" 
    }, 
    { 
      op: "remove", 
      path: "/parents" 
    }, 
    { 
      op: "set", 
      path: "/children/firstName", 
      value: "Anderson" 
    }, 
    { 
      op: "incr", 
      path: "/years", 
      value: 5 
    } 
  ]; 

const { resource: patchSource2  } = await container.item(patchSource.id).patch(multipleOperations); 
 ```

**Sintaxis de revisión condicional basada en el predicado de filtro**

```javascript
const operations : PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/newImproved", 
      value: "it works" 
    } 
  ]; 

const condition = "from c where NOT IS_DEFINED(c.newImproved)"; 

const { resource: patchSource3 } = await container 
    .item(patchSource.id) 
    .patch({ condition, operations }); 

console.log(`Patched ${patchSource3} to new ${patchSource3}.`); 
```

## <a name="support-for-server-side-programming"></a>Compatibilidad con la programación en el lado servidor

Las operaciones de actualización parcial de documentos también pueden [ejecutarse en el lado servidor](stored-procedures-triggers-udfs.md) utilizando procedimientos almacenados, desencadenadores y funciones definidas por el usuario.


```javascript
        this.patchDocument = function (documentLink, patchSpec, options, callback) { 
                if (arguments.length < 2) { 
                    throw new Error(ErrorCodes.BadRequest, sprintf(errorMessages.invalidFunctionCall, 'patchDocument', 2, arguments.length)); 
                }
                if (patchSpec === null || !(typeof patchSpec === "object" || Array.isArray(patchSpec))) { 
                    throw new Error(ErrorCodes.BadRequest, errorMessages.patchSpecMustBeObjectOrArray); 
                } 

                var documentIdTuple = validateDocumentLink(documentLink, false); 
                var collectionRid = documentIdTuple.collId; 
                var documentResourceIdentifier = documentIdTuple.docId; 
                var isNameRouted = documentIdTuple.isNameRouted; 

                patchSpec = JSON.stringify(patchSpec); 
                var optionsCallbackTuple = validateOptionsAndCallback(options, callback); 

                options = optionsCallbackTuple.options; 
                callback = optionsCallbackTuple.callback; 

                var etag = options.etag || ''; 
                var indexAction = options.indexAction || ''; 

                return collectionObjRaw.patch( 
                    collectionRid, 
                    documentResourceIdentifier, 
                    isNameRouted, 
                    patchSpec, 
                    etag, 
                    indexAction, 
                    function (err, response) { 
                        if (callback) { 
                            if (err) { 
                                callback(err); 
                            } else { 
                                callback(undefined, JSON.parse(response.body), response.options); 
                            } 
                        } else { 
                            if (err) { 
                                throw err; 
                            } 
                        } 
                    } 
                ); 
            }; 
```

## <a name="troubleshooting"></a>Solución de problemas

Esta es una lista de errores comunes que puede encontrar al usar esta característica:

| **Mensaje de error** | **Descripción** |
| ------------ | -------- |
| Solicitud de revisión no válida: compruebe la sintaxis de la especificación de revisión.| La sintaxis de la operación de revisión no es válida. Revise [la especificación](partial-document-update.md#partial-document-update-specification).
| Solicitud de revisión no válida: no se puede aplicar una revisión a la propiedad del sistema `SYSTEM_PROPERTY`. | No se admite la aplicación de revisiones a propiedades generadas por el sistema como  `_id`,  `_ts`,  `_etag` o  `_rid`. Para más información: [Preguntas más frecuentes sobre la actualización parcial de documentos](partial-document-update-faq.yml#is-partial-document-update-supported-for-system-generated-properties-). 
| El número de operaciones de revisión no puede superar 10. | Hay un límite de 10 operaciones de revisión que se pueden agregar en una única especificación de revisión. Para más información: [Preguntas más frecuentes sobre la actualización parcial de documentos](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-).
| Para la operación (`PATCH_OPERATION_INDEX`): el índice (`ARRAY_INDEX`) en el que operar está fuera de los límites de matriz. | El índice del elemento de matriz al que se va a aplicar la revisión está fuera de los límites. 
| Para la operación (`PATCH_OPERATION_INDEX`): el nodo(`PATH`) que se va a sustituir se ha eliminado anteriormente en la operación.| La ruta de acceso a la que intenta aplicar la revisión no existe.
| Para la operación (`PATCH_OPERATION_INDEX`): el nodo (`PATH`) que se va a quitar no está presente. Nota: Es posible que también se haya quitado anteriormente en la transacción.  | La ruta de acceso a la que intenta aplicar la revisión no existe.
| Para la operación (`PATCH_OPERATION_INDEX`): el nodo (`PATH`) que se va a reemplazar no está presente. | La ruta de acceso a la que intenta aplicar la revisión no existe.
| Para la operación (`PATCH_OPERATION_INDEX`): el nodo (`PATH`) no es un número.| La operación de incremento solo puede funcionar en enteros y flotantes. Para obtener más información: [Operaciones admitidas](partial-document-update.md#supported-operations).
| Para la operación (`PATCH_OPERATION_INDEX`): la adición de una operación solo puede crear un objeto secundario de un nodo existente (matriz u objeto) y no puede crear la ruta de acceso de forma recursiva, no se encuentra ninguna ruta de acceso más allá de: `PATH`. | Las rutas de acceso secundarias se pueden agregar a un tipo de nodo de objeto o matriz. Además, para crear el elemento secundario`n`, debe estar presente el elemento secundario `n-1`. 
| Para la operación (`PATCH_OPERATION_INDEX`): la operación dada solo puede crear un objeto secundario de un nodo existente (matriz u objeto) y no puede crear la ruta de acceso de forma recursiva, no se encuentra ninguna ruta de acceso más allá de `PATH`. | Las rutas de acceso secundarias se pueden agregar a un tipo de nodo de objeto o matriz. Además, para crear el elemento secundario`n`, debe estar presente el elemento secundario `n-1`. 

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el resumen conceptual de la [actualización parcial de documentos](partial-document-update.md).
