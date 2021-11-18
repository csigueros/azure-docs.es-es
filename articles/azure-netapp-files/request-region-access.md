---
title: Solicitar acceso a la región para Azure NetApp Files | Microsoft Docs
description: Indica cómo solicitar acceso a una región para usar Azure NetApp Files.
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
ms.date: 11/09/2021
ms.author: b-juche
ms.openlocfilehash: 55a4d9c74e0f4e2ee54b3a7bc16e2ef7e4b30502
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159297"
---
# <a name="request-region-access-for-azure-netapp-files"></a>Solicitar acceso a la región para Azure NetApp Files

En este artículo se indica cómo solicitar acceso a una región.

## <a name="steps"></a>Pasos

1. Vaya a **Nueva solicitud de soporte técnico** en **Soporte técnico y solución de problemas**.   

2. En la pestaña **Descripción del problema**, proporcione la información necesaria:
    1. En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .
    2. En **Suscripción**, seleccione la suscripción. 
    3. En **Tipo de cuota**, seleccione **Storage: límites de Azure NetApp Files**.

    ![Captura de pantalla que muestra la pestaña Descripción del problema.](../media/azure-netapp-files/support-problem-descriptions.png)

3. En la pestaña **Detalles adicionales**, haga clic en **Escribir detalles** en el campo Detalles de la solicitud.  

    ![Captura de pantalla que muestra la pestaña Detalles y el campo Escribir detalles.](../media/azure-netapp-files/quota-additional-details.png)

4. Para solicitar acceso a la región, proporcione la siguiente información en la ventana Detalles de cuota que aparece:   
    1. En **Tipo de cuota**, seleccione **Acceso a la región**.
    2. En **Region Requested** (Región solicitada), seleccione su región.

    ![Captura de pantalla que muestra la ventana Detalles de cuota para solicitar acceso a la región.](../media/azure-netapp-files/quota-details-region-access.png)

5. Haga clic en **Guardar y continuar**. Haga clic en **Revisar y crear** para crear la solicitud.

## <a name="next-steps"></a>Pasos siguientes  

- [Información sobre la jerarquía del almacenamiento de Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de costo de Azure NetApp Files](azure-netapp-files-cost-model.md)
- [Cuota de capacidad regional para Azure NetApp Files](regional-capacity-quota.md)
- [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
