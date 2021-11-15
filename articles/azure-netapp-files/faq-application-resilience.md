---
title: Preguntas más frecuentes sobre resistencia de aplicaciones para Azure NetApp Files | Microsoft Docs
description: Este artículo responde a las preguntas más frecuentes (P+F) sobre la resistencia de aplicaciones para Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 3a0b243203c0edb01bc5b647a15093ee52b78e7c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270157"
---
# <a name="application-resilience-faqs-for-azure-netapp-files"></a>Preguntas más frecuentes sobre resistencia de aplicaciones para Azure NetApp Files

En este artículo se responden las preguntas más frecuentes (P+F) sobre la resistencia de aplicaciones para Azure NetApp Files.

## <a name="what-do-you-recommend-for-handling-potential-application-disruptions-due-to-storage-service-maintenance-events"></a>¿Qué se recomienda para controlar posibles interrupciones de la aplicación debido a eventos de mantenimiento del servicio de almacenamiento?

Azure NetApp Files pueden someterse a un mantenimiento planeado ocasional (por ejemplo, actualizaciones de plataforma, actualizaciones de servicio o de software). Desde una perspectiva del protocolo de archivos (NFS/SMB), las operaciones de mantenimiento no son perjudiciales, siempre y cuando la aplicación pueda administrar las pausas de E/S que pueden producirse brevemente durante estos eventos. Las pausas de E/S suelen ser cortas, desde unos segundos hasta 30 segundos. El protocolo NFS es especialmente sólido y las operaciones de archivo cliente-servidor continúan con normalidad. Algunas aplicaciones pueden requerir un ajuste para administrar las pausas de E/S de entre 30 y 45 segundos. Por lo tanto, asegúrese de que conoce la configuración de resistencia de la aplicación para hacer frente a los eventos de mantenimiento del servicio de almacenamiento. En el caso de las aplicaciones interactivas humanas que aprovechan el protocolo SMB, la configuración de protocolo estándar suele ser suficiente. 

## <a name="do-i-need-to-take-special-precautions-for-smb-based-applications"></a>¿Es necesario tomar precauciones especiales para las aplicaciones basadas en SMB?

Sí, ciertas aplicaciones basadas en SMB requieren la conmutación por error transparente de SMB. La conmutación por error transparente de SMB facilita las operaciones de mantenimiento en el servicio Azure NetApp Files sin interrumpir la conectividad con las aplicaciones de servidor que almacenan los datos en volúmenes SMB y acceden a ellos. Para facilitar la conmutación por error transparente de SMB, Azure NetApp Files ahora admite la [opción de recursos compartidos con disponibilidad continua de SMB](azure-netapp-files-create-volumes-smb.md#continuous-availability). 

## <a name="i-am-running-ibm-mq-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>Estoy ejecutando IBM MQ en Azure NetApp Files. ¿Qué precauciones puedo tomar para evitar interrupciones debido a eventos de mantenimiento del servicio de almacenamiento a pesar de usar el protocolo NFS?

Si ejecuta la [aplicación IBM MQ en una configuración de archivos compartidos](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-sharing-mq-files), donde los datos y registros de IBM MQ se almacenan en un volumen de Azure NetApp Files, se recomiendan las siguientes consideraciones para mejorar la resistencia durante los eventos de mantenimiento del servicio de almacenamiento:

* Solo debe usar el protocolo NFS v4.1.
* Para alta disponibilidad, debe usar una [configuración de instancias múltiples de IBM MQ mediante volúmenes NFS v4.1 compartidos](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=manager-create-multi-instance-queue-linux). 
* Debe comprobar la funcionalidad de la [configuración de instancias múltiples de IBM mediante volúmenes NFS v4.1 compartidos](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-verifying-shared-file-system-behavior). 
* Debe implementar una arquitectura de IBM MQ de escalabilidad horizontal en lugar de usar una configuración de IBM MQ de instancias múltiples de gran tamaño. Al distribuir la carga de procesamiento de mensajes entre varios pares de instancias múltiples de IBM MQ, la posibilidad de interrupción del servicio podría reducirse porque cada par de instancias múltiples de MQ procesaría menos mensajes.

> [!NOTE] 
> El número de mensajes que debe procesar cada par de instancias múltiples de MQ depende en gran medida de su entorno específico. Debe decidir cuántos pares de instancias múltiples de MQ serían necesarios o cuáles serían las reglas de escalado o reducción vertical.

La arquitectura de escalabilidad horizontal constaría de varios pares de instancias múltiples de la implementados IBM MQ detrás de una instancia de Azure Load Balancer. Las aplicaciones configuradas para comunicarse con IBM MQ se configurarían para comunicarse con las instancias de IBM MQ mediante Azure Load Balancer. Para obtener compatibilidad relacionada con IBM MQ en volúmenes NFS compartidos, debe obtener soporte técnico del proveedor en IBM.

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>Estoy ejecutando Apache ActiveMQ con LevelDB o KahaDB en Azure NetApp Files. ¿Qué precauciones puedo tomar para evitar interrupciones debido a eventos de mantenimiento del servicio de almacenamiento a pesar de usar el protocolo *NFS*?

>[!NOTE]
> En este artículo se describen referencias a los términos *subordinado* y *maestro*, unos términos que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

Si ejecuta Apache ActiveMQ, se recomienda implementar la [alta disponibilidad de ActiveMQ con cajas de seguridad de almacenamiento conectables](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq). 

Los modelos de alta disponibilidad de ActiveMQ garantizan que una instancia de agente siempre está en línea y puede procesar el tráfico de mensajes. Los dos modelos de alta disponibilidad de ActiveMQ más comunes implican el uso compartido de un sistema de archivos a través de una red. El propósito es proporcionar LevelDB o KahaDB a las instancias de agente activas y pasivas. Estos modelos de alta disponibilidad requieren que se obtenga y mantenga un bloqueo de nivel de sistema operativo en un archivo de los directorios LevelDB o KahaDB, denominado "bloqueo". Hay algunos problemas con este modelo de alta disponibilidad de ActiveMQ. Pueden dar lugar a una situación de "sin maestro", donde el "subordinado" no es consciente de que puede bloquear el archivo.  También pueden dar lugar a una configuración de "maestro-maestro" que da como resultado daños en el índice o el diario y, en última instancia, la pérdida de mensajes. La mayoría de estos problemas se derivan de factores fuera del control de ActiveMQ. Por ejemplo, un cliente NFS mal optimizado puede hacer que los datos de bloqueo se vuelvan obsoletos bajo carga, lo que provoca un tiempo de inactividad "sin maestro" durante la conmutación por error. 

Dado que la mayoría de los problemas con esta solución de alta disponibilidad se derivan de bloqueos de archivos de nivel de sistema operativo inexactos, la comunidad de ActiveMQ [introdujo el concepto de una caja de seguridad de almacenamiento conectable](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq) en la versión 5.7 del agente. Este enfoque permite que un usuario aproveche un medio diferente del bloqueo compartido, mediante un bloqueo de base de datos JDBC de nivel de fila en lugar de un bloqueo del sistema de archivos de nivel de sistema operativo. Para obtener soporte técnico o consultoría sobre las implementaciones y arquitecturas de alta disponibilidad de ActiveMQ, debe [ponerse en contacto con OpenLogic by Perforce](https://www.openlogic.com/contact-us).

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despites-using-the-smb-protocol"></a>Estoy ejecutando Apache ActiveMQ con LevelDB o KahaDB en Azure NetApp Files. ¿Qué precauciones puedo tomar para evitar interrupciones debido a eventos de mantenimiento del servicio de almacenamiento a pesar de usar el protocolo *SMB*?

La recomendación general del sector es [no ejecutar el almacenamiento compartido de KahaDB en CIFS/SMB](https://www.openlogic.com/blog/activemq-community-deprecates-leveldb-what-you-need-know). Si tiene problemas para mantener el estado de bloqueo preciso, consulte la página de la caja de seguridad de almacenamiento conectable de JDBC, que puede proporcionar un mecanismo de bloqueo más confiable. Para obtener soporte técnico o consultoría sobre las implementaciones y arquitecturas de alta disponibilidad de ActiveMQ, debe [ponerse en contacto con OpenLogic by Perforce](https://www.openlogic.com/contact-us).

## <a name="next-steps"></a>Pasos siguientes  

- [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Preguntas frecuentes sobre redes](faq-networking.md)
- [Preguntas más frecuentes de seguridad](faq-security.md)
- [Preguntas más frecuentes sobre rendimiento](faq-performance.md)
- [Preguntas más frecuentes sobre NFS](faq-nfs.md)
- [Preguntas más frecuentes de SMB](faq-smb.md)
- [Preguntas más frecuentes sobre la administración de la capacidad](faq-capacity-management.md)
- [Preguntas frecuentes sobre migración y protección de datos](faq-data-migration-protection.md)
- [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
- [Preguntas más frecuentes sobre la integración](faq-integration.md)

