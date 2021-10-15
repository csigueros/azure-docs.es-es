---
title: Solución de problemas de conector Azure Cosmos DB
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con los conectores Azure Cosmos DB y Azure Cosmos DB (API de SQL) en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: cd5f10c05ab1ca2524f384a4d085a913193e7293
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390445"
---
# <a name="troubleshoot-the-azure-cosmos-db-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector Azure Cosmos DB en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con los conectores Azure Cosmos DB y Azure Cosmos DB (API de SQL) en Azure Data Factory y Azure Synapse.

## <a name="error-message-request-size-is-too-large"></a>Mensaje de error: Request size is too large (El tamaño de la solicitud es demasiado grande)

- **Síntomas**: Cuando se copian datos en Azure Cosmos DB con un tamaño de lote de escritura predeterminado, se recibe el siguiente error: `Request size is too large.`

- **Causa**: Azure Cosmos DB limita el tamaño de una única solicitud a 2 MB. La fórmula es *tamaño de la solicitud = tamaño de documento único \* tamaño de lote de escritura*. Si el tamaño del documento es grande, el comportamiento predeterminado generará un tamaño de solicitud demasiado grande. Puede ajustar el tamaño del lote de escritura.

- **Solución:** En el receptor de la actividad de copia, reduzca el valor del *tamaño de lote de escritura* (el valor predeterminado es 10000).

## <a name="error-message-unique-index-constraint-violation"></a>Mensaje de error: Unique index constraint violation (Infracción de restricción de índice único)

- **Síntomas**: Al copiar datos en Azure Cosmos DB, recibe el siguiente error:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Causa**: Hay dos causas posibles:

    - Causa 1: Si utiliza **Insert** como comportamiento de escritura, este error indica que los datos de origen tienen filas u objetos con el mismo identificador.
    - Causa 2: Si usa **Upsert** como comportamiento de escritura y establece otra clave única en el contenedor, este error significa que los datos de origen tienen filas u objetos con identificadores diferentes, pero con el mismo valor para la clave única definida.

- **Solución:** 

    - Para la primera causa, establezca **Upsert** como comportamiento de escritura.
    - Para la segunda causa, asegúrese de que cada documento tenga un valor diferente para la clave única definida.

## <a name="error-message-request-rate-is-large"></a>Mensaje de error: La tasa de solicitudes es grande

- **Síntomas**: Al copiar datos en Azure Cosmos DB, recibe el siguiente error:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Causa**: El número de unidades de solicitud (RU) usadas es mayor que el valor de RU disponible configurado en Azure Cosmos DB. Para saber cómo Azure Cosmos DB calcula las RU, consulte [Unidades de solicitud en Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Solución:** Pruebe una de las dos soluciones siguientes:

    - Aumente el número de *RU de contenedor* a un valor mayor en Azure Cosmos DB. Esta solución mejorará el rendimiento de la actividad de copia, pero supondrá más costo en Azure Cosmos DB. 
    - Disminuya *writeBatchSize* a un valor inferior, como 1000, y *parallelCopies* a un valor inferior, como 1. Esta solución reducirá el rendimiento de la ejecución de copias, pero no supondrá un mayor costo en Azure Cosmos DB.

## <a name="columns-missing-in-column-mapping"></a>Falta una columna en la asignación de columnas

- **Síntomas**: Cuando se importa un esquema en Azure Cosmos DB para la asignación de columnas, faltan algunas columnas. 

- **Causa**: las canalizaciones de Azure Data Factory y Synapse deducen el esquema de los diez primeros documentos de Azure Cosmos DB. Si algunas columnas o propiedades del documento no contienen valores, no se detectará el esquema y, por lo tanto, no se mostrará.

- **Solución:** Puede optimizar la consulta como se muestra en el código siguiente para forzar que los valores de columna se muestren en el conjunto de resultados con valores vacíos. Supongamos que falta la columna *impossible* en los 10 primeros documentos. Como alternativa, puede agregar manualmente la columna para la asignación.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

## <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensaje de error: The GuidRepresentation for the reader is CSharpLegacy (El valor de GuidRepresentation del lector es CSharpLegacy)

- **Síntomas**: Al copiar datos desde MongoAPI o MongoDB de Azure Cosmos DB con el campo de identificador único universal (UUID), recibe el siguiente error:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Causa**: Hay dos maneras de representar el UUID en JSON binario (BSON): UuidStardard y UuidLegacy. De forma predeterminada, UuidLegacy se usa para leer datos. Si los datos de UUID en MongoDB son UuidStandard, recibirá un error.

- **Solución:** En la cadena de conexión de MongoDB, agregue la opción *uuidRepresentation=standard*. Para más información, consulte [Cadena de conexión de MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="error-code-cosmosdbsqlapioperationfailed"></a>Código de error: CosmosDbSqlApiOperationFailed

- **Mensaje**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Causa**: Problema con la operación CosmosDbSqlApi.  Esto se aplica específicamente al conector Cosmos DB (API de SQL).

- **Recomendación:**  Para consultar los detalles del error, vea el [documento de ayuda de Azure Cosmos DB](../cosmos-db/troubleshoot-dot-net-sdk.md). Para recibir ayuda adicional, póngase en contacto con el equipo de Azure Cosmos DB.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
