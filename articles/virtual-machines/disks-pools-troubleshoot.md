---
title: Introducción a la solución de problemas de los grupos de discos de Azure (versión preliminar)
description: Solucione los problemas con los grupos de discos de Azure. Obtenga información sobre los códigos de error comunes y cómo resolverlos.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 878cec7021ebcd7c3b63ae4af1f29a0793977280
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277286"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Solución de problemas de los grupos de discos de Azure (versión preliminar)

En este artículo, se enumeran algunos códigos de error comunes relacionados con los grupos de discos de Azure (versión preliminar). También proporciona posibles resoluciones y ciertas aclaraciones sobre los estados del grupo de discos.

## <a name="disk-pool-status"></a>Estado del grupo de discos

Los grupos de discos y los destinos iSCSI tienen cuatro estados: **Desconocido**, **En ejecución**, **Actualizando** y **Detenido (desasignado)** .

**Desconocido** significa que el recurso está en un estado incorrecto o desconocido. Para intentar la recuperación, realice una operación de actualización en el recurso (por ejemplo, agregar o quitar discos o LUN) o elimine y vuelva a implementar el grupo de discos.

**En ejecución** significa que el recurso se está ejecutando y en un estado correcto.

**Actualizando** significa que el recurso está pasando por una actualización. Esto suele ocurrir durante la implementación o al aplicar una actualización como agregar discos o LUN.

**Detenido (desasignado)** significa que el recurso está detenido y sus recursos subyacentes se han desasignado. Puede reiniciar el recurso para recuperar el grupo de discos o el destino iSCSI.

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>Códigos de error comunes al implementar un grupo de discos
 
|Código  |Descripción  |
|---------|---------|
|UnexpectedError     |Normalmente se produce cuando se produce un error irrecuperable de back-end. Vuelva a intentar la implementación. Si el problema no se resuelve, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del mensaje de error.         |
|DeploymentFailureZonalAllocationFailed     |Esto sucede cuando Azure se queda sin capacidad para aprovisionar una máquina virtual en la región o zona especificadas. Vuelva a intentar la implementación en otro momento.         |
|DeploymentFailureQuotaExceeded     |La suscripción que se usa para implementar el grupo de discos está fuera de la cuota de núcleos de máquina virtual en esta región. Puede [solicitar un aumento en los límites de cuota de CPU virtual por cada serie de máquinas virtuales de Azure](../azure-portal/supportability/per-vm-quota-requests.md) para la serie Dsv3.         |
|DeploymentFailurePolicyViolation     |Una directiva de la suscripción impedía la implementación de los recursos de Azure necesarios para admitir un grupo de discos. Consulte el para más detalles.         |
|DeploymentTimeout     |Esto ocurre cuando la implementación de la infraestructura del grupo de discos se queda bloqueada y no se completa en el tiempo asignado. Vuelva a intentar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del mensaje de error.         |
|GoalStateApplicationTimeoutError     |Se produce cuando la infraestructura del grupo de discos deja de responder al proveedor de recursos. Confirme que cumple los [requisitos previos de red](disks-pools-deploy.md#prerequisites) y vuelva a ejecutar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del error.         |
|OngoingOperationInProgress     |Hay una operación en curso en el grupo de discos. Espere hasta que se complete la operación y vuelva a intentar la implementación.         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>Códigos de error comunes al habilitar iSCSI en grupos de discos

|Código  |Descripción  |
|---------|---------|
|GoalStateApplicationError     |Se produce cuando la configuración del destino iSCSI no es válida y no se puede aplicar al grupo de discos. Vuelva a intentar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del error.         |
|GoalStateApplicationTimeoutError     |Se produce cuando la infraestructura del grupo de discos deja de responder al proveedor de recursos. Confirme que cumple los [requisitos previos de red](disks-pools-deploy.md#prerequisites) y vuelva a ejecutar la implementación. Si el problema persiste, póngase en contacto con el Soporte técnico de Azure y proporcione el identificador de seguimiento del error.         |
|OngoingOperationInProgress     |Hay una operación en curso en el grupo de discos. Espere hasta que se complete la operación y vuelva a intentar la implementación.         |

## <a name="next-steps"></a>Pasos siguientes

[Administración de un grupo de discos de Azure (versión preliminar)](disks-pools-manage.md)
