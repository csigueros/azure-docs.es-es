---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 6e18baf15b63a37f9c7746ac1273b481f1ea77cc
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556582"
---
Si usa Data Box en la Administración Pública de EE. UU., Japón, Singapur, Corea, India, Sudáfrica, Reino Unido, Alemania, Suiza, Oeste de Europa, Australia o Brasil, y ha seleccionado el envío autoadministrado al crear el pedido, siga estas instrucciones. Para ver los pasos detallados, consulte [Uso del envío autoadministrado](../articles/databox/data-box-portal-customer-managed-shipping.md).

1. Anote el código de autorización que se muestra en la página **Preparación para el envío** de la interfaz de usuario web local de Data Box una vez que el paso se complete correctamente.
2. Apague el dispositivo y quite los cables. Enrolle y coloque de forma segura el cable de alimentación que se suministró junto con el dispositivo en la parte posterior del mismo.
3. Cuando esté listo para devolver el dispositivo, envíe un correo electrónico al equipo de Azure Data Box Operations mediante la siguiente plantilla.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a government-approved ID during the drop off.
    ```

   > [!NOTE]
   > - La información necesaria para la devolución puede variar según la región. 
   > - Si va a devolver una instancia de Data Box en Brasil, consulte [Uso del envío autoadministrado para Azure Data Box](..\articles\databox\data-box-portal-customer-managed-shipping.md) para obtener instrucciones detalladas. 
