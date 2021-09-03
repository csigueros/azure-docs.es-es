---
title: Configuración de la carga de archivos en IoT Hub con la CLI de Azure | Microsoft Docs
description: Configuración de las cargas de archivos en Azure IoT Hub con la CLI de Azure multiplataforma.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.openlocfilehash: ecaae482a0a577efd480610f25d0186502dbeb15
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735157"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configuración de cargas de archivos de IoT Hub mediante la CLI de Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

En este artículo se muestra cómo configurar cargas de archivos en el centro de IoT mediante la CLI de Azure. 

Para usar la [funcionalidad de carga de archivos en IoT Hub](iot-hub-devguide-file-upload.md), primero debe asociar una cuenta de Azure Storage y un contenedor de blobs al centro de IoT. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos. Además de la cuenta de almacenamiento y el contenedor de blobs, puede establecer el período de vida del URI de SAS y el tipo de autenticación que IoT Hub usa con Azure Storage. También puede configurar opciones para las notificaciones de carga de archivos opcionales que IoT Hub puede entregar a los servicios de back-end.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.

* Un centro de Azure IoT. Si no tiene instancias de IoT Hub, puede usar el [`az iot hub create` comando](/cli/azure/iot/hub#az_iot_hub_create) para crear una o [crear una instancia de IoT Hub mediante el portal](iot-hub-create-through-portal.md).

* Una cuenta de Azure Storage. Si no tiene ninguna cuenta de Azure Storage, puede usar la CLI de Azure para crear una. Para obtener más información, consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Inicio de sesión y configuración de la cuenta de Azure

Inicie sesión en la cuenta de Azure y seleccione su suscripción. Si usa Azure Cloud Shell, ya debería haber iniciado sesión; pero es posible que todavía tenga que seleccionar la suscripción de Azure si tiene varias suscripciones.

1. En el símbolo del sistema, ejecute el [comando de inicio de sesión](/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

    Siga las instrucciones para realizar la autenticación mediante el código e inicie sesión en la cuenta de Azure a través de un explorador web.

2. Si tiene varias suscripciones de Azure, iniciar sesión en Azure le concede acceso a todas las cuentas de Azure asociadas con las credenciales. Use el siguiente [comando para mostrar las cuentas de Azure](/cli/azure/account) que tiene disponibles para su uso:

    ```azurecli
    az account list
    ```

    Use el siguiente comando para seleccionar la suscripción que desee usar para ejecutar los comandos que crearán la instancia de IoT Hub. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Recuperación de los detalles de la cuenta de almacenamiento

En los siguientes pasos se supone que ha creado la cuenta de almacenamiento mediante el modelo de implementación de **Resource Manager**, y no el modelo **clásico**.

Para configurar cargas de archivos desde sus dispositivos, necesitará la cadena de conexión de una cuenta de almacenamiento de Azure. Esta cuenta debe encontrarse en la misma suscripción que IoT Hub. También necesitará el nombre de un contenedor de blobs de la cuenta de almacenamiento. Use el siguiente comando para recuperar las claves de la cuenta de almacenamiento:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```
La cadena de conexión será similar a la siguiente salida:

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={your storage account name};AccountKey={your storage account key}"
}
```

Anote el valor `connectionString`. La necesitará en los pasos siguientes.

Puede usar un contenedor de blobs existente para sus cargas de archivos o crear uno nuevo:

* Para mostrar los contenedores de blobs existentes en su cuenta de almacenamiento, use el siguiente comando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Para crear un contenedor de blobs en su cuenta de almacenamiento, use el siguiente comando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="configure-your-iot-hub"></a>Configuración del centro de IoT

Ahora puede configurar la instancia de IoT Hub para permitir la [carga de archivos en IoT Hub](iot-hub-devguide-file-upload.md) con los datos de la cuenta de almacenamiento.

La configuración requiere los siguientes valores:

* **Contenedor de almacenamiento:** : un contenedor de blobs en una cuenta de Azure Storage en la suscripción actual para asociar con IoT Hub. En la sección anterior, recuperó la información necesaria de la cuenta de almacenamiento. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos.

* **Receive notifications for uploaded files** (Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos.

* **SAS TTL**(TTL SAS): este valor es el periodo de vida de los URI de SAS que Azure IoT Hub devuelve al dispositivo. De forma predeterminada, está establecido en una hora.

* **File notification settings default TTL** (TTL predeterminado de configuración de notificación de archivos): el periodo de vida de una notificación de carga de archivos antes de que expire. De forma predeterminada, está establecido en un día.

* **File notification maximum delivery count**(Número máximo de entregas de notificaciones de archivo): el número de veces que Azure IoT Hub tratará de entregar una notificación de carga de archivos. De forma predeterminada, está establecido en 10.

* **File notification lock duration** (Duración del bloqueo de notificación de archivo): duración del bloqueo de la cola de notificación de archivos. Se establece en 60 segundos de forma predeterminada.

* **Tipo de autenticación**: tipo de autenticación IoT Hub que se usará con Azure Storage. Esta configuración determina cómo se autentica y autoriza el centro de IoT con Azure Storage. El valor predeterminado es la autenticación basada en claves; sin embargo, también se pueden usar identidades administradas asignadas por el sistema y asignadas por el usuario. Las identidades administradas proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure AD de manera segura. Para obtener información sobre cómo configurar identidades administradas en el centro de IoT y la cuenta de Azure Storage, consulte [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md). Una vez configurado, puede establecer una de las identidades administradas que se usará para la autenticación con Azure Storage.

    > [!NOTE]
    > La configuración del tipo de autenticación configura cómo se autentica el centro de IoT con la cuenta de Azure Storage. Los dispositivos siempre se autentican con Azure Storage mediante el URI de SAS que obtienen del centro de IoT. 


Use los siguientes comandos para mostrar cómo configurar la carga de archivos en el centro de IoT. Estos comandos se muestran por separado para mayor claridad, pero, normalmente, emitiría un único comando con todos los parámetros necesarios para su escenario. Incluya comillas cuando aparezcan en la línea de comandos. No incluya las llaves. Puede encontrar más detalles sobre cada parámetro en la documentación de la CLI de Azure del comando [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update).

El siguiente comando configura la cuenta de almacenamiento y el contenedor de blobs.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-connectionstring "{your storage account connection string}" \
    --fileupload-storage-container-name "{your container name}" 
```

El comando siguiente establece el período de vida del URI de SAS en el valor predeterminado (una hora).

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-sas-ttl 1 
```

El siguiente comando habilita las notificaciones de archivos y establece las propiedades de notificación de archivos en sus valores predeterminados. (El período de vida de la notificación de carga de archivos se establece en una hora y la duración del bloqueo se establece en 60 segundos).

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-notifications true  \
    --fileupload-notification-max-delivery-count 10 \
    --fileupload-notification-ttl 1 \
    --fileupload-notification-lock-duration 60
```

El comando siguiente configura la autenticación basada en claves:

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type keyBased
```

El comando siguiente configura la autenticación mediante la identidad administrada asignada por el sistema del centro de IoT. Para poder ejecutar este comando, debe habilitar la identidad administrada asignada por el sistema para el centro de IoT y concederle el rol RBAC correcto en la cuenta de Azure Storage. Para obtener más información, vea [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md).

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity [system] 
```

Los comandos siguientes recuperan las identidades administradas asignadas por el usuario configuradas en el centro de IoT y configuran la autenticación con una de ellas. Para poder usar una identidad administrada asignada por el usuario para autenticarse, debe configurarse en el centro de IoT y tener concedido un rol RBAC adecuado en su cuenta de Azure Storage. Para obtener más información y conocer los pasos, vea [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md).

Para consultar identidades administradas asignadas por el usuario en el centro de IoT, use el comando [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show).

```azurecli
az iot hub identity show --name {your iot hub name} --query userAssignedIdentities
```

El comando devuelve una colección de las identidades administradas asignadas por el usuario configuradas en el centro de IoT. En la salida siguiente se muestra una colección que contiene una única identidad administrada asignada por el usuario.

```json
{
  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}": 
  {
    "clientId": "<client ID GUID>",
    "principalId": "<principal ID GUID>"
  }
}
```

El comando siguiente configura la autenticación para usar la identidad asignada por el usuario anterior.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}"
```

Puede revisar la configuración en el centro de IoT mediante el siguiente comando:

```azurecli
az iot hub show --name {your iot hub name}
```

Para revisar solo la configuración de carga de archivos, use el siguiente comando:

```azurecli
az iot hub show --name {your iot hub name}
    --query '[properties.storageEndpoints, properties.enableFileUploadNotifications, properties.messagingEndpoints.fileNotifications]'
```

Para la mayoría de las situaciones, es más fácil usar los parámetros con nombre de la CLI de Azure; sin embargo, también puede configurar la carga de archivos con el parámetro `--set`. Los comandos siguientes pueden ayudarle a comprender cómo hacerlo. 

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la carga de archivos desde un dispositivo](iot-hub-devguide-file-upload.md)
* [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md)
* [Guías paso a paso de carga de archivos](./iot-hub-csharp-csharp-file-upload.md)
* Comandos [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update), [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) y [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) de la CLI de Azure
