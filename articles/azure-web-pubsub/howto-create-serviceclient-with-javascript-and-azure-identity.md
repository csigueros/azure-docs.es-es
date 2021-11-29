---
title: Creación de un WebPubSubServiceClient con JavaScript y Azure Identity
description: Creación de un WebPubSubServiceClient con JavaScript y Azure Identity
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 9d32f3b7b56fffacf811c9ba1860dec34af917ec
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725365"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-javascript-and-azure-identity"></a>Creación de un `WebPubSubServiceClient` con JavaScript y Azure Identity

En esta guía paso a paso se muestra cómo crear un `WebPubSubServiceClient` con Azure Active Directory en JavaScript.

## <a name="requirements"></a>Requisitos

- Instale el paquete [@azure/identity](https://www.npmjs.com/package/@azure/identity) desde npmjs.com.

  ```bash
  npm install --save @azure/identity
  ```

- Instale el paquete [@azure/web-pubsub](https://www.npmjs.com/package/@azure/web-pubsub) desde npmjs.com.

  ```bash
  npm install @azure/web-pubsub
  ```

## <a name="sample-codes"></a>Códigos de ejemplo

1. Cree un `TokenCredential` con el SDK de Azure Identity.

    ```javascript
    const { DefaultAzureCredential } = require('@azure/identity')

    let credential = new DefaultAzureCredential();
    ```

    `credential` puede ser cualquier clase que se herede de la clase `TokenCredential`.

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    Para más información, consulte [Biblioteca cliente de Azure Identity para JavaScript](/javascript/api/overview/azure/identity-readme).

2. Después, cree un `client` con `endpoint`, `hub` y `credential`. 

    ```javascript
    const { DefaultAzureCredential } = require('@azure/identity')

    let credential = new DefaultAzureCredential();

    let serviceClient = new WebPubSubServiceClient("<endpoint>", credential, "<hub>");
    ```

    Para aprender a usar este cliente, consulte [Biblioteca cliente del servicio Azure Web PubSub para JavaScript](/javascript/api/overview/azure/web-pubsub-readme).

## <a name="complete-sample"></a>Ejemplo completo

- [Sala de chat sencilla con autenticación de AAD](https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp-aad)