---
title: 'Inicio rápido: Uso de cURL y REST para administrar knowledge base: respuesta a preguntas personalizada'
description: En este inicio rápido se muestra cómo crear, publicar y consultar la base de conocimiento mediante las API REST.
ms.date: 11/02/2021
ms.topic: include
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2768081da14be2c481093d54c833f1cde45e36a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030550"
---
## <a name="prerequisites"></a>Prerrequisitos

* La versión actual de [cURL](https://curl.haxx.se/). En las guías de inicio rápido se usan varios modificadores de línea de comandos, que se indican en la [documentación de cURL](https://curl.haxx.se/docs/manpage.html).
* Las preguntas y respuestas personalizadas requieren un [recurso de idioma](../../../qnamaker/how-to/set-up-qnamaker-service-azure.md?tabs=v2#create-a-new-qna-maker-service) con la característica de respuesta a preguntas personalizada habilitada para generar un punto de conexión y una clave de API. El **Nombre** que eligió al crear el recurso se usa como subdominio para el punto de conexión. Para recuperar la clave y el nombre del recurso, seleccione **Inicio rápido** para el recurso en Azure Portal. El nombre del recurso es el primer subdominio de la dirección URL del punto de conexión: <!--TODO: Change link-->

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2`

> [!CAUTION]
> En los siguientes ejemplos de BASH se usa el carácter de continuación de línea `\`. Si la consola o el terminal usan un carácter de continuación de línea diferente, use este carácter.

## <a name="create-a-knowledge-base"></a>Creación de una base de conocimientos

Para crear una base de conocimiento con las API REST y cURL, debe tener la siguiente información:

|Information|Configuración de cURL|Propósito|
|--|--|--|
|Nombre del recurso de idioma (característica Respuesta a preguntas personalizada habilitada)|URL|Se usa para construir la dirección URL|
|Clave del recurso de idioma|Parámetro `-h` para el encabezado `Ocp-Apim-Subscription-Key`|Autenticación en el servicio de lenguaje|
|JSON que describe la base de conocimiento|Parámetro `-d`|[Ejemplos](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Tamaño del archivo JSON en bytes|Parámetro `-h` para el encabezado `Content-Size`||

El comando de cURL se ejecuta desde un shell de BASH. Edite este comando con su propio nombre de recurso, clave de recurso y valores y tamaño de JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

La respuesta de cURL de la respuesta a preguntas personalizada incluye `operationId`, que se necesita para [obtener el estado de la operación](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Obtención del estado de la operación

Al crear una base de conocimiento, dado que la operación es asincrónica, la respuesta incluye información para determinar el estado.

|Information|Configuración de cURL|Propósito|
|--|--|--|
|Nombre del recurso de idioma (característica Respuesta a preguntas personalizada habilitada)|URL|Se usa para construir la dirección URL|
|Identificador de la operación|Ruta de dirección URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Clave del recurso de idioma|Parámetro `-h` para el encabezado `Ocp-Apim-Subscription-Key`|Autenticación en el servicio de lenguaje|

El comando de cURL se ejecuta desde un shell de BASH. Edite este comando con su propio nombre de recurso, clave de recurso e identificador de operación.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

La respuesta de cURL incluye el estado. Si el estado de la operación es correcto, `resourceLocation` incluye el identificador de la base de conocimiento.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Publicación de una base de conocimiento

Antes de consultar la knowledge base, tiene que publicarla.

|Information|Configuración de cURL|Propósito|
|--|--|--|
|Nombre del recurso de idioma (característica Respuesta a preguntas personalizada habilitada)|URL|Se usa para construir la dirección URL|
|Clave del recurso de idioma|Parámetro `-h` para el encabezado `Ocp-Apim-Subscription-Key`|Autenticación en el servicio de lenguaje|
|Identificador de base de conocimiento|Ruta de dirección URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

El comando de cURL se ejecuta desde un shell de BASH. Edite este comando con su propio nombre de recurso, clave de recurso e identificador de base de conocimiento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

El estado de la respuesta es 204 sin resultados. Use el parámetro de línea de comandos `-v` para ver resultados detallados del comando de cURL. Esto incluirá el estado de HTTP.

## <a name="query-for-answer-from-published-knowledge-base"></a>Consulta para obtener respuesta de la base de conocimiento publicada

|Information|Configuración de cURL|Propósito|
|--|--|--|
|Nombre del recurso de idioma (característica Respuesta a preguntas personalizada habilitada)|URL|Se usa para construir la dirección URL|
|Clave del recurso de idioma|Parámetro `-h` para el encabezado `Ocp-Apim-Subscription-Key`|Autenticación en el servicio de lenguaje|
|Identificador de base de conocimiento|Ruta de dirección URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON que describe la consulta|Parámetro `-d`|[Parámetros del cuerpo de la solicitud](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) y [ejemplos](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Tamaño del archivo JSON en bytes|Parámetro `-h` para el encabezado `Content-Size`||

El comando de cURL se ejecuta desde un shell de BASH. Edite este comando con su propio nombre de recurso, clave de recurso e identificador de base de conocimiento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Una respuesta correcta incluye la respuesta principal junto con otra información que una aplicación cliente, como un bot de chat, necesita para mostrar una respuesta al usuario.

## <a name="delete-knowledge-base"></a>Eliminación de una base de conocimiento

Cuando haya terminado de usar la base de conocimiento, elimínela.

|Information|Configuración de cURL|Propósito|
|--|--|--|
|Nombre del recurso de idioma (característica Respuesta a preguntas personalizada habilitada)|URL|Se usa para construir la dirección URL|
|Clave del recurso de idioma|Parámetro `-h` para el encabezado `Ocp-Apim-Subscription-Key`|Autenticación en el servicio de lenguaje|
|Identificador de base de conocimiento|Ruta de dirección URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

El comando de cURL se ejecuta desde un shell de BASH. Edite este comando con su propio nombre de recurso, clave de recurso e identificador de base de conocimiento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

El estado de la respuesta es 204 sin resultados. Use el parámetro de línea de comandos `-v` para ver resultados detallados del comando de cURL. Esto incluirá el estado de HTTP.

## <a name="additional-resources"></a>Recursos adicionales

* Documentación de referencia de [creación](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)
* Documentación de referencia del [entorno de ejecución](/rest/api/cognitiveservices/qnamaker4.0/runtime)
* [Scripts de BASH de ejemplo con cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)
