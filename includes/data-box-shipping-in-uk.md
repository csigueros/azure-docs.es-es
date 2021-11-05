---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: ee0c69c4cd7a3ca6d1d6b7eaef033850aa25d083
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005607"
---
Si va a devolver el dispositivo en el Reino Unido, siga estos pasos.

1. Asegúrese de que el dispositivo está apagado y de que se han quitado los cables.
2. Enrolle y coloque de forma segura el cable de alimentación que se proporcionó junto con el dispositivo en la parte posterior del mismo.
3. Asegúrese de que la etiqueta de envío aparece en la pantalla de tinta electrónica y programe una recogida con su transportista. Si la etiqueta está dañada, se ha perdido o no aparece en la pantalla de tinta electrónica, [póngase en contacto con el servicio de soporte técnico de Microsoft](..\articles\databox\data-box-disk-contact-microsoft-support.md). Si el soporte técnico lo sugiere, puede ir a **Información general > Descargar la etiqueta de envío** en Azure Portal. Descargue la etiqueta de envío y péguela en el dispositivo.
1. Compruebe la etiqueta de envío para ver el centro de datos desde el que se envió el dispositivo: Cardiff o Londres. El centro de datos de Londres exige que se les avise con antelación de todas las devoluciones de dispositivos. 

    Si el dispositivo se envió desde Londres, siga estos pasos:
    1. Envíe un correo electrónico a Operaciones de Azure Data Box, a [adbops@microsoft.com](mailto:adbops@microsoft.com) para recibir un identificador de entrada. Envíe un correo electrónico a [adbops@microsoft.com](mailto:adbops@microsoft.com). Use la siguiente plantilla.

       ```
       To: adbops@microsoft.com
       Subject: Request for Azure Data Box Inbound ID: <orderName> 
       Body: 
        
       I am ready to return an Azure Data Box and would like to request an Inbound ID for the following order:
       
       Order Name: <orderName>
       Return Tracking Number: <returnTracking#>
       ```

    1. Anote el número de identificador de entrada proporcionado por Operaciones de Azure Data Box y péguelo en la unidad, en un lugar claramente visible, cerca de la etiqueta de devolución.
1. Programe una recogida con UPS si está devolviendo el dispositivo. Para programar una recogida:

    * Llame a la oficina local de UPS (número gratuito específico del país o región).
    * En la llamada, indique el número de seguimiento del envío inverso, que se muestra en la pantalla E-ink (Tinta electrónica) o la etiqueta impresa. Si no indica el número de seguimiento, UPS le exigirá una cantidad adicional en la recogida.
    * Si surge algún problema durante la programación de una recogida o si se le pide que pague tarifas adicionales, póngase en contacto con Azure Data Box Operations. Envíe un correo electrónico a [adbops@microsoft.com](mailto:adbops@microsoft.com).

    En lugar de programar la recogida, también devolver la instancia de Data Box en la ubicación de recogida más cercana.

Una vez que el transportista recoge y examina el dispositivo Data Box, el estado del pedido en el portal se actualiza a **Picked up** (Recogido). También se muestra un identificador de seguimiento.
