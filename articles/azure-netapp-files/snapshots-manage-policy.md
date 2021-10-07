---
title: Administración de directivas de instantáneas en Azure NetApp Files | Microsoft Docs
description: Describe cómo crear, administrar, modificar y eliminar directivas de instantáneas mediante Azure NetApp Files.
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: e4ccbd68ead83f682f39359c053e5a608d34903c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699515"
---
# <a name="manage-snapshot-policies-in-azure-netapp-files"></a>Administración de directivas de instantáneas en Azure NetApp Files

Las [instantáneas](snapshots-introduction.md) permiten la recuperación a un momento dado de los volúmenes. Puede programar la realización automática de [instantáneas de volumen](snapshots-introduction.md) mediante el uso de directivas de instantánea. También puede modificar una directiva de instantánea según sea necesario o eliminar una directiva de instantánea que ya no necesite.  

## <a name="create-a-snapshot-policy"></a>Creación de una directiva de instantánea 

Una directiva de instantánea le permite especificar la frecuencia de creación de instantáneas en ciclos de horas, diarios, semanales o mensuales. También debe especificar el número máximo de instantáneas que se conservarán para el volumen.  

1.  En la vista Cuenta de NetApp, haga clic en **Directiva de instantánea**.

    ![Captura de pantalla que muestra cómo navegar a Directiva de instantánea.](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  En la ventana Directiva de instantánea, establezca el estado de la directiva en **Habilitado**. 

3.  Haga clic en la pestaña **Por hora**, **Cada día**, **Semanalmente** o **Mensualmente** para crear directivas de instantánea por hora, diarias, semanales o mensuales. Seleccione el **número de instantáneas que desea mantener**.  

    Consulte [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md) acerca del número máximo de instantáneas permitido para un volumen. 

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea por hora. 

    ![Captura de pantalla que muestra la directiva de instantánea por hora.](../media/azure-netapp-files/snapshot-policy-hourly.png)

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea diaria.

    ![Captura de pantalla que muestra la directiva de instantánea diaria.](../media/azure-netapp-files/snapshot-policy-daily.png)

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea semanal.

    ![Captura de pantalla que muestra la directiva de instantánea semanal.](../media/azure-netapp-files/snapshot-policy-weekly.png)

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea mensual.

    ![Captura de pantalla que muestra la directiva de instantánea mensual.](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Haga clic en **Save**(Guardar).  

Si tiene que crear directivas de instantánea adicionales, repita el paso 3.
Las directivas que ha creado aparecen en la página Directiva de instantánea.

Si desea que un volumen use la directiva de instantánea, debe [aplicar la directiva al volumen](snapshots-manage-policy.md#apply-a-snapshot-policy-to-a-volume). 

## <a name="apply-a-snapshot-policy-to-a-volume"></a>Aplicación de una directiva de instantánea a un volumen

Si desea que un volumen use una directiva de instantánea que haya creado, debe aplicarla al volumen. 

No se puede aplicar una directiva de instantánea a un volumen de destino en la replicación entre regiones.  

1.  Vaya a la página **Volúmenes**, haga clic con el botón derecho en el volumen al que desee aplicar una directiva de instantánea y seleccione **Editar**.

    ![Captura de pantalla que muestra el menú contextual de Volúmenes.](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  En la ventana de edición, en **Directiva de instantánea**, seleccione la directiva que se vaya a usar para el volumen.  Haga clic en **Aceptar** para aplicar la directiva.  

    ![Captura de pantalla que muestra el menú Directiva de instantánea.](../media/azure-netapp-files/snapshot-policy-edit.png) 

## <a name="modify-a-snapshot-policy"></a>Modificación de una directiva de instantánea 

Puede modificar una directiva de instantánea existente para cambiar su estado, la frecuencia (cada hora, diariamente, semanalmente o mensualmente) o el número de instantáneas que se deben conservar.  
 
1.  En la vista Cuenta de NetApp, haga clic en **Directiva de instantánea**.

2.  Haga clic con el botón derecho en la directiva de instantánea que desee modificar y, a continuación, seleccione **Editar**.

    ![Captura de pantalla que muestra el menú contextual de Directiva de instantánea.](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Realice los cambios en la ventana Directiva de instantánea que aparezca y, a continuación, haga clic en **Guardar**. 

## <a name="delete-a-snapshot-policy"></a>Eliminación de una directiva de instantánea 

Puede eliminar una directiva de instantánea que ya no desee conservar.   

1.  En la vista Cuenta de NetApp, haga clic en **Directiva de instantánea**.

2.  Haga clic con el botón derecho en la directiva de instantánea que desee modificar y, a continuación, seleccione **Eliminar**.

    ![Captura de pantalla que muestra el elemento de menú Eliminar.](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Haga clic en **Sí** para confirmar que desea eliminar la directiva de instantánea.   

    ![Captura de pantalla que muestra la confirmación de eliminación de la directiva de instantánea.](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas de directivas de instantáneas](troubleshoot-snapshot-policies.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Más información sobre las instantáneas](snapshots-introduction.md)