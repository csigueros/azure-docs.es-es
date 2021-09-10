---
title: Modificación de la topología de Azure File Sync | Microsoft Docs
description: Instrucciones sobre cómo modificar la topología de sincronización de Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 51fc737284e62d1efd3a947e29ea71a4bb2594a0
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768657"
---
# <a name="modify-your-azure-file-sync-topology"></a>Modificación de la topología de Azure File Sync

En este artículo se tratan las formas más comunes en las que a los clientes les gustaría modificar la topología de Azure File Sync y nuestras recomendaciones sobre cómo hacerlo. Si quiere modificar la topología de Azure File Sync, consulte los procedimientos recomendados siguientes para evitar errores o posibles pérdidas de datos.

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>Migración de un punto de conexión de servidor a un servicio de sincronización de almacenamiento de Azure File Sync diferente

Una vez que se haya asegurado de que los datos están actualizados en el servidor local, desaprovisione el punto de conexión de servidor. Para instrucciones detalladas sobre cómo hacerlo, consulte [Desaprovisionamiento del punto de conexión de servidor de Azure File Sync](./file-sync-server-endpoint-delete.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share). A continuación, vuelva a aprovisionar en el grupo de sincronización deseado y el servicio de sincronización de almacenamiento.

Si desea migrar todos los puntos de conexión de servidor asociados a un servidor a otro grupo de sincronización o servicio de sincronización de almacenamiento, consulte [Desaprovisionamiento de todos los puntos de conexión de servidor asociados a un servidor registrado](#deprovision-all-server-endpoints-associated-with-a-registered-server).

## <a name="change-the-granularity-of-a-server-endpoint"></a>Cambio de la granularidad de un punto de conexión de servidor

Después de confirmar que los datos están actualizados en el servidor local (consulte [Desaprovisionamiento del punto de conexión de servidor de Azure File Sync](./file-sync-server-endpoint-delete.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)), desaprovisione el punto de conexión de servidor. A continuación, vuelva a aprovisionarlo con la granularidad deseada.

## <a name="deprovision-azure-file-sync-topology"></a>Desaprovisionamiento de la topología de Azure File Sync

Los recursos de Azure File Sync deben desaprovisionarse en un orden específico: puntos de conexión de servidor, grupo de sincronización y, a continuación, servicio de almacenamiento. Aunque todo el flujo se documenta a continuación, puede detenerse en cualquier nivel que desee. 

En primer lugar, vaya al recurso de servicio de sincronización de almacenamiento en Azure Portal y seleccione un grupo de sincronización en el servicio de sincronización de almacenamiento. Siga los pasos descritos en [Desaprovisionamiento del punto de conexión de servidor de Azure File Sync](./file-sync-server-endpoint-delete.md) para garantizar la integridad y disponibilidad de los datos al eliminar los puntos de conexión de servidor. Para desaprovisionar el grupo de sincronización o el servicio de sincronización de almacenamiento, se deben eliminar todos los puntos de conexión de servidor. Si solo tiene como objetivo eliminar puntos de conexión de servidor específicos, puede detenerse aquí. 

Una vez que elimine todos los puntos de conexión de servidor del grupo de sincronización, elimine el punto de conexión de nube. 

A continuación, elimine el grupo de sincronización. 

Repita estos pasos para todos los grupos de sincronización del servicio de sincronización de almacenamiento que desea eliminar. Una vez eliminados todos los grupos de sincronización de ese servicio de sincronización de almacenamiento, elimine el recurso de servicio de sincronización de almacenamiento.

## <a name="change-a-server-endpoint-path"></a>Cambio de la ruta de acceso de punto de conexión de servidor

Una ruta de acceso de punto de conexión de servidor es una propiedad inmutable. Elegir una ubicación diferente en el servidor tiene consecuencias para los datos de la ubicación anterior, el recurso compartido de archivos de Azure y la nueva ubicación. La mayoría de estos comportamientos no están definidos si simplemente se va a cambiar la ruta de acceso. Solo puede quitar un punto de conexión de servidor y, luego, crear otro con la nueva ruta de acceso. Considere detenidamente el estado de sincronización del servidor para encontrar el momento adecuado para realizar este gran cambio.

La eliminación de un punto de conexión de servidor no es algo insignificante y puede provocar la pérdida de datos si se realiza de forma incorrecta. El [artículo sobre cómo eliminar un punto de conexión de servidor](file-sync-server-endpoint-delete.md) le guía por el proceso.

Si actualmente usa la unidad D y planea migrar a la nube, consulte [Conversión de la unidad D: de una máquina virtual en un disco de datos: Azure Virtual Machines](../../virtual-machines/windows/change-drive-letter.md).

## <a name="rename-a-sync-group"></a>Cambio de nombre de un grupo de sincronización

No se puede cambiar el nombre de un grupo de sincronización. Su nombre forma parte de la dirección URL con la que se almacenan y administran los puntos de conexión en la nube y los puntos de conexión de servidor de los recursos secundarios. Elija cuidadosamente el nombre al crear el recurso.

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>Desaprovisionamiento de todos los puntos de conexión de servidor asociados a un servidor registrado

Para asegurarse de que los datos están seguros y totalmente actualizados antes de desaprovisionar, consulte [Desaprovisionamiento del punto de conexión de servidor de Azure File Sync](./file-sync-server-endpoint-delete.md).

No se deben quitar todos los puntos de conexión de servidor de forma masiva, a menos que se trate de una implementación de prueba con datos descartables en el servidor y en la nube. Al anular el registro del servidor con el *Servicio de sincronización de almacenamiento* de Azure File Sync se eliminan de forma masiva todos los puntos de conexión de servidor. La pérdida de datos es una consecuencia probable del uso de este método. 

Para cancelar el registro de un servidor sin tener en cuenta las implicaciones negativas, vaya al recurso Servicio de sincronización de almacenamiento y desplácese hasta la pestaña **Servidores registrados**. Seleccione el servidor del que quiere anular el registro y elija **Cancelar registro de servidor**. Se quitarán todos los puntos de conexión de servidor asociados a ese servidor.

## <a name="next-steps"></a>Pasos siguientes
* [Desaprovisionamiento de un punto de conexión de servidor de Azure File Sync](./file-sync-server-endpoint-delete.md)


