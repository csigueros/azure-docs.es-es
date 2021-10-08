---
title: Deshabilitación de la funcionalidad de copia de seguridad de un volumen de Azure NetApp Files | Microsoft Docs
description: Se describe cómo deshabilitar la funcionalidad de copia de seguridad de un volumen que ya no necesita protección de copia de seguridad.
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
ms.openlocfilehash: 1254a9884063fce100152f370a434c55ec2ae207
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094924"
---
# <a name="disable-backup-functionality-for-a-volume"></a>Deshabilitación de la funcionalidad de copia de seguridad de un volumen 

Puede deshabilitar la funcionalidad de copia de seguridad de un volumen si ya no necesita la protección de copia de seguridad. 

> [!IMPORTANT]
> Al deshabilitar las copias de seguridad de un volumen, se eliminarán todas las copias de seguridad almacenadas en la instancia de Azure Storage con relación a dicho volumen.

Si se elimina un volumen, pero la directiva de copia de seguridad no se ha deshabilitado antes de la eliminación, todas las copias de seguridad relacionadas con el volumen se conservan en la instancia de Azure Storage y se mostrarán en la cuenta de NetApp asociada. 

## <a name="steps"></a>Pasos

1. Seleccione **Volúmenes**.
2. Seleccione el volumen específico cuya funcionalidad de copia de seguridad desee deshabilitar.
3. Seleccione **Configurar**.
4. En la página "Configurar copias de seguridad", cambie el valor **Habilitado** a **Desactivado**. Escriba el nombre del volumen que desee confirmar y haga clic en **Aceptar**.

    ![Captura de pantalla en la que se muestra la ventana "Configurar copias de seguridad" con la copia de seguridad deshabilitada](../media/azure-netapp-files/backup-configure-backups-disable.png)

## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)

