---
title: Solución de problemas de sincronización de hora para dispositivos de Azure Data Box y Azure Data Box Heavy
description: Aquí se describe cómo solucionar problemas de sincronización de hora en Azure Data Box o Azure Data Box Heavy a través de la interfaz de PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 8999c2873528dd5dd0fefc0b730f4856ac995649
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557184"
---
# <a name="sync-device-time-for-azure-data-box-and-azure-data-box-heavy"></a>Sincronización de la hora del dispositivo Azure Data Box y Azure Data Box Heavy

En este artículo se describe cómo diagnosticar que el dispositivo Data Box no está sincronizado y, a continuación, cómo cambiar la hora en este. La información de este artículo se aplica a los pedidos de importación y exportación en dispositivos Data Box y Data Box Heavy.


## <a name="about-device-time-sync"></a>Acerca de la sincronización de la hora del dispositivo

Data Box sincroniza automáticamente la hora cuando se conecta a Internet mediante el servidor de hora `time.windows.com` de Windows predeterminado. Sin embargo, si Data Box no está conectado a Internet, es posible que la hora del dispositivo no esté sincronizada. Esta situación puede afectar a la copia de datos de los datos de origen a Data Box, específicamente si la copia se realiza a través de la API de REST o de determinadas herramientas que tienen restricciones de tiempo. 

Si ve alguna diferencia de tiempo entre la hora en Data Box y otros dispositivos locales del sitio, puede sincronizar la hora en Data Box accediendo a su interfaz de PowerShell. `Set-Date API` se usa para modificar la hora del dispositivo. Para obtener más información, consulte [Set-Date API](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true).


## <a name="connect-to-powershell-interface"></a>Conexión a la interfaz de PowerShell

Para solucionar problemas de sincronización de hora, primero debe conectarse a la interfaz de PowerShell del dispositivo.

[!INCLUDE [Connect to Data Box PowerShell interface](../../includes/data-box-connect-powershell-interface.md)]


## <a name="change-device-time"></a>Cambio de la hora del dispositivo

Para cambiar la hora del dispositivo, siga estos pasos.

1. Para diagnosticar si la hora del dispositivo no está sincronizada, obtenga primero la hora de este. Use el cmdlet `Get-Date` para ver la fecha y hora de Data Box.

    `Get-Date`

1. Si la hora del dispositivo no está sincronizada, use el cmdlet `Set-Date` para cambiar la hora de Data Box.

    - Establezca la hora a 2 minutos antes.
    
        ```powershell
        Set-Date -Adjust <time change in hours:mins:secs format> -DisplayHint Time
        ```
    - Establezca la hora con 2 minutos de retraso.

        ```powershell
        Set-Date -Adjust -<time change in hours:mins:secs format> -DisplayHint Time
        ```    

        A continuación, se incluye una salida de ejemplo:
        
        ```powershell
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:22:50 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust 00:02:00 -DisplayHint Time
        2:25:18 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust -00:02:00 -DisplayHint Time
        2:23:28 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:23:42 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>
        ```
        Para obtener más información, consulte [Set-Date API](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true).
    
## <a name="next-steps"></a>Pasos siguientes

Para solucionar otros problemas de Data Box, consulte uno de los siguientes artículos:

- [Solución de problemas relacionados con el almacenamiento de blobs de Data Box](data-box-troubleshoot-rest.md).
- [Solución de problemas relacionados con la copia de datos de Data Box](data-box-troubleshoot.md).
- [Solución de problemas relacionados con la carga de datos de Data Box](data-box-troubleshoot-data-upload.md).
