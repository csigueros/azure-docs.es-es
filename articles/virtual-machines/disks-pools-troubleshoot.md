---
title: Introducción a la solución de problemas de los grupos de discos de Azure (versión preliminar)
description: Solucione los problemas con los grupos de discos de Azure. Obtenga información sobre los códigos de error comunes y cómo resolverlos.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6dc1b5ae3e885231a0d80ade2b8477a66e85d62d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456246"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Solución de problemas de los grupos de discos de Azure (versión preliminar)

En este artículo, se enumeran algunos códigos de error comunes relacionados con los grupos de discos de Azure (versión preliminar). También proporciona posibles resoluciones y ciertas aclaraciones sobre los estados del grupo de discos.

## <a name="disk-pool-status"></a>Estado del grupo de discos

Los grupos de discos y los destinos iSCSI tienen cuatro estados: **Desconocido**, **En ejecución**, **Actualizando** y **Detenido (desasignado)** .

**Desconocido** significa que el recurso está en un estado incorrecto o desconocido. Para intentar la recuperación, realice una operación de actualización en el recurso (por ejemplo, agregar o quitar discos o LUN) o elimine y vuelva a implementar el grupo de discos.

**En ejecución** significa que el recurso se está ejecutando y en un estado correcto.

**Actualizando** significa que el recurso está pasando por una actualización. Esto suele ocurrir durante la implementación o al aplicar una actualización como agregar discos o LUN.

**Detenido (desasignado)** significa que el recurso está detenido y sus recursos subyacentes se han desasignado. Puede reiniciar el recurso para recuperar el grupo de discos o el destino iSCSI.

## <a name="recover-a-disk-pool-or-an-iscsi-target"></a>Recuperación de un grupo de discos o un destino iSCSI

En primer lugar, detenga el grupo de discos y reinícielo. A continuación, compruebe el estado del grupo de discos y el destino iSCSI. Si se han recuperado, los clústeres de Azure VMware conectados al grupo de discos se recuperarán automáticamente a menos que el grupo de discos haya estado inaccesible durante más de 24 horas. Si han pasado más de 24 horas, debe ponerse en contacto con el Soporte técnico de Azure para forzar la desconexión de los almacenes de datos inaccesibles asociados al grupo de discos. Después, puede volver a conectar los clústeres de VS al grupo de discos y configurar los almacenes de datos.

Si el grupo de discos no se recupera después de este proceso, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento de cualquier mensaje de error que haya recibido.

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>Códigos de error comunes al implementar un grupo de discos
 
|Código  |Descripción  |
|---------|---------|
|UnexpectedError     |Normalmente se produce cuando se produce un error irrecuperable de back-end. Vuelva a intentar la implementación. Si el problema no se resuelve, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del mensaje de error.         |
|DeploymentFailureZonalAllocationFailed     |Esto sucede cuando Azure se queda sin capacidad para aprovisionar una máquina virtual en la región o zona especificadas. Vuelva a intentar la implementación en otro momento.         |
|DeploymentFailureQuotaExceeded     |La suscripción que se usa para implementar el grupo de discos está fuera de la cuota de núcleos de máquina virtual en esta región. Puede [solicitar un aumento en los límites de cuota de CPU virtual por cada serie de máquinas virtuales de Azure](../azure-portal/supportability/per-vm-quota-requests.md) para la serie Dsv3.         |
|DeploymentFailurePolicyViolation     |Una directiva de la suscripción impedía la implementación de los recursos de Azure necesarios para admitir un grupo de discos. Consulte el para más detalles.         |
|DeploymentTimeout     |Esto ocurre cuando la implementación de la infraestructura del grupo de discos se queda bloqueada y no se completa en el tiempo asignado. Vuelva a intentar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del mensaje de error.         |
|OngoingOperationInProgress     |Hay una operación en curso en el grupo de discos. Espere hasta que se complete la operación y vuelva a intentar la implementación.         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>Códigos de error comunes al habilitar iSCSI en grupos de discos

|Código  |Descripción  |
|---------|---------|
|GoalStateApplicationError     |Se produce cuando la configuración del destino iSCSI no es válida y no se puede aplicar al grupo de discos. Vuelva a intentar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del error.         |
|GoalStateApplicationTimeoutError     |Se produce cuando la infraestructura del grupo de discos deja de responder al proveedor de recursos. Vuelva a intentar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del error.         |
|OngoingOperationInProgress     |Hay una operación en curso en el grupo de discos. Espere hasta que se complete la operación y vuelva a intentar la implementación.         |

## <a name="next-steps"></a>Pasos siguientes

[Administración de un grupo de discos de Azure (versión preliminar)](disks-pools-manage.md)