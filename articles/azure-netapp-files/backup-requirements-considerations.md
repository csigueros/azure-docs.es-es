---
title: Requisitos y consideraciones para la copia de seguridad de Azure NetApp Files | Microsoft Docs
description: Describe los requisitos y consideraciones que se deben tener en cuenta antes de usar la copia de seguridad de Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: abbf02776d6bc1506912a5bdd1446393b9f678fb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094911"
---
# <a name="requirements-and-considerations-for-azure-netapp-files-backup"></a>Requisitos y consideraciones para la copia de seguridad de archivos de Azure NetApp Files 

En este artículo se describen los requisitos y las consideraciones que se deben tener en cuenta antes de usar la copia de seguridad de Azure NetApp Files.

## <a name="requirements-and-considerations"></a>Requisitos y consideraciones

Antes de usar la copia de seguridad de Azure NetApp Files es preciso tener en cuenta varios requisitos y consideraciones antes de usar: 

* La copia de seguridad de Azure NetApp Files está disponible en las regiones asociadas a su suscripción de Azure NetApp Files. La copia de seguridad de Azure NetApp Files de una región solo puede proteger los volúmenes de Azure NetApp Files que se encuentren en esa misma región. Por ejemplo, las copias de seguridad que crea el servicio en Oeste de EE. UU. 2 para un volumen ubicado en Oeste de EE. UU. 2 se envían al almacenamiento de Azure que también se encuentra en Oeste de EE. UU. 2. Azure NetApp Files no admite copias de seguridad ni replicación de copias de seguridad en otra región.  

* Una vez que una copia de seguridad se ha completado puede tardar hasta 5 minutos en mostrarse.

* Actualmente, la característica de copia de seguridad de Azure NetApp Files admite la realización de copias de seguridad de las instantáneas locales diarias, semanales y mensuales que crea la directiva de instantáneas asociada en Azure Storage. Actualmente no se admiten las copias de seguridad por hora.

* La copia de seguridad de Azure NetApp Files usa la cuenta de [almacenamiento con redundancia de zona](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (ZRS) que replica los datos de forma sincrónica en tres zonas de disponibilidad de Azure de la región, excepto en las regiones que se indican a continuación, donde solo se admite el [almacenamiento con redundancia local](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (LRS):   

    * Oeste de EE. UU.   

    El LRS puede recuperarse de errores de unidad y bastidor del servidor. No obstante, si se produce un desastre como un incendio o una inundación en el centro de datos, es posible que todas las réplicas de las cuentas de almacenamiento que usen LRS se pierdan o no se puedan recuperar. 

* El uso de una copia de seguridad basada en directivas (programada) de Azure NetApp Files requiere que la directiva de instantáneas esté configurada y habilitada. Consulte [Administración de instantáneas mediante Azure NetApp Files](azure-netapp-files-manage-snapshots.md).   
    El volumen del que se debe realizar copia de seguridad requiere una directiva de instantáneas configurada para crear instantáneas. El número configurado de copias de seguridad se almacena en Azure Storage. 

* Si se produce un problema (por ejemplo, no queda espacio suficiente en el volumen) que hace que la directiva de instantáneas deje de crear instantáneas, la característica de copia de seguridad no tendrá instantáneas nueva de las que realizar copias de seguridad. 

## <a name="next-steps"></a>Pasos siguientes

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Funcionamiento de las instantáneas de Azure NetApp Files](snapshots-introduction.md)
