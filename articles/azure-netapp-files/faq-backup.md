---
title: Preguntas frecuentes sobre copia de seguridad de Azure NetApp Files | Microsoft Docs
description: Aquí se responden las preguntas frecuentes (FAQ) sobre el uso de la característica de copia de seguridad de Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: cec399e00215994e55e06430f2170d8c19c20b03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269912"
---
# <a name="azure-netapp-files-backup-faqs"></a>Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files

En este artículo se responden las preguntas frecuentes (FAQ) sobre el uso de la característica de [copia de seguridad de Azure NetApp Files](backup-introduction.md). 

## <a name="when-do-my-backups-occur"></a>¿Cuándo se producen mis copias de seguridad?   

Las copias de seguridad de Azure NetApp Files se inician dentro de un período de tiempo aleatorio después de que se especifique la frecuencia de una directiva de copia de seguridad. Por ejemplo, las copias de seguridad semanales se inician el domingo dentro de un intervalo asignado aleatoriamente después de las 12:00 a.m., medianoche. Los usuarios no pueden modificar este intervalo en este momento. La copia de seguridad de la línea base se inicia en cuanto se asigna la directiva de copia de seguridad al volumen.

## <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>¿Qué ocurre si surge un problema en una operación de copia de seguridad?

Si se produce un problema durante una operación de copia de seguridad, la copia de seguridad de Azure NetApp Files reintenta la operación automáticamente, sin necesidad de la interacción del usuario. Si los reintentos siguen sin funcionar, la copia de seguridad de Azure NetApp Files informará del error de la operación.

## <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>¿Puedo cambiar la ubicación o el nivel de almacenamiento de mi almacén de copia de seguridad?

No, Azure NetApp Files automáticamente la ubicación de los datos de copia de seguridad dentro de Azure Storage. El usuario no puede modificar esta ubicación o capa de almacenamiento de Azure.

## <a name="what-types-of-security-are-provided-for-the-backup-data"></a>¿Qué tipos de seguridad se proporcionan para los datos de copia de seguridad?

Azure NetApp Files el cifrado AES de 256 bits durante la codificación de los datos de copia de seguridad recibidos. Además, los datos cifrados se transmiten de forma segura a Azure Storage mediante conexiones HTTPS TLSv1.2. La copia de seguridad de Azure NetApp Files depende de la funcionalidad en reposo del cifrado integrado de la cuenta de Azure Storage para almacenar los datos de copia de seguridad.

## <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>¿Qué ocurre con las copias de seguridad cuando se elimina un volumen o una cuenta de NetApp? 

 Al eliminar un volumen de Azure NetApp Files, se conservan las copias de seguridad. Si no desea que se conserven las copias de seguridad, deshabilítelas antes de eliminar el volumen. Cuando se elimina una cuenta de NetApp, las copias de seguridad se conservan y se muestran en otras cuentas de NetApp de la misma suscripción, por lo que sigue estando disponible para la restauración. Si elimina todas las cuentas de NetApp de una suscripción, debe asegurarse de deshabilitar las copias de seguridad antes de eliminar todos los volúmenes de todas las cuentas de NetApp.

## <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>¿Cuál es el número máximo de reintentos de copia de seguridad del sistema para un volumen?  

El sistema realiza 10 reintentos al procesar un trabajo de copia de seguridad programado. Si se produce un error en el trabajo, el sistema genera un error en la operación de copia de seguridad. En el caso de las copias de seguridad programadas (basadas en la directiva configurada), el sistema intenta hacer una copia de seguridad de los datos una vez cada hora. Si hay nuevas instantáneas disponibles que no se han transferido (o no se han podido realizar en el último intento), esas instantáneas se considerarán para la transferencia. 

## <a name="next-steps"></a>Pasos siguientes  

- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes de la resistencia de la aplicación](faq-application-resilience.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)
