---
title: Recurso compartido de archivos de Azure para grupos de Azure Batch
description: Cómo montar un recurso compartido de archivos de Azure a partir de nodos de proceso en un grupo de Linux o Windows en Azure Batch.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 2c5cbf1a3e5d74927ddc74c4838c31f68a348876
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772119"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Uso de un recurso compartido de archivos con un grupo de Batch

[Azure Files](../storage/files/storage-files-introduction.md) ofrece recursos compartidos de archivos completamente administrados en la nube, a los que se puede acceder mediante el protocolo de Bloque de mensajes del servidor (SMB). Puede montar y usar un recurso compartido de archivos de Azure en nodos de ejecución de un grupo de Batch.

## <a name="considerations-for-use-with-batch"></a>Consideraciones sobre el uso con Batch

Podría usar un recurso compartido de archivos de Azure cuando tiene grupos que ejecutan un número de tareas en paralelo relativamente bajo si usa Azure Files no Premium. Revise los [objetivos de rendimiento y escala](../storage/files/storage-files-scale-targets.md) para determinar si se debe usar Azure Files (que usa una cuenta de Azure Storage), dado el tamaño esperado del grupo y el número de archivos de recursos.

Los recursos compartidos de archivos de Azure son [rentables](https://azure.microsoft.com/pricing/details/storage/files/) y se pueden configurar con replicación de datos en otra región para tener redundancia global.

Se puede montar al mismo tiempo un recurso compartido de archivos de Azure desde un equipo local. Pero asegúrese de comprender las [implicaciones de simultaneidad](../storage/blobs/concurrency-manage.md), especialmente al usar API REST.

Consulte también las [consideraciones de planeación](../storage/files/storage-files-planning.md) sobre los recursos compartidos de archivos de Azure.

## <a name="create-a-file-share"></a>Creación de un recurso compartido de archivos

Puede crear un recurso compartido de archivos de Azure en una cuenta de almacenamiento que esté vinculada a la cuenta de Batch, o en una cuenta de almacenamiento independiente. Para más información, consulte [Creación de un recurso compartido de archivos de Azure](../storage/files/storage-how-to-create-file-share.md).

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Montaje de un recurso compartido de archivos de Azure en un grupo de Batch

Para obtener detalles sobre cómo montar un recurso compartido de archivos de Azure en un grupo, vea [Montaje de un sistema de archivos virtual en un grupo de Batch](virtual-file-mount.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre otras opciones de lectura y escritura de datos en Batch, vea [Trabajo persistente y resultado de la tarea](batch-task-output.md).
- Explore el kit de herramientas [Batch Shipyard](https://github.com/Azure/batch-shipyard), que incluye [recetas de Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) para implementar sistemas de archivos en cargas de trabajo de contenedor de Batch.
