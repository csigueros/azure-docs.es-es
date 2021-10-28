---
title: Eliminación de copias de seguridad de un volumen de Azure NetApp Files | Microsoft Docs
description: Se describe cómo eliminar copias de seguridad individuales que ya no necesita conservar de un volumen.
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
ms.openlocfilehash: e1d59d336d331a41bf5f82e650d80d7843ab3977
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250585"
---
# <a name="delete-backups-of-a-volume"></a>Eliminación de copias de seguridad de un volumen 

Puede eliminar copias de seguridad individuales que ya no necesita conservar de un volumen. Al eliminar las copias de seguridad, se eliminarán los objetos asociados en la cuenta de Azure Storage, lo que provocará un ahorro de espacio.  

No se puede eliminar la copia de seguridad más reciente.  

## <a name="steps"></a>Pasos

1. Seleccione **Volúmenes**.
2. Vaya a **Copias de seguridad**.
3. En la lista de copias de seguridad, seleccione la que quiere eliminar. Haga clic en los tres puntos (`…`) a la derecha de la copia de seguridad y, a continuación, haga clic en **Eliminar** en el menú "Acción".

    ![Captura de pantalla que muestra el menú "Eliminar" de las copias de seguridad.](../media/azure-netapp-files/backup-action-menu-delete.png)

## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](faq-backup.md)
