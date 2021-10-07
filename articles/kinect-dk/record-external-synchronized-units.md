---
title: Uso de la grabadora de Azure Kinect con dispositivos sincronizados externos
description: Aprenda a grabar datos desde dispositivos configurados para la sincronización externa mediante la grabadora de Azure Kinect.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, sensor, viewer, external sync, phase delay, depth, RGB, camera, audio cable, recorder
ms.openlocfilehash: fd751c9979cf2dc1fa2698deff0e2b207fe518c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771391"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Uso de la grabadora de Azure Kinect con dispositivos sincronizados externos

En este artículo se proporcionan instrucciones sobre el modo en que la [grabadora de Azure Kinect](azure-kinect-recorder.md) puede grabar los datos de los dispositivos configurados para la sincronización externa.

## <a name="prerequisites"></a>Requisitos previos

- [Configure varias unidades de Azure Kinect DK para la sincronización externa](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Restricciones de la sincronización externa

- El dispositivo maestro no puede tener conectado un cable SYNC IN.
- El dispositivo maestro debe transmitir a la cámara RGB para permitir la sincronización.
- Todas las unidades deben usar la misma configuración de cámara (velocidad de fotogramas y resolución).
- Todas las unidades deben ejecutar el mismo firmware del dispositivo (consulte las instrucciones para [actualizar el firmware](update-device-firmware.md)).
- Todos los dispositivos subordinados deben iniciarse antes que el dispositivo maestro.
- Se debe establecer el mismo valor de exposición en todos los dispositivos.
- El valor de *Delay off master* (Retrasar maestro) de cada subordinado es relativo al dispositivo maestro.

## <a name="record-when-each-unit-has-a-host-pc"></a>Grabación cuando cada unidad tenga un equipo host

En el ejemplo siguiente, cada dispositivo tiene su propio equipo host dedicado.
Se recomienda conectar los dispositivos a equipos dedicados para evitar problemas con el ancho de banda USB y el uso de CPU o GPU.

### <a name="subordinate-1"></a>Subordinado 1

1. Configure la grabadora para la primera unidad.

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Comienza la espera del dispositivo.

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Subordinado 2

1. Configure la grabadora para la segunda unidad.

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Comienza la espera del dispositivo.

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Master

1. Inicie la grabación en el maestro.

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Espere hasta que termine la grabación.

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Grabación cuando hay varias unidades conectadas a un solo equipo host

Aunque puede tener varias unidades de Azure Kinect DK conectadas a un solo equipo host, pueden consumir mucho ancho de banda USB y proceso de host. Para reducir el consumo:

- Conecte cada dispositivo a su propio controlador host USB.
- Disponga de una GPU eficaz que pueda controlar el motor de profundidad de cada dispositivo.
- Grabe solo los sensores necesarios y use una velocidad de fotogramas inferior.

Inicie siempre los dispositivos subordinados primero y el maestro en último lugar.

## <a name="subordinate-1"></a>Subordinado 1

1. Inicie la grabación en el subordinado.

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. El dispositivo entra en estado de espera.

## <a name="master"></a>Master

1. Inicie el dispositivo maestro.

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Espere a que finalice la grabación.

## <a name="playing-recording"></a>Reproducción de la grabación

Puede usar el [visor de Azure Kinect](azure-kinect-viewer.md) para reproducir una grabación.



## <a name="tips"></a>Sugerencias

- Use la exposición manual para grabar las cámaras sincronizadas. La exposición automática de la cámara RGB puede afectar a la sincronización de la hora.
- El reinicio del dispositivo subordinado hará que se pierda la sincronización.
- Algunos [modos de cámara](hardware-specification.md#depth-camera-supported-operating-modes) admiten 15 fps como máximo. Se recomienda no mezclar modos/velocidades de fotogramas entre dispositivos.
- La conexión de varias unidades a un solo equipo puede saturar fácilmente el ancho de banda USB, así que considere la posibilidad de usar distintos equipos host para cada dispositivo. Preste atención también al proceso de CPU/GPU.
- Para mejorar la confiabilidad, deshabilite el micrófono y la IMU si no son necesarios.

Si tiene algún problema, consulte [Solución de problemas](troubleshooting.md).

## <a name="see-also"></a>Consulte también

- [Configuración de la sincronización externa](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Grabadora de Azure Kinect](azure-kinect-recorder.md) para ver la configuración de la grabadora e información adicional.
- [Visor de Azure Kinect](azure-kinect-viewer.md) para reproducir grabaciones o establecer las propiedades de la cámara RGB no disponibles mediante la grabadora.
- [Herramienta de firmware de Azure Kinect](azure-kinect-firmware-tool.md) para actualizar el firmware del dispositivo.
