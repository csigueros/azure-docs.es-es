---
ms.openlocfilehash: ad95a54a62c863b3c80cc372d57efbdd2adc49dc
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656967"
---
> [!NOTE]
> Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/use-managed-Identity)

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

Abra la ventana de comandos o de terminal, cree un nuevo directorio para la aplicación y navegue hasta este.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

### <a name="install-the-sdk-packages"></a>Instalación de los paquetes del SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Creación de un archivo nuevo
Abra y guarde un nuevo archivo en la carpeta creada denominada `authentication.py`, colocaremos nuestro código dentro de este archivo.

### <a name="use-the-sdk-packages"></a>Uso de los paquetes del SDK

Agregue las siguientes instrucciones `import` en la parte superior del archivo para usar los SDK que hemos instalado.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Creación de una credencial DefaultAzureCredential

Usaremos [DefaultAzureCredential.](/python/api/azure-identity/azure.identity.defaultazurecredential) Esta credencial es adecuada para entornos de producción y desarrollo. Como la usaremos en todo este inicio rápido, la crearemos en la parte superior del archivo.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>Creación de una identidad y emisión de un token con entidades de servicio

Ahora agregaremos código que usa la credencial creada para emitir un token de acceso VoIP. Llamaremos a este código más adelante:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-service-principals"></a>Envío de un SMS con entidades de servicio
Como otro ejemplo de uso de entidades de servicio, agregaremos este código que usa la misma credencial para enviar un SMS:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Escritura del código principal

Con las funciones creadas, podemos escribir el código principal que llamará a las funciones que hemos escrito anteriormente.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

El archivo `authentication.py` final debe tener un aspecto similar al siguiente:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Ejecución del programa

Una vez completado todo, puede ejecutar el archivo si especifica `python authentication.py` desde el directorio del proyecto. Si todo es correcto, debe ver algo parecido a lo siguiente.

```Bash
    $ python authentication.py
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
