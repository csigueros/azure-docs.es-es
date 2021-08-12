---
title: 'Inicio rápido: Configuración de Azure Kinect DK'
description: En esta guía de inicio rápido se proporcionan instrucciones sobre cómo configurar el hardware de Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, set up, hardware, quick, usb, power, viewer, sensor, streaming, setup, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2020
ms.locfileid: "77211285"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Inicio rápido: Configuración de Azure Kinect DK

En esta guía de inicio rápido se proporcionan instrucciones sobre cómo configurar Azure Kinect DK. Le mostraremos cómo probar la visualización del flujo del sensor y usar [Azure Kinect Viewer](azure-kinect-viewer.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="system-requirements"></a>Requisitos del sistema

Compruebe los [requisitos del sistema](system-requirements.md) para verificar que la configuración del equipo host cumple todos los requisitos mínimos de Azure Kinect DK.

## <a name="set-up-hardware"></a>Configuración del hardware

> [!NOTE]
> Asegúrese de quitar la película protectora de la cámara antes de usar el dispositivo.

1. Enchufe el conector de alimentación a la clavija de alimentación situada en la parte posterior del dispositivo. Conecte el adaptador de alimentación USB al otro extremo del cable y, a continuación, conecte el adaptador a una toma de corriente.
2. Conecte el cable de datos USB en el dispositivo y, después, a un puerto USB 3.0 en el PC.
   >[!NOTE]
   >Para obtener los mejores resultados, conecte el cable directamente al dispositivo y al equipo. Evite el uso de extensiones o adaptadores adicionales en la conexión.

3. Compruebe que el LED de indicador de energía (junto al cable USB) es blanco sólido.
  
   El dispositivo tarda unos segundos en encenderse. El dispositivo ya está listo para usarse cuando apague el indicador de flujo LED frontal.  

   Para más información sobre el indicador de energía LED, consulte [Significado de las luces](hardware-specification.md#what-does-the-light-mean).

    ![Características completas del dispositivo](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Descarga del SDK

1. Seleccione el vínculo para [descargar el SDK](sensor-sdk-download.md).
2. Instalación del SDK en el equipo

## <a name="update-firmware"></a>Actualización del firmware

Para que funcione correctamente, el SDK requiere la versión más reciente del firmware del dispositivo. Para comprobar y actualizar la versión de firmware, siga los pasos descritos en [Actualización del firmware de Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Comprobación de los datos de flujo de dispositivo

1. Inicie [Azure Kinect Viewer](azure-kinect-viewer.md). Puede iniciar esta herramienta mediante uno de estos métodos:
   - Puede iniciar el visor desde la línea de comandos o haciendo doble clic en el archivo ejecutable. El archivo, `k4aviewer.exe`, reside en el directorio de herramientas del SDK (por ejemplo, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, donde `X.Y.Z` es la versión instalada del SDK).
   - Puede iniciar Azure Kinect Viewer desde el menú de **inicio** del dispositivo.
2. En Azure Kinect Viewer, seleccione **Open Device** > **Start** (Abrir dispositivo > Iniciar).

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Compruebe que la herramienta visualiza cada flujo de sensor:
   - Cámara de profundidad
   - Cámara de color
   - Cámara de infrarrojos
   - IMU
   - Micrófonos

    ![Herramienta de visualización](./media/quickstarts/visualization-tool.png)

Ya ha terminado la configuración de Azure Kinect DK. Ahora puede empezar a desarrollar la aplicación o a los servicios de integración.

Si tiene algún problema, consulte [Solución de problemas](troubleshooting.md).

## <a name="see-also"></a>Vea también

- [Información del hardware de Azure Kinect DK](hardware-specification.md)
- [Actualización del firmware del dispositivo](update-device-firmware.md)
- Obtenga más información sobre [Azure Kinect Viewer](azure-kinect-viewer.md).

## <a name="next-steps"></a>Pasos siguientes

Después de que Azure Kinect DK esté listo y en funcionamiento, también puede aprender lo siguiente:
> [!div class="nextstepaction"]
> [Grabación de transmisiones de sensores en un archivo](record-sensor-streams-file.md)
