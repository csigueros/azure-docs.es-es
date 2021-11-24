---
title: 'Referencia: SDK del servidor de Python para Azure Web PubSub'
description: En esta referencia se describe el SDK del servidor de Python para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 1345eec1d0868e602ba90624cd6dcc6c8635f68c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705140"
---
# <a name="azure-web-pubsub-service-client-library-for-python"></a>Biblioteca cliente del servicio Azure Web PubSub para Python

[Azure Web PubSub](./index.yml) es un servicio administrado de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real y patrón de publicación-suscripción. Cualquier escenario que requiera mensajería de publicación y suscripción en tiempo real entre el servidor y los clientes o entre clientes, puede usar el servicio Azure Web PubSub. Las características tradicionales en tiempo real que a menudo requieren tener que sondear desde el servidor o enviar solicitudes HTTP, también pueden usar el servicio Azure Web PubSub.

Puede usar esta biblioteca en el lado del servidor de aplicaciones para administrar las conexiones de cliente de WebSocket, como se muestra en el diagrama siguiente:

![El diagrama de desbordamiento muestra el desbordamiento que supone el uso de la biblioteca cliente del servicio.](media/sdk-reference/service-client-overflow.png)

Use esta biblioteca para:
- Enviar mensajes a centros y grupos.
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

[Código fuente](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [Paquete (Pypi)][package] | [Documentación de referencia de API](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice) | [Documentación del producto][webpubsubservice_docs]

## <a name="_disclaimer_"></a>_Declinación de responsabilidades_

_La compatibilidad de paquetes de Python del SDK de Azure para Python 2.7 finaliza el 1 de enero de 2022. Para obtener más información y preguntas, consulte https://github.com/Azure/azure-sdk-for-python/issues/20691_

## <a name="getting-started"></a>Introducción

### <a name="prerequisites"></a>Requisitos previos

- Se requiere Python 2.7, 3.6 o posterior para usar este paquete.
- Se necesitan una [suscripción de Azure][azure_sub] y una [instancia de servicio Azure Web PubSub][webpubsubservice_docs] para usar este paquete.
- Una instancia existente del servicio Azure Web PubSub.

### <a name="1-install-the-package"></a>1. Instale el paquete

```bash
python -m pip install azure-messaging-webpubsubservice
```

### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Cree y autentique de WebPubSubServiceClient.

Puede autenticar `WebPubSubServiceClient` mediante una [cadena de conexión][connection_string]:

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string(connection_string='<connection_string>', hub='hub')
```

Mediante el punto de conexión de servicio y la clave de acceso:

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential

>>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=AzureKeyCredential("<access_key>"))
```

O mediante [Azure Active Directory][aad_doc]:
1. [pip][pip] install [`azure-identity`][azure_identity_pip]
2. Siga el documento para [habilitar la autenticación AAD en el recurso Webpubsub][aad_doc].
3. Actualice el código para usar [DefaultAzureCredential][default_azure_credential].

    ```python
    >>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
    >>> from azure.identity import DefaultAzureCredential
    >>> service = WebPubSubServiceClient(endpoint='<endpoint>', hub='hub', credential=DefaultAzureCredential())
    ```

## <a name="key-concepts"></a>Conceptos clave

### <a name="connection"></a>Conexión

Una conexión, también conocida como "cliente" o "conexión de cliente", representa una conexión WebSocket individual conectada al servicio Web PubSub. Cuando se conecta correctamente, el servicio Web PubSub asigna un identificador de conexión único a esta conexión.

### <a name="hub"></a>Hub

El centro de conectividad es un concepto lógico para un conjunto de conexiones de clientes. Normalmente se usa un centro de conectividad para una finalidad, por ejemplo, un centro de conectividad de chats o uno de notificaciones. Cuando se establece una conexión de cliente, esta se realiza con un centro de conectividad y, durante su vigencia, pertenece a dicho centro. Diferentes aplicaciones pueden compartir un servicio de Azure Web PubSub mediante el uso de nombres de centros de conectividad diferentes.

### <a name="group"></a>Grupo

El grupo es un subconjunto de conexiones al centro de conectividad. Puede agregar una conexión de cliente a un grupo o quitarla de este cuando quiera. Por ejemplo, cuando un cliente se une a una sala de chat o sale de ella, dicha sala puede considerarse un grupo. Un cliente puede unirse a varios grupos, y un grupo puede contener varios clientes.

### <a name="user"></a>Usuario

Las conexiones a Web PubSub pueden pertenecer a un usuario. Un usuario puede tener varias conexiones, por ejemplo, cuando está conectado a través de varios dispositivos o distintas pestañas del explorador.

### <a name="message"></a>Message

Cuando el cliente está conectado, puede enviar mensajes a la aplicación de nivel superior o recibir mensajes de esta, mediante la conexión WebSocket.

## <a name="examples"></a>Ejemplos

### <a name="broadcast-messages-in-json-format"></a>Difusión de mensajes en formato JSON

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = {
        'from': 'user1',
        'data': 'Hello world'
    })
```

El cliente de WebSocket recibirá texto serializado JSON: `{"from": "user1", "data": "Hello world"}`.

### <a name="broadcast-messages-in-plain-text-format"></a>Difusión de mensajes en formato de texto sin formato

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub1')
>>> service.send_to_all(message = 'Hello world', content_type='text/plain')
```

El cliente de WebSocket recibirá texto: `Hello world`.

### <a name="broadcast-messages-in-binary-format"></a>Difusión de mensajes en formato binario

```python
>>> import io
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> service = WebPubSubServiceClient.from_connection_string('<connection_string>', hub='hub')
>>> service.send_to_all(message=io.StringIO('Hello World'), content_type='application/octet-stream')
```
El cliente de WebSocket recibirá texto binario: `b'Hello world'`.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="logging"></a>Registro

Este SDK usa la biblioteca de registro estándar de Python.
Puede configurar la información de depuración de impresión de registro en stdout o cualquier ubicación que quiera.

```python
import sys
import logging
from azure.identity import DefaultAzureCredential
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "<endpoint>"
credential = DefaultAzureCredential()

# This WebPubSubServiceClient will log detailed information about its HTTP sessions, at DEBUG level
service = WebPubSubServiceClient(endpoint=endpoint, hub='hub', credential=credential, logging_enable=True)
```

Igualmente, `logging_enable` puede habilitar el registro detallado de una sola llamada, aunque no esté habilitado para el cliente WebPubSubServiceClient:

```python
result = service.send_to_all(..., logging_enable=True)
```

Los detalles de solicitud y respuesta HTTP se imprimen en stdout con esta configuración de registro.

## <a name="next-steps"></a>Pasos siguientes

Encuentre [más ejemplos aquí][samples].

## <a name="contributing"></a>Contribuciones

Este proyecto agradece las contribuciones y sugerencias. La mayoría de las contribuciones requieren que acepte un Contrato de licencia para el colaborador (CLA) que declara que tiene el derecho a concedernos y nos concede los derechos para usar su contribución.
Para más detalles, visite https://cla.microsoft.com.

Cuando se envía una solicitud de incorporación de cambios, un bot de CLA determinará de forma automática si tiene que aportar un CLA y completar la PR adecuadamente (por ejemplo, la etiqueta, el comentario). Solo siga las instrucciones que le dará el bot. Solo será necesario que lo haga una vez en todos los repositorios con nuestro CLA.

Este proyecto ha adoptado el [Código de conducta de Microsoft Open Source][code_of_conduct]. Para obtener más información, vea las preguntas más frecuentes sobre el código de conducta o póngase en contacto con opencode@microsoft.com si tiene preguntas o comentarios.

<!-- LINKS -->
[webpubsubservice_docs]: ./index.yml
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[cla]: https://cla.microsoft.com
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[azure_identity_credentials]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#credentials
[azure_identity_pip]: https://pypi.org/project/azure-identity/
[default_azure_credential]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#defaultazurecredential
[pip]: https://pypi.org/project/pip/
[enable_aad]: howto-develop-create-instance.md
[api_key]: howto-websocket-connect.md#authorization
[connection_string]: howto-websocket-connect.md#authorization
[azure_portal]: howto-develop-create-instance.md
[azure-key-credential]: https://aka.ms/azsdk-python-core-azurekeycredential
[aad_doc]: howto-authorize-from-application.md
[samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/webpubsub/azure-messaging-webpubsubservice/samples