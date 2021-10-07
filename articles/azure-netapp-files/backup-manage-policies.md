---
title: Administración de directivas de copia de seguridad para Azure NetApp Files | Microsoft Docs
description: Describe cómo modificar o suspender una directiva de copia de seguridad para volúmenes de Azure NetApp Files.
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
ms.openlocfilehash: c3f440899e53125b6a7e6ef7986d74e4f1d0977d
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094915"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>Administración de directivas de copia de seguridad para Azure NetApp Files 

Se requiere una configuración de directiva de instantáneas para las [copias de seguridad basadas en directivas](backup-configure-policy-based.md) de Azure NetApp Files para realizar automáticamente una copia de seguridad de un volumen. Después de configurar las copias de seguridad de Azure NetApp Files mediante una directiva de copia de seguridad, puede modificar o suspender una directiva de copia de seguridad según sea necesario.  

Las copias de seguridad manuales no se ven afectadas por los cambios en la directiva de copia de seguridad.

## <a name="modify-a-backup-policy"></a>Modificación de una directiva de copia de seguridad   

Puede modificar una directiva de copia de seguridad de Azure NetApp Files existente según sea necesario para asegurarse de que tiene una cobertura de copia de seguridad adecuada para los volúmenes de Azure NetApp Files.  Por ejemplo, si necesita cambiar el número de copias de seguridad retenidas protegidas por el servicio, puede modificar la directiva de copia de seguridad de Azure NetApp Files del volumen para revisar el número de restauraciones que se van a mantener. 

Para modificar la configuración de la directiva de copia de seguridad:   

1. Vaya a **Copias de seguridad**.  

2. Seleccione **Directivas de Backup**, haga clic en los tres puntos (`…`) situados a la derecha de una directiva de copia de seguridad y, a continuación, haga clic en **Editar**.

    ![Captura de pantalla que muestra el menú contextual de Directivas de Backup.](../media/azure-netapp-files/backup-policies-edit.png)

3. En la ventana Modify Backup Policy (Modificar directiva de copia de seguridad), actualice el número de restauraciones que desea conservar para las copias de seguridad diarias, semanales y mensuales. Escriba el nombre de la directiva de copia de seguridad para confirmar la acción. Haga clic en **Save**(Guardar).  

    ![Captura de pantalla en la que se muestra la ventana para modificar la directiva de copia de seguridad.](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > Una vez habilitadas las copias de seguridad y que hayan tenido efecto para la frecuencia programada, no se puede cambiar el recuento de retención de copias de seguridad a `0`. Se requiere un número mínimo de retención de `1` para la directiva de copia de seguridad. Consulte [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md) para más información.  

## <a name="suspend-a-backup-policy"></a>Suspensión de una directiva de copia de seguridad  

Una directiva de copia de seguridad se puede suspender para que no realice nuevas operaciones de copia de seguridad en los volúmenes asociados. Esta acción le permite suspender temporalmente las copias de seguridad, en caso de que las copias de seguridad existentes deban mantenerse, pero no retirarse debido al control de versiones.   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>Suspensión de una directiva de copia de seguridad para todos los volúmenes asociados a la directiva

1. Vaya a **Copias de seguridad**.

2. Seleccione **Directivas de Backup**, haga clic en los tres puntos (`…`) situados a la derecha de una directiva de copia de seguridad y haga clic en **Editar**. 

3. Cambie **Policy State** (Estado de la directiva) a **Disabled** (Deshabilitado), escriba el nombre de la directiva para confirmar y haga clic en **Save** (Guardar). 

    ![Captura de pantalla que muestra la ventana para modificar la directiva de copia de seguridad con el estado de la directiva deshabilitado.](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>Suspensión de una directiva de copia de seguridad para un volumen específico 

1. Vaya a **Volúmenes**. 
2. Seleccione el volumen específico cuyas copias de seguridad desea suspender.
3. Seleccione **Configurar**.
4. En la página Configure Backups (Configurar copias de seguridad), cambie **Policy State** (Estado de la directiva) a **Suspend** (Suspender), escriba el nombre del volumen para confirmar y haga clic en **OK** (Aceptar).   

    ![Captura de pantalla que muestra la ventana para configurar las copias de seguridad con el estado de la directiva Suspender.](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de archivos de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales para archivos de Azure NetApp Files](backup-configure-manual.md)
* [Búsqueda de copias de seguridad de volúmenes de Azure NetApp Files](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



