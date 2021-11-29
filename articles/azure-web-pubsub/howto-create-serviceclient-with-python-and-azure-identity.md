---
title: Creación de un WebPubSubServiceClient con Python y Azure Identity
description: Creación de un WebPubSubServiceClient con Python y Azure Identity
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 17dba03200132131fc5e43cd32a3c39317c2321c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725320"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-python-and-azure-identity"></a>Creación de un `WebPubSubServiceClient` con Python y Azure Identity

En esta guía paso a paso se muestra cómo crear un `WebPubSubServiceClient` con Azure Active Directory en Python.

## <a name="requirements"></a>Requisitos

- Instale el paquete [azure-identity](https://pypi.org/project/azure-identity/) desde pypi.org.

  ```bash
  python -m pip install azure-identity
  ```

- Instale el paquete [azure-messaging-webpubsubservice](https://pypi.org/project/azure-messaging-webpubsubservice/) desde pypi.org.

  ```bash
  python -m pip install azure-messaging-webpubsubservice
  ```

## <a name="sample-codes"></a>Códigos de ejemplo

1. Cree un `TokenCredential` con el SDK de Azure Identity.

    ```python
    from azure.identity import DefaultAzureCredential

    credential = DefaultAzureCredential()
    ```

    `credential` puede ser cualquier clase que se herede de la clase `TokenCredential`.

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    Para más información, consulte [Biblioteca cliente de Azure Identity para Python](/python/api/overview/azure/identity-readme).

2. Después, cree un `client` con `endpoint`, `hub` y `credential`. 

    ```python
    from azure.identity import DefaultAzureCredential

    credential = DefaultAzureCredential()

    client = WebPubSubServiceClient(hub="<hub>", endpoint="<endpoint>", credential=credential)
    ```

    Para aprender a usar este cliente, consulte [Biblioteca cliente del servicio Azure Web PubSub para Python](/python/api/overview/azure/messaging-webpubsubservice-readme).

## <a name="complete-sample"></a>Ejemplo completo

- [Sala de chat sencilla con autenticación de AAD](https://github.com/Azure/azure-webpubsub/tree/main/samples/python/chatapp-aad)