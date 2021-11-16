---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 60c534a64bae703de30cd7aaddd1da31d27e750b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461206"
---
## <a name="set-up-prerequisites"></a>Configuración de requisitos previos

- [Python](https://www.python.org/downloads/) 2.7 o 3.6+.

## <a name="set-up"></a>Configuración

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

1. Abra la ventana de comandos o de terminal, cree un nuevo directorio para la aplicación y navegue hasta este.

   ```console
   mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
   ```

1. Use un editor de texto para crear un archivo denominado `exchange-communication-access-tokens.py` en el directorio raíz del proyecto y agregue la estructura del programa, incluido el control de excepciones básico. En las secciones siguientes agregará todo el código fuente de esta guía de inicio rápido a este archivo.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Instalar el paquete

En el directorio de aplicaciones, instale Identity SDK de Azure Communication Services para Python con el comando `pip install`.

```console
pip install azure-communication-identity
pip install msal
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Paso 1: Recibir el token de usuario de Azure AD mediante la biblioteca MSAL

El primer paso del flujo de intercambio de tokens es obtener un token para el usuario de Teams mediante [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```python
from msal.application import PublicClientApplication

client_id = "<contoso_application_id>"
tenant_id = "<contoso_tenant_id>"
authority = "https://login.microsoftonline.com/%s" % tenant_id

app = PublicClientApplication(client_id, authority=authority)

scope = [ "https://auth.msft.communication.azure.com/VoIP" ]
teams_token_result = app.acquire_token_interactive(scope)
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Paso 2: Inicializar CommunicationIdentityClient

Cree una instancia de un objeto `CommunicationIdentityClient` con su cadena de conexión. El código siguiente recupera la cadena de conexión para el recurso de una variable de entorno denominada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Aprenda a [administrar la cadena de conexión del recurso](../create-communication-resource.md#store-your-connection-string).

Agregue este código dentro del bloque `try`:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Paso 3: Intercambiar el token de usuario de Azure AD para el token de acceso de Teams

Use el método `get_token_for_teams_user` para emitir un token de acceso para el usuario de Teams que se puede usar con los SDK de Azure Communication Services.

```python
token_result = client.get_token_for_teams_user(teams_token_result['access_token'])
print("Token: " + token_result.token)
```

## <a name="run-the-code"></a>Ejecución del código

Desde un símbolo del sistema de la consola, vaya al directorio que contiene el archivo *exchange-teams-access-tokens.py* y ejecute el siguiente comando `python` para ejecutar la aplicación.

```console
python ./exchange-communication-access-tokens.py
```
