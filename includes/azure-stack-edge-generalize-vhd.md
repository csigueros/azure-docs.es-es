---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: 95604a5b7d0bc62568e0e941e5cf035c956f141b
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968449"
---
1. Dentro de la máquina virtual, abra un símbolo del sistema.

1. Ejecute el siguiente comando para generalizar el VHD. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    Para más información, consulte [Introducción a Sysprep (preparación del sistema)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

1.  Una vez completado el comando, la máquina virtual se apagará. **No reinicie la VM**.
