---
title: Búsqueda de copias de seguridad de volúmenes de Azure NetApp Files | Microsoft Docs
description: Aquí se explica cómo mostrar y buscar copias de seguridad de volúmenes de Azure NetApp Files en el nivel de volumen y el nivel de cuenta de NetApp.
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
ms.topic: how-to
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 71fcf944f0664c1d3e4d07e96348d109042047a8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094899"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>Búsqueda de copias de seguridad de volúmenes de Azure NetApp Files

Puede mostrar copias de seguridad y buscar una determinada en el nivel de volumen o el nivel de cuenta de NetApp.

Los nombres usados para las instantáneas se conservan cuando se hace una copia de seguridad de las instantáneas. Los nombres de instantánea incluyen el prefijo `daily`, `weekly` o `monthly`. También incluyen la marca de tiempo de cuando se creó la instantánea. La primera instantánea que se toma cuando se habilita la característica de copia de seguridad se denomina *instantánea de línea de base*, y su nombre incluye el prefijo `snapmirror`.

Si se elimina un volumen, sus copias de seguridad se siguen conservando. Las copias de seguridad se muestran en las cuentas asociadas de NetApp, en la sección Copias de seguridad. Esta lista incluye todas las copias de seguridad de la suscripción (en cuentas de NetApp) en la región. Se puede usar para restaurar una copia de seguridad en un volumen de otra cuenta de NetApp de la misma suscripción.

## <a name="search-backups-at-volume-level"></a>Búsqueda de copias de seguridad en el nivel de volumen    

Puede mostrar y buscar copias de seguridad en el nivel de volumen:

1. Seleccione **Volúmenes**.

2. Vaya a **Copias de seguridad** para mostrar las copias de seguridad del volumen.   
    La columna **Tipo** muestra si la copia de seguridad se genera mediante una copia de seguridad *programada* (basada en directivas) o *manual*.

3. En el cuadro **Buscar copias de seguridad**, escriba el nombre de la copia de seguridad que quiere buscar.  

    Se admite la búsqueda parcial; no hay que especificar el nombre completo de la copia de seguridad. La búsqueda filtra las copias de seguridad en función de la cadena de búsqueda.

    ![Captura de pantalla que muestra una lista de copias de seguridad de un volumen.](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>Búsqueda de copias de seguridad en el nivel de cuenta de NetApp 

Puede mostrar y buscar copias de seguridad asociadas a una suscripción en todas las cuentas de NetApp de una región de una suscripción.   

1. Seleccione **Cuenta de NetApp**.

2. Vaya a **Copias de seguridad**.
    La columna **Tipo** muestra si la copia de seguridad se genera mediante una copia de seguridad *programada* (basada en directivas) o *manual*. 

3. En el cuadro **Buscar copias de seguridad**, escriba el nombre de la copia de seguridad que quiere buscar.

    * La lista de copias de seguridad incluye todas las copias de seguridad de todos los volúmenes (tanto activos como eliminados) que pertenecen a todas las cuentas de NetApp de una suscripción.

    * La lista de copias de seguridad se puede usar para restaurar una copia de seguridad en un nuevo volumen y también para eliminar una copia de seguridad. El flujo de trabajo para restaurar una copia de seguridad es el mismo que el de la [restauración de copias de seguridad de nivel de volumen](backup-restore-new-volume.md). El flujo de trabajo para eliminar una copia de seguridad es el mismo que el de la [eliminación de copias de seguridad de nivel de volumen](backup-delete.md).

    * El procedimiento recomendado sugerido para la convención de nomenclatura de [copias de seguridad manuales](backup-configure-manual.md) ayuda a identificar a qué volumen pertenece la copia de seguridad. Este procedimiento es especialmente útil en casos en los que la copia de seguridad pertenece a un volumen que se ha eliminado (con copias de seguridad conservadas).

    ![Captura de pantalla que muestra una lista de copias de seguridad de una cuenta de NetApp.](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de archivos de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
