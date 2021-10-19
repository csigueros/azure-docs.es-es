---
title: Ejecución de un trabajo de reindexación en Azure API for FHIR
description: En este artículo se describe cómo ejecutar un trabajo de reindexación a fin de indexar los parámetros de ordenación o búsqueda que todavía no se indexan en la base de datos.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/23/2021
ms.author: cavoeg
ms.openlocfilehash: 3434a868ac11c90b3ad864f94adf6876e358a8aa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772027"
---
# <a name="running-a-reindex-job-in-azure-api-for-fhir"></a>Ejecución de un trabajo de reindexación en Azure API for FHIR

Hay escenarios en los que puede tener parámetros de ordenación o búsqueda en Azure API for FHIR que todavía no se indexaron. Este escenario es relevante cuando se definen sus propios parámetros de búsqueda. No puede utilizar un parámetro de búsqueda en una búsqueda hasta que se lo indexe. En este artículo se analiza cómo ejecutar un trabajo de reindexación a fin de indexar los parámetros de ordenación o búsqueda que todavía no se indexan en la base de datos.

> [!Warning]
> Antes de empezar, lea todo el artículo. Un trabajo de reindexación puede requerir mucho rendimiento. En este artículo se incluyen opciones para limitar y controlar el trabajo de reindexación.

## <a name="how-to-run-a-reindex-job"></a>Procedimientos para ejecutar un trabajo de reindexación 

Para iniciar un trabajo de reindexación, use el ejemplo de código siguiente:

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

Si la solicitud se realiza correctamente, se devuelve un estado de **201 Creado**. El resultado de este mensaje se verá así:

```json
HTTP/1.1 201 Created 
Content-Location: https://{{FHIR URL}}/_operations/reindex/560c7c61-2c70-4c54-b86d-c53a9d29495e 

{
  "resourceType": "Parameters",
  "id": "560c7c61-2c70-4c54-b86d-c53a9d29495e",
  "meta": {
    "versionId": "\"4c0049cd-0000-0100-0000-607dc5a90000\""
  },
  "parameter": [
    {
      "name": "id",
      "valueString": "560c7c61-2c70-4c54-b86d-c53a9d29495e"
    },
    {
      "name": "queuedTime",
      "valueDateTime": "2021-04-19T18:02:17.0118558+00:00"
    },
    {
      "name": "totalResourcesToReindex",
      "valueDecimal": 0.0
    },
    {
      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 0.0
    },
    {
      "name": "progress",
      "valueDecimal": 0.0
    },
    {
      "name": "status",
      "valueString": "Queued"
    },
    {
      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {
      "name": "resources",
      "valueString": ""
    },
    {
      "name": "searchParams",
      "valueString": ""
    }
  ]
}
```

> [!NOTE]
> Para comprobar el estado de un trabajo de reindexación o cancelar el trabajo, necesitará el identificador de la reindexación. Este es el identificador del recurso Parameters resultante. En el ejemplo anterior, el identificador del trabajo de reindexación sería `560c7c61-2c70-4c54-b86d-c53a9d29495e`.

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>Comprobación del estado de un trabajo de reindexación

Una vez que haya iniciado un trabajo de reindexación, puede comprobar el estado del trabajo mediante la siguiente llamada:

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

A continuación, se muestra el estado del resultado del trabajo de reindexación:

```json
{

  "resourceType": "Parameters",
  "id": "b65fd841-1c62-47c6-898f-c9016ced8f77",
  "meta": {

    "versionId": "\"1800f05f-0000-0100-0000-607a1a7c0000\""
  },
  "parameter": [

    {

      "name": "id",
      "valueString": "b65fd841-1c62-47c6-898f-c9016ced8f77"
    },
    {

      "name": "startTime",
      "valueDateTime": "2021-04-16T23:11:35.4223217+00:00"
    },
    {

      "name": "queuedTime",
      "valueDateTime": "2021-04-16T23:11:29.0288163+00:00"
    },
    {

      "name": "totalResourcesToReindex",
      "valueDecimal": 262544.0
    },
    {

      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 5754.0
    },
    {

      "name": "progress",
      "valueDecimal": 2.0
    },
    {

      "name": "status",
      "valueString": "Running"
    },
    {

      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {

      "name": "resources",
      "valueString": 
      "{LIST OF IMPACTED RESOURCES}"
    },
    {
```

La información siguiente se muestra en el resultado del trabajo de reindexación:

* **totalResourcesToReindex**: incluye el número total de recursos que se reindexan como parte del trabajo.

* **resourcesSuccessfullyReindexed**: el total de recursos que ya se reindexó correctamente.

* **progress**: porcentaje completado del trabajo de reindexación. Equivale a resourcesSuccessfullyReindexed/totalResourcesToReindex x 100.

* **status:** indica si el trabajo de reindex está en cola, en ejecución, completo, con errores o cancelado.

* **resources:** enumera todos los tipos de recursos afectados por el trabajo de reindexación.

## <a name="delete-a-reindex-job"></a>Eliminación de un trabajo de reindexación

Si necesita cancelar un trabajo de reindexación, use una llamada de eliminación y especifique el identificador del trabajo de reindexación:

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>Consideraciones de rendimiento

Un trabajo de reindexación puede requerir mucho rendimiento. Hemos implementado algunos controles de limitación para ayudarlo a administrar cómo se ejecutará un trabajo de reindexación en la base de datos.

> [!NOTE]
> En los conjuntos de datos de gran tamaño, no es poco frecuente que la ejecución de un trabajo de reindexación dure varios días. En el caso de una base de datos con 30 000 000 millones de recursos, observamos que la reindexación de toda la base de datos tardó entre 4 y 5 días a 100 000 RU.

A continuación se muestra una tabla en la que se describen los parámetros disponibles, los valores predeterminados y los intervalos recomendados. Puede usar estos parámetros para acelerar el proceso (usar más proceso) o ralentizar el proceso (usar menos proceso). Por ejemplo, podría ejecutar el trabajo de reindexación a una hora de poco tráfico y aumentar el proceso para que se haga más rápido. En su lugar, podría usar la configuración para garantizar un uso bajo del proceso y hacer que se ejecute durante días en segundo plano. 

| **Parámetro**                     | **Descripción**              | **Valor predeterminado**        | **Intervalo disponible**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | Retraso entre cada lote de recursos que se va a iniciar durante el trabajo de reindexación. Un número menor acelerará el trabajo, mientras que un número mayor lo ralentizará. | 500 MS (0,5 segundos) | 50-500000 |
| MaximumResourcesPerQuery  | Número máximo de recursos incluidos en el lote de recursos que se van a volver a indexar.  | 100 | 1-5000 |
| MaximumConcurrency  | Número de lotes que se realizan a la vez.  | 1 | 1-10 |
| targetDataStoreUsagePercentage | Permite especificar qué porcentaje del almacén de datos se va a usar para el trabajo de reindexación. Por ejemplo, podría especificar el 50 % y eso garantizaría que el trabajo de reindexación usaría, como máximo, el 50 % de las RU disponibles en Cosmos DB.  | No está presente, lo que significa que se puede usar hasta el 100 %. | 0-100 |

Si desea usar cualquiera de los parámetros anteriores, puede pasarlos al recurso Parameters al iniciar el trabajo de reindexación.

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "maximumConcurrency",
      "valueInteger": "3"
    },
    {
      "name": "targetDataStoreUsagePercentage",
      "valueInteger": "20"
    },
    {
      "name": "queryDelayIntervalInMilliseconds",
      "valueInteger": "1000"
    },
    {
      "name": "maximumNumberOfResourcesPerQuery",
      "valueInteger": "1"
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo aprendió a iniciar un trabajo de reindexación. Si desea aprender a definir parámetros de búsqueda nuevos que requieran el trabajo de reindexación, consulte 

>[!div class="nextstepaction"]
>[Definición de parámetros de búsqueda personalizados](how-to-do-custom-search.md)
