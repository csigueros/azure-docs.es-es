---
title: Uso de Azure PowerShell para configurar la carga de archivos | Microsoft Docs
description: Cómo usar los cmdlets de Azure PowerShell para configurar su centro de IoT para habilitar cargas de archivos desde dispositivos conectados. Incluye información sobre cómo configurar la cuenta de Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd108bfd61a84e4c25b1ab59e9f24e23048ada2d
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015623"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configuración de cargas de archivos de IoT Hub mediante PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

En este artículo se muestra cómo configurar cargas de archivos en el centro de IoT mediante PowerShell. 

Para usar la [funcionalidad de carga de archivos en IoT Hub](iot-hub-devguide-file-upload.md), primero debe asociar una cuenta de Azure Storage y un contenedor de blobs al centro de IoT. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos. Además de la cuenta de almacenamiento y el contenedor de blobs, puede establecer el período de vida del URI de SAS y configurar las opciones para las notificaciones opcionales de carga de archivos que IoT Hub puede entregar a los servicios de back-end.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.

* Un centro de Azure IoT. Si no dispone de un centro de IoT, puede usar el [cmdlet New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) para crear uno o usar el portal para [crear un centro de IoT](iot-hub-create-through-portal.md).

* Una cuenta de almacenamiento de Azure. Si no tiene una cuenta de almacenamiento de Azure, puede usar los [cmdlets de PowerShell de Azure Storage](/powershell/module/az.storage/) para crear una o usar el portal para [crear una cuenta de almacenamiento](../storage/common/storage-account-create.md).

* Use el entorno PowerShell en [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md).

   [![Iniciar Cloud Shell en una nueva ventana](./media/iot-hub-configure-file-upload-powershell/hdi-launch-cloud-shell.png)](https://shell.azure.com)

* Si lo prefiere, [instale](/powershell/scripting/install/installing-powershell) PowerShell en el entorno local.

  * [Instale el módulo Azure Az de PowerShell](/powershell/azure/install-az-ps). (El módulo se instala de forma predeterminada en el entorno PowerShell de Azure Cloud Shell). 
  * Inicie sesión en PowerShell con el comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).  Siga los pasos que se muestran en el terminal para completar el proceso de autenticación.  Para obtener otras opciones de inicio de sesión, vea [Inicio de sesión con Azure PowerShell](/powershell/azure/authenticate-azureps).


## <a name="sign-in-and-set-your-azure-account"></a>Inicio de sesión y configuración de la cuenta de Azure

Inicie sesión en la cuenta de Azure y seleccione su suscripción. Si usa Azure Cloud Shell, ya debería haber iniciado sesión; pero es posible que todavía tenga que seleccionar la suscripción de Azure si tiene varias suscripciones.

1. En el símbolo del sistema de PowerShell, ejecute el cmdlet **Connect-AzAccount**:

    ```powershell
    Connect-AzAccount
    ```

2. Si tiene varias suscripciones de Azure, el inicio de sesión en Azure le concede acceso a todas las suscripciones de Azure asociadas a sus credenciales. Use el comando [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) para mostrar las suscripciones de Azure que están disponibles para su uso:

    ```powershell
    Get-AzSubscription
    ```

    Use el siguiente comando para seleccionar la suscripción que desea usar para ejecutar los comandos para administrar su centro de IoT. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

    ```powershell
    Select-AzSubscription `
        -Name "{your subscription name}"
    ```

    > [!NOTE]
    > El comando **Select-AzSubscription** es un alias de [Select-AzContext](/powershell/module/az.accounts/select-azcontext) que permite usar el nombre de la suscripción (**Name**) o el id. (**Id**) devuelto por el comando **Get-AzSubscription** en lugar del nombre de contexto más complejo necesario para el comando **Select-AzContext**.

## <a name="retrieve-your-storage-account-details"></a>Recuperación de los detalles de la cuenta de almacenamiento

En los siguientes pasos se supone que ha creado la cuenta de almacenamiento mediante el modelo de implementación de **Resource Manager**, y no el modelo **clásico**.

Para configurar cargas de archivos desde sus dispositivos, necesitará la cadena de conexión de una cuenta de almacenamiento de Azure. Esta cuenta debe encontrarse en la misma suscripción que IoT Hub. También necesitará el nombre de un contenedor de blobs de la cuenta de almacenamiento. Use el comando [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) para recuperar las claves de la cuenta de almacenamiento:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anote el valor de la clave de almacenamiento **key1**. La necesitará en los pasos siguientes.

Puede usar un contenedor de blobs existente para sus cargas de archivos o crear uno nuevo:

* Para mostrar los contenedores de blobs existentes en la cuenta de almacenamiento, use los comandos [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) y [Get-AzStorageContainer](/powershell/module/az.storage/get-azstoragecontainer):

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Para crear un contenedor de blobs en la cuenta de almacenamiento, use los comandos [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) y [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer):

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configuración del centro de IoT

Ahora puede configurar el centro de IoT para [cargar archivos en el centro de IoT](iot-hub-devguide-file-upload.md) con los datos de su cuenta de almacenamiento.

La configuración requiere los siguientes valores:

* **Contenedor de almacenamiento:** : un contenedor de blobs en una cuenta de Azure Storage en la suscripción actual para asociar con IoT Hub. En la sección anterior, recuperó la información necesaria de la cuenta de almacenamiento. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos.

* **Receive notifications for uploaded files** (Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos.

* **SAS TTL**(TTL SAS): este valor es el periodo de vida de los URI de SAS que Azure IoT Hub devuelve al dispositivo. De forma predeterminada, está establecido en una hora.

* **File notification settings default TTL** (TTL predeterminado de configuración de notificación de archivos): el periodo de vida de una notificación de carga de archivos antes de que caduque. De forma predeterminada, está establecido en un día.

* **File notification maximum delivery count**(Número máximo de entregas de notificaciones de archivo): el número de veces que Azure IoT Hub tratará de entregar una notificación de carga de archivos. De forma predeterminada, está establecido en 10.

Use el comando [Set-AzIotHub](/powershell/module/az.iothub/set-aziothub) para configurar la carga de archivos en el centro de IoT:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

> [!NOTE]
> De forma predeterminada, IoT Hub se autentica con Azure Storage mediante la clave de cuenta en la cadena de conexión. También está disponible la autenticación mediante identidades administradas asignadas por el sistema o por el usuario. Las identidades administradas proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure AD de manera segura. Para obtener más información, vea [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md). Actualmente no hay ningún parámetro en el comando **Set-AzIotHub** para establecer el tipo de autenticación. En su lugar, puede usar [Azure Portal](./iot-hub-configure-file-upload.md) o la [CLI de Azure](./iot-hub-configure-file-upload-cli.md). 

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la carga de archivos desde un dispositivo](iot-hub-devguide-file-upload.md)
* [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md)
* [Guías paso a paso de carga de archivos](./iot-hub-csharp-csharp-file-upload.md)