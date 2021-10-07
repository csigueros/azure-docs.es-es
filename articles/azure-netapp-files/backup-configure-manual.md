---
title: Configuración de copias de seguridad manuales para Azure NetApp Files | Microsoft Docs
description: Se describe cómo configurar copias de seguridad manuales para volúmenes de Azure NetApp Files.
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
ms.openlocfilehash: 827268fee96227f056029c8b2a75629353861d7e
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094891"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>Configuración de copias de seguridad manuales para Azure NetApp Files 

La copia de seguridad de Azure NetApp Files admite tanto copias de seguridad *basadas en directivas* (programadas) como copias de seguridad *manuales* (a petición) en el nivel de volumen. Puede usar ambos tipos de copias de seguridad en el mismo volumen. Para poder hacer copias de seguridad manuales o basadas en directivas, deberá habilitar antes la característica de copia de seguridad para un volumen de Azure NetApp Files durante el proceso de configuración. 

En este artículo se muestra cómo configurar las copias de seguridad manuales. Para obtener más información sobre la configuración de las copias de seguridad basadas en directivas, consulte [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md).  

> [!IMPORTANT]
> La característica de copia de seguridad de Azure NetApp Files se encuentra actualmente en versión preliminar. Para acceder a la característica, debe enviar una solicitud de lista de espera a través de la página de **[versión preliminar pública de copias de seguridad de Azure NetApp Files](https://aka.ms/anfbackuppreviewsignup)** . Espere a recibir el correo electrónico de confirmación oficial del equipo de Azure NetApp Files antes de utilizar la característica de copia de seguridad de dicho servicio.

## <a name="about-manual-backups"></a>Acerca de las copias de seguridad manuales  

Cada uno de los volúmenes de Azure NetApp Files debe tener habilitada la funcionalidad de copia de seguridad para poder realizar copias de seguridad (basadas en directivas o manuales).   

Después de habilitar la funcionalidad de copia de seguridad, tiene la opción de realizar manualmente una copia de seguridad de un volumen. Una copia de seguridad manual toma una instantánea a un momento dado del sistema de archivos activo y realiza una copia de seguridad de esa instantánea en la cuenta de Azure Storage.

En la lista siguiente se resumen los comportamientos de las copias de seguridad manuales:  

* Puede crear copias de seguridad manuales en un volumen incluso si el volumen ya está habilitado para copias de seguridad y configurado con directivas de copias de seguridad.  Sin embargo, solo puede haber una solicitud de copia de seguridad manual pendiente para el volumen. Si asigna una directiva de copia de seguridad y la transferencia de línea de base todavía está en curso, la creación de una copia de seguridad manual se bloqueará hasta que se complete la transferencia de línea de base.

* La creación de una copia de seguridad manual genera una instantánea en el volumen. A continuación, la instantánea se transfiere a Azure Storage. Esta instantánea no se elimina automáticamente, sino que debe eliminarla de forma manual.  No obstante, no se permite eliminar la instantánea generada para la copia de seguridad manual más reciente.  Por lo tanto, después de crear una copia de seguridad manual posterior, puede limpiar (eliminar) las instantáneas generadas para las copias de seguridad manuales anteriores si no necesita conservarlas. 

## <a name="enable-backup-functionality"></a>Habilitación de la funcionalidad de copia de seguridad

Si no lo ha hecho, habilite la funcionalidad de copia de seguridad del volumen antes de crear copias de seguridad manuales: 

1. Vaya a **Volúmenes** y seleccione el volumen específico para el que quiera habilitar la copia de seguridad.
2. Seleccione **Configurar**.
3. En la página de configuración de copias de seguridad, cambie el valor **Habilitado** a **Activado**.   
    El campo Almacén se rellena previamente. 
4. Haga clic en **OK**.   

![Captura de pantalla que muestra la opción "Habilitado" de la ventana "Configuración de copias de seguridad".](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>Creación de una copia de seguridad manual de un volumen

1. Vaya a **Volúmenes** y seleccione el volumen para el que quiera crear una copia de seguridad manual.
2. Seleccione **Add Backup** (Agregar copia de seguridad).
3. Especifique un nombre para la copia de seguridad.   

    * Los nombres de las copias de seguridad manuales deben tener entre 3 y 256 caracteres.   

    * Como procedimiento recomendado, anteponga un prefijo en el siguiente formato antes del nombre real de la copia de seguridad. Esto le ayuda a identificar la copia de seguridad manual si se elimina el volumen (con copias de seguridad retenidas).   

        `NetAppAccountName-CapacityPoolName-VolumeName`   

        Por ejemplo, suponga que la cuenta de NetApp es `account1`, el grupo de capacidad es `pool1` y el nombre del volumen es `vol1`. Una copia de seguridad manual se puede denominar de la siguiente manera:    

        `account1-pool1-vol1-backup1`   

        Si usa un formato más corto para el nombre de la copia de seguridad, asegúrese de que todavía incluye información que identifica la cuenta de NetApp, el grupo de capacidad y el nombre del volumen para mostrar en la lista de copias de seguridad.

4. Haga clic en **Crear**. 

    Al crear una copia de seguridad manual, también se crea una instantánea en el volumen con el mismo nombre que especificó para la copia de seguridad. Esta instantánea representa el estado actual del sistema de archivos activo. Se transfiere a Azure Storage. Una vez completada la copia de seguridad, la entrada de la copia de seguridad manual aparecerá en la lista de copias de seguridad del volumen.

![Captura de pantalla que muestra la ventana "Nueva copia de seguridad".](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)


