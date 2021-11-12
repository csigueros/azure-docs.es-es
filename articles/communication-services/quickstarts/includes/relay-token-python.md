---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/30/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 35319f125578143abc2518d8037e0050dd9a6041
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893258"
---
> [!NOTE]
> Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/relay-token-quickstart)

## <a name="prerequisites-for-python"></a>Requisitos previos para Python

 Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 o 3.6+.
- Un recurso activo de Communication Services y una cadena de conexión. [Cree un recurso de Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

1. Abra la ventana de comandos o de terminal, cree un nuevo directorio para la aplicación y navegue hasta este.

   ```console
   mkdir access-tokens-quickstart && cd relay-tokens-quickstart
   ```

2. Use un editor de texto para crear un archivo denominado **issue-relay-tokens.py** en el directorio raíz del proyecto y agregue la estructura del programa, incluido el control de excepciones básico. En las secciones siguientes agregará todo el código fuente de esta guía de inicio rápido a este archivo.

   ```python
    import os
    from azure.communication.networktraversal import CommunicationRelayClient
    from azure.identity import DefaultAzureCredential
    from azure.communication.identity import CommunicationIdentityClient
    
    try:
      print("Azure Communication Services - Access Relay Configuration  Quickstart")
      # Quickstart code goes here
    except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Instalar el paquete

En el directorio de aplicaciones, instale Identity SDK de Azure Communication Services para Python con el comando `pip install`.

```console
pip install azure-communication-identity
pip install azure.communication.networktraversal
```

## <a name="authenticate-the-client"></a>Autenticar el cliente

Cree una instancia de `CommunicationIdentityClient` con la clave de acceso y el punto de conexión del recurso. El código siguiente recupera la cadena de conexión para el recurso de una variable de entorno denominada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Aprenda a [administrar la cadena de conexión del recurso](../create-communication-resource.md#store-your-connection-string). 

Agregue este código dentro del bloque `try`:

```python

# You can find your endpoint and access token from your resource in the Azure Portal
connection_str = "endpoint=ENDPOINT;accessKey=KEY"
endpoint = "https://<RESOURCE_NAME>.communication.azure.com"

# To use Azure Active Directory Authentication (DefaultAzureCredential) make sure to have
# AZURE_TENANT_ID, AZURE_CLIENT_ID and AZURE_CLIENT_SECRET as env variables.
# We also need Identity client to get a User Identifier
identity_client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
relay_client = CommunicationRelayClient(endpoint, DefaultAzureCredential())

#You can also authenticate using your connection string
identity_client = CommunicationIdentityClient.from_connection_string(self.connection_string)
relay_client = CommunicationRelayClient.from_connection_string(self.connection_string)
```

## <a name="create-a-user-from-identity"></a>Creación de un usuario a partir de una identidad 

Azure Communication Services mantiene un directorio de identidades ligero. Use el método `create_user` para crear una nueva entrada en el directorio con un `Id` único. Almacene la identidad recibida con la asignación a los usuarios de la aplicación. Por ejemplo, almacenándolos en la base de datos del servidor de aplicaciones. La identidad es necesaria más adelante para emitir tokens de acceso.

```python
user = identity_client.create_user()
```

## <a name="getting-the-relay-configuration"></a>Obtención de la configuración de retransmisión
Llame al servicio de token de Azure Communication para intercambiar el token de acceso de usuario por un token de servicio TURN.

```python
relay_configuration = relay_client.get_relay_configuration(user)

for iceServer in config.ice_servers:
    assert iceServer.username is not None
    print('Username: ' + iceServer.username)

    assert iceServer.credential is not None
    print('Credential: ' + iceServer.credential)
    
    assert iceServer.urls is not None
    for url in iceServer.urls:
        print('Url:' + url)
```     

## <a name="run-the-code"></a>Ejecución del código

Desde un símbolo del sistema de la consola, vaya al directorio que contiene el archivo *issue-relay-tokens.py* y ejecute el siguiente comando `python` para ejecutar la aplicación.

```console
python ./issue-relay-tokens.py
```
