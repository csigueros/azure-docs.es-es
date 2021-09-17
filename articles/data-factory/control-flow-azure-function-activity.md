---
title: Actividad de función de Azure
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a usar la actividad Función de Azure para ejecutar una función de Azure en una canalización de Azure Data Factory o Azure Synapse Analytics
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 4e62ea4c57e00695b2a2f969b9fd4f80f8298681
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824088"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Actividad de las funciones de Azure en Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
La actividad Función de Azure permite ejecutar [Azure Functions](../azure-functions/functions-overview.md) en una canalización de Azure Data Factory o Synapse. Para ejecutar una función de Azure, debe crear una conexión de servicio vinculado y una actividad que especifique la función de Azure que se va a ejecutar.

Si desea una introducción y demostración de ocho minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Servicio vinculado de la función de Azure


El tipo de valor devuelto de la función de Azure tiene que ser un elemento `JObject` válido. (Tenga en cuenta que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)*no* es un `JObject`.) Los tipos de valor devuelto que no sean `JObject` producen un error y generan el error de usuario *El contenido de la respuesta no es un elemento JObject válido*.

La clave de función proporciona acceso seguro al nombre de la función; cada una de ellas tiene claves únicas independientes o una clave maestra dentro de una aplicación de funciones. La identidad administrada proporciona acceso seguro a toda la aplicación de funciones. El usuario debe proporcionar la clave para acceder al nombre de la función. Vea la documentación de la función para obtener más detalles sobre la [clave de acceso a la función](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#configuration).


| **Propiedad** | **Descripción** | **Obligatorio** |
| --- | --- | --- |
| type   | La propiedad type debe establecerse en: **AzureFunction** | sí |
| Dirección URL de Function App | Dirección URL de la instancia de Azure Function App. El formato es `https://<accountname>.azurewebsites.net`. Esta dirección URL es el valor que aparece en la sección **URL** al visualizar la instancia de Function App en Azure Portal.  | sí |
| Tecla de función | Tecla de acceso de la función de Azure. Haga clic en la sección **Administrar** de la función correspondiente y copie la **tecla de función** o la **tecla del host**. Obtenga más información aquí. [Enlaces y desencadenadores HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | sí |
|   |   |   |

## <a name="azure-function-activity"></a>Actividad de función de Azure

| **Propiedad**  | **Descripción** | **Valores permitidos** | **Obligatorio** |
| --- | --- | --- | --- |
| name  | Nombre de la actividad en la canalización  | String | sí |
| type  | El tipo de actividad es “AzureFunctionActivity” | String | sí |
| Servicio vinculado | El servicio vinculado de la función de Azure para la instancia correspondiente de Azure Function App  | Referencia al servicio vinculado | sí |
| nombre de función  | Nombre de la función de la instancia de Azure Function App al que esta actividad llama | String | sí |
| method  | Método de API de REST para llamar a la función | Tipos de cadena admitidos: "GET", "POST" y "PUT"   | sí |
| header  | Encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Cadena (o expresión con un valor resultType de cadena) | No |
| body  | Cuerpo que se envía junto con la solicitud al método de API de la función  | Cadena (o expresión con un valor resultType de cadena) u objeto.   | Necesario para los métodos POST o PUT |
|   |   |   | |

Vea el esquema de la carga de solicitud en la sección [Solicitar un esquema de carga](control-flow-web-activity.md#request-payload-schema).

## <a name="routing-and-queries"></a>Enrutamiento y consultas

La actividad de la función de Azure admite **enrutamiento**. Por ejemplo, si la función de Azure tiene el punto de conexión `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, el elemento `functionName` que se va a usar en la actividad de la función de Azure es `<functionName>/<value>`. Se puede parametrizar esta función para proporcionar el elemento `functionName` que se quiera en el tiempo de ejecución.

La actividad de la función de Azure admite **consultas**. Debe incluirse una consulta como parte de `functionName`. Por ejemplo, cuando el nombre de función es `HttpTriggerCSharp` y la consulta que se quiere incluir es `name=hello`, se puede construir `functionName` en la actividad de la función de Azure como `HttpTriggerCSharp?name=hello`. Esta función se puede parametrizar, por lo que el valor se puede determinar en el tiempo de ejecución.

## <a name="timeout-and-long-running-functions"></a>Funciones de tiempo de expiración y larga duración

Azure Functions agota el tiempo de espera después de 230 segundos, independientemente del valor de `functionTimeout` que se haya configurado. Para obtener más información, consulte [este artículo](../azure-functions/functions-versions.md#timeout). Para evitar este comportamiento, siga un patrón asincrónico o use Durable Functions. La ventaja de Durable Functions es que ofrece su propio mecanismo de seguimiento de estado, por lo que no tendrá que implementar el suyo propio.

Obtenga más información sobre Durable Functions en [este artículo](../azure-functions/durable/durable-functions-overview.md). Puede configurar una actividad de la función de Azure para llamar a la función duradera, lo que devolverá una respuesta con un URI diferente, como [en este ejemplo](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Dado que `statusQueryGetUri` devuelve código de estado HTTP 202 mientras la función se ejecuta, puede sondear el estado de la función mediante una actividad web. Solo tiene que configurar una actividad web con el campo `url` establecido en `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Cuando se completa la función duradera, la salida de la función será el resultado de la actividad web.


## <a name="sample"></a>Muestra

[Aquí](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction) puede encontrar un ejemplo que usa una función de Azure para extraer el contenido de un archivo tar.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las actividades admitidas en [Canalizaciones y actividades](concepts-pipelines-activities.md).
