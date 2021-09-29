---
title: Uso de Azure Queue Storage desde PowerShell - Azure Storage
description: Realice operaciones en Azure Queue Storage a través de PowerShell. Con Azure Queue Storage, puede almacenar grandes cantidades de mensajes que son accesibles por HTTP/HTTPS.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a607d1c494aee2881e623e01e6cd9637cb94dc9d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582295"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Uso de Azure Queue Storage desde PowerShell

Azure Queue Storage es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Para obtener más información, consulte [Introducción a Azure Queue Storage](storage-queues-introduction.md). En este artículo de ayuda se describen operaciones de Queue Storage habituales. Aprenderá a:

> [!div class="checklist"]
>
> - Creación de una cola
> - Recuperar una cola
> - Agregar un mensaje
> - Leer un mensaje
> - Eliminar un mensaje
> - Eliminación de una cola

Este artículo de ayuda requiere la versión 0.7 del módulo de Azure PowerShell (`Az`) o cualquier versión posterior. Ejecute `Get-Module -ListAvailable Az` para encontrar la versión instalada actualmente. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps).

No hay ningún cmdlet de PowerShell para el plano de datos de las colas. Para realizar operaciones de plano de datos como agregar un mensaje, leer un mensaje y eliminar un mensaje, debe usar la biblioteca del cliente de Storage de .NET como se expone en PowerShell. Cree un objeto de mensaje y después podrá usar comandos como `AddMessage` para realizar operaciones en dicho mensaje. En este artículo se explica cómo hacerlo.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción a Azure con el comando `Connect-AzAccount` y siga las instrucciones de la pantalla.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperación de la lista de ubicaciones

Si no sabe qué ubicación desea usar, puede enumerar las ubicaciones disponibles. Cuando se muestre la lista, busque la que desee usar. En este ejercicio se usará `eastus`. Guárdela en la variable `location` para usarla más adelante.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Guarde el nombre del grupo de recursos en una variable para usarlo más adelante. En este ejemplo, se crea un grupo de recursos denominado `howtoqueuesrg` en la región `eastus`.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crear cuenta de almacenamiento

Cree una cuenta de almacenamiento genérica estándar con almacenamiento con redundancia local (LRS) mediante [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Obtenga el contexto de la cuenta de almacenamiento que define la cuenta de almacenamiento que se usará. Cuando actúa en una cuenta de almacenamiento, hace referencia al contexto en lugar de proporcionar varias veces las credenciales.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Creación de una cola

En el siguiente ejemplo, primero se establece una conexión a Azure Storage mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación llama al cmdlet [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) para crear una cola llamada `howtoqueue`.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Para obtener información sobre las convenciones de nomenclatura del servicio Azure Queue Storage, consulte [Nomenclatura de colas y metadatos](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Recuperar una cola

Puede consultar y recuperar una cola específica o una lista de todas las colas de una cuenta de almacenamiento. Los ejemplos siguientes muestran cómo recuperar todas las colas de la cuenta de almacenamiento y una cola específica; ambos comandos usan el cmdlet [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>un mensaje a una cola

Las operaciones que afectan a los mensajes reales de la cola usan la biblioteca del cliente de Storage de .NET como se expone en PowerShell. Para agregar un mensaje a una cola, cree una nueva instancia del objeto de mensaje [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage). A continuación, llame al método [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage). Se puede crear un objeto `CloudQueueMessage` a partir de una cadena (en formato UTF-8) o de una matriz de bytes.

En el siguiente ejemplo le mostraremos cómo agregar un mensaje a la cola.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Si usa el [Explorador de Azure Storage](https://storageexplorer.com), puede conectarse a su cuenta de Azure, ver las colas de la cuenta de almacenamiento y explorar una de ellas para ver los mensajes de la cola.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Lectura y eliminación de un mensaje de la cola

Los mensajes se leen, en lo posible, siguiendo un orden de tipo primero en entrar, primero en salir, si bien no se ofrece ninguna garantía al respecto. Al leer el mensaje de la cola, se hace invisible para todos los demás procesos que observan la cola. De este modo, se garantiza que si su código no puede procesar el mensaje a causa de un error de hardware o software, otra instancia de su código pueda obtener el mismo mensaje e intentarlo de nuevo.

Este **tiempo de expiración de invisibilidad** define cuánto tiempo permanece invisible el mensaje antes de volver a estar disponible para procesarse. El valor predeterminado es 30 segundos.

El código lee un mensaje de la cola en dos pasos. Cuando se llama al método [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage), se obtiene el siguiente mensaje de la cola. Un mensaje devuelto por `GetMessage` se hace invisible a cualquier otro código de lectura de mensajes de esta cola. Para acabar de quitar el mensaje de la cola, también debe llamar a [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage).

En el ejemplo siguiente, leerá los tres mensajes de la cola y, después, esperará diez segundos (el tiempo de expiración de invisibilidad). A continuación, leerá los tres mensajes de nuevo, eliminando los mensajes tras leerlos mediante una llamada a `DeleteMessage`. Si intenta leer la cola después de que se eliminen los mensajes, se devolverá `$queueMessage` como `$null`.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Eliminación de una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método `Remove-AzStorageQueue`. En el siguiente ejemplo se muestra cómo eliminar la cola específica utilizada en este ejercicio mediante el cmdlet `Remove-AzStorageQueue`.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar todos los activos que ha creado en este ejercicio, quite el grupo de recursos. Esto también elimina todos los recursos contenidos en el grupo. En este caso, se quita la cuenta de almacenamiento creada y el propio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo de ayuda, ha aprendido conceptos básicos sobre la administración de Queue Storage con PowerShell. Por ejemplo:

> [!div class="checklist"]
>
> - Creación de una cola
> - Recuperar una cola
> - Agregar un mensaje
> - Leer el siguiente mensaje
> - Eliminar un mensaje
> - Eliminación de una cola

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de almacenamiento de Microsoft Azure PowerShell

- [Cmdlets de PowerShell de almacenamiento](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Microsoft Azure Storage

- El [Explorador de Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
