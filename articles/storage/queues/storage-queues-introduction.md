---
title: Introducción a Azure Queue Storage | Azure Storage
description: Vea una introducción a Azure Queue Storage, un servicio que permite almacenar grandes cantidades de mensajes. Un servicio Queue Storage contiene un formato de dirección URL, una cuenta de almacenamiento, una cola y un mensaje.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 1da3781b3dac4b79d8c8dc49decc669bfa7efde6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551743"
---
# <a name="what-is-azure-queue-storage"></a>¿Qué es Azure Queue Storage?

Azure Queue Storage es un servicio para almacenar grandes cantidades de mensajes, a los que se puede acceder desde cualquier lugar del mundo a través de llamadas autenticadas mediante HTTP o HTTPS. Un mensaje de la cola puede llegar a tener hasta 64 KB. Una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Las colas se utilizan normalmente para crear un trabajo pendiente del trabajo que se va a procesar de forma asincrónica.

## <a name="queue-storage-concepts"></a>Conceptos de Queue Storage

Queue Storage contiene los siguientes componentes:

![Diagrama que muestra la relación entre una cuenta de almacenamiento, las colas y los mensajes.](./media/storage-queues-introduction/queue1.png)

- **Formato URL:** las colas son direccionables mediante el formato de dirección URL siguiente:

  `https://<storage account>.queue.core.windows.net/<queue>`

  La siguiente dirección URL dirige a una cola del diagrama:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Cuenta de almacenamiento**: Todo el acceso a Azure Storage se realiza a través de una cuenta de almacenamiento. Para más información sobre la capacidad de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento para cuentas de almacenamiento estándar](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Cola:** una cola contiene un conjunto de mensajes. El nombre de la cola **debe** escribirse en minúsculas. Para información sobre la asignación de nombres a colas, consulte [Asignación de nombres a colas y metadatos](/rest/api/storageservices/naming-queues-and-metadata).

- **Mensaje:** un mensaje, en cualquier formato, de hasta 64 KB. Antes de la versión 2017-07-29, el máximo tiempo de vida permitido es de siete días. A partir de la versión del 2017-07-29, inclusive, el tiempo de vida máximo puede ser cualquier número positivo o -1, lo que indica que el mensaje no expira. Si se omite este parámetro, el tiempo de vida predeterminado es siete días.

## <a name="next-steps"></a>Pasos siguientes

- [Cree una cuenta de almacenamiento](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Introducción a Queue Storage mediante .NET](storage-dotnet-how-to-use-queues.md)
- [Introducción a Queue Storage mediante Java](storage-java-how-to-use-queue-storage.md)
- [Introducción a Queue Storage mediante Python](storage-python-how-to-use-queue-storage.md)
- [Introducción a Queue Storage mediante Node.js](storage-nodejs-how-to-use-queues.md)
