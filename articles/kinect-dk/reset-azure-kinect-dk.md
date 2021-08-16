---
title: Restablecimiento de Azure Kinect DK
description: Describe cómo restablecer un dispositivo de Azure Kinect DK a su imagen de fábrica.
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reset
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2020
ms.locfileid: "77201905"
---
# <a name="reset-azure-kinect-dk"></a>Restablecimiento de Azure Kinect DK

Es posible que se produzca una situación en la que tenga que volver a restablecer el dispositivo Azure Kinect DK a su imagen de fábrica (por ejemplo, si una actualización de firmware no se instaló correctamente).

1. Apague el dispositivo Azure Kinect DK. Para ello, quite el cable USB y el cable de alimentación.
  ![Diagrama que muestra la ubicación del tornillo que cubre el botón de restablecimiento.](media/reset-azure-kinect-dk-diagram.png)
1. Para buscar el botón de restablecimiento, quite el tornillo que se encuentra en el bloqueo de montaje del trípode.
1. Vuelva a conectar el cable de alimentación.
1. Inserte la punta de un clip estirado en el orificio de tornillo vacío del bloqueo de montaje del trípode.
1. Use el clip para mantener presionado con suavidad el botón de restablecimiento.
1. Mientras mantiene presionado el botón de restablecimiento, vuelva a conectar el cable USB.
1. Al cabo de unos tres segundos, la luz del indicador de electricidad cambia a ámbar. Cuando cambie la luz, suelte el botón de restablecimiento.  
   
   Después de soltarlo, la luz del indicador de energía parpadeará en color blanco y ámbar mientras se restablece el dispositivo. 
1. Espere a que la luz del indicador de energía se vuelva blanco sólido.
1. Vuelva a colocar el tornillo de bloqueo de montaje del trípode, sobre el botón de restablecimiento.
1. Use Azure Kinect Viewer para comprobar que se ha restablecido el firmware. Para ello, inicie [Azure Kinect Viewer](azure-kinect-viewer.md) y, después, seleccione **Device firmware version info** (Información de versión de firmware del dispositivo) para ver la versión de firmware que está instalada en Azure Kinect DK.

Asegúrese siempre de tener instalado el firmware más reciente en el dispositivo. Para obtener la versión de firmware más reciente, use la herramienta de firmware de Azure Kinect. Para más información sobre cómo comprobar el estado del firmware, consulte [Comprobación de la versión de firmware del dispositivo](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Temas relacionados

- [Acerca de Azure Kinect DK](about-azure-kinect-dk.md)
- [Configuración de Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Especificaciones de hardware de Azure Kinect DK: entorno operativo](hardware-specification.md#operating-environment)
- [Herramienta de firmware de Azure Kinect](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Sincronización entre varios dispositivos de Azure Kinect DK](multi-camera-sync.md)
