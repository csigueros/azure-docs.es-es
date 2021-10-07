---
title: Restauración de una copia de seguridad en un nuevo volumen de Azure NetApp Files | Microsoft Docs
description: Describe cómo restaurar una copia de seguridad en un nuevo volumen.
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
ms.openlocfilehash: 1a0b2e59f2d11caf1db9985cfba937e2df0f44c7
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094907"
---
# <a name="restore-a-backup-to-a-new-volume"></a>Restauración de una copia de seguridad en un nuevo volumen

Al restaurar una copia de seguridad, se crea un nuevo volumen con el mismo tipo de protocolo. En este artículo, se explica la operación de restauración. 

## <a name="considerations"></a>Consideraciones

* El nuevo volumen creado por la operación de restauración no se puede montar hasta que se complete la restauración. 

* Debe desencadenar la operación de restauración cuando no haya copias de seguridad de línea base. De lo contrario, la restauración podría aumentar la carga en la cuenta de Azure Blob Storage en la que se hace la copia de seguridad de los datos. 

## <a name="steps"></a>Pasos

1. Seleccione **Volúmenes**. Vaya a **Copias de seguridad**.

    > [!NOTE]
    > Si se elimina un volumen, pero la directiva de copia de seguridad no se deshabilitó antes de la eliminación, todas las copias de seguridad relacionadas con el volumen se conservan en Azure Storage y puede encontrarlas en la cuenta de NetApp asociada.  Consulte [Búsqueda de copias de seguridad en el nivel de cuenta de NetApp](backup-search.md#search-backups-at-netapp-account-level).


2. En la lista de copias de seguridad, seleccione la que quiere restaurar. Haga clic en los tres puntos (`…`) situados a la derecha de la copia de seguridad y, a continuación, haga clic en **Restaurar al nuevo volumen** en el menú de acciones.   

    ![Captura de pantalla que muestra la opción de restaurar la copia de seguridad en un nuevo volumen.](../media/azure-netapp-files/backup-restore-new-volume.png)

3. En la página Crear un volumen que aparece, proporcione información para los campos de la página según corresponda y haga clic en **Revisar y crear** para empezar a restaurar la copia de seguridad en un nuevo volumen.   

    * El campo **Protocolo** se rellena previamente a partir del volumen original y no se puede cambiar.    
        Sin embargo, si restaura un volumen desde la lista de copias de seguridad en el nivel de cuenta de NetApp, debe especificar el campo Protocolo. El campo Protocolo debe coincidir con el protocolo del volumen original. De lo contrario, se producirá un error en la operación de restauración con el siguiente mensaje de error:  
        `Protocol Type value mismatch between input and source volume of backupId <backup-id of the selected backup>. Supported protocol type : <Protocol Type of the source volume>`

    * El valor de **Cuota** debe ser mayor o igual que el tamaño de la copia de seguridad desde la que se desencadena la restauración (100 GiB como mínimo).

    * El **grupo de capacidad** en el que se restaura la copia de seguridad debe tener suficiente capacidad sin usar para hospedar el nuevo volumen restaurado. De lo contrario, se producirá un error en la operación de restauración.   

    ![Captura de pantalla que muestra la página Crear un volumen.](../media/azure-netapp-files/backup-restore-create-volume.png)

## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de archivos de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales para archivos de Azure NetApp Files](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad de volúmenes de Azure NetApp Files](backup-search.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



