---
title: 'Referencia: SDK del servidor de Python para el servicio Azure Web PubSub'
description: En la referencia se describe el SDK del servidor de Python para el servicio Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: c394258a49e73055f6848eed87dd15e75bd1c069
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114390"
---
# <a name="python-server-sdk-for-azure-web-pubsub-service"></a>SDK del servidor de Python para el servicio Azure Web PubSub

Use la biblioteca para:

- Enviar mensajes a centros y grupos.
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

[Código fuente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Paquete (Pypi)][package] | [Documentación de referencia de API](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Documentación del producto][webpubsubservice_docs] |
[Ejemplos][samples_ref]

## <a name="getting-started"></a>Introducción

### <a name="installations-the-package"></a>Instalación del paquete

```bash
python -m pip install azure-messaging-webpubsubservice
```

#### <a name="prerequisites"></a>Requisitos previos

- Se requiere Python 2.7, 3.6 o posterior para usar este paquete.
- Se necesitan una [suscripción de Azure][azure_sub] y una [instancia de servicio Azure Web PubSub][webpubsubservice_docs] para usar este paquete.
- Una instancia de servicio Azure Web PubSub existente.

### <a name="authenticating-the-client"></a>Autenticación del cliente

Para interactuar con el servicio Azure Web PubSub, tiene que crear una instancia de la clase [WebPubSubServiceClient][webpubsubservice_client_class]. Para autenticarse en el servicio, debe pasar una instancia de AzureKeyCredential con punto de conexión y clave de acceso. El punto de conexión y la clave de acceso se pueden encontrar en Azure Portal.

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>Ejemplos

### <a name="sending-a-request"></a>Envío de una solicitud

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>Conceptos clave

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>Solución de problemas

### <a name="logging"></a>Registro

Este SDK usa la biblioteca de registro estándar de Python.
Puede configurar la información de depuración de impresión de registro en stdout o cualquier ubicación que quiera.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Los detalles de solicitud y respuesta HTTP se imprimen en stdout con esta configuración de registro.

[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
