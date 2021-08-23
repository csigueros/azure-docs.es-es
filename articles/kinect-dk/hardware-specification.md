---
title: Especificaciones de hardware de Azure Kinect DK
description: Comprenda los componentes, las especificaciones y las funcionalidades de Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: article
keywords: azure, kinect, specs, hardware, DK, capabilities, depth, color, RGB, IMU, microphone, array, depth
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b5ec67537fe0bd8732b4068c0ed52861ed8f2c1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2021
ms.locfileid: "116986085"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Especificaciones de hardware de Azure Kinect DK

En este artículo se proporcionan detalles sobre cómo el hardware de Azure Kinect integra la última tecnología de sensor de Microsoft en un único accesorio conectado por USB.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Términos

Estos términos abreviados se usan en todo este artículo.

- NFOV (Modo de profundidad de campo de visión estrecho)
- WFOV (Modo de profundidad de campo de visión ancho)
- FOV (Campo de visión)
- FPS (Fotogramas por segundo)
- IMU (Unidad de medición inercial)
- FoI (Campo de interés)

## <a name="product-dimensions-and-weight"></a>Dimensiones y peso del producto

El dispositivo Azure Kinect tiene las siguientes dimensiones de tamaño y peso.

- **Dimensiones**: 103 x 39 x 126 mm
- **Peso**:440 g

![Dimensiones de Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

Hay un archivo STEP para el dispositivo Azure Kinect disponible [aquí](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/assets). 

## <a name="operating-environment"></a>Entorno operativo

Azure Kinect DK está diseñado para desarrolladores y empresas comerciales que operan en las siguientes condiciones ambientales:

- **Temperatura**: 10-25<sup>0</sup>C
- **Humedad**: humedad relativa del 8-90 % (sin condensar)

> [!NOTE]
> El uso en otras condiciones ambientales podría provocar un error en el dispositivo o un funcionamiento incorrecto. Estas condiciones ambientales son aplicables al entorno inmediato del dispositivo en todas las condiciones operativas. Cuando se usa con un receptáculo externo, se recomienda un control activo de la temperatura u otras soluciones de enfriamiento para asegurarse de que el dispositivo se mantenga dentro de estos intervalos. El diseño del dispositivo incluye un canal de refrigeración entre la sección delantera y la cubierta trasera. Cuando implemente el dispositivo, asegúrese de que este canal de refrigeración no esté obstruido.

Consulte más [información de seguridad](https://support.microsoft.com/help/4023454/safety-information) del producto.

## <a name="depth-camera-supported-operating-modes"></a>Modos de funcionamiento admitidos en la cámara de profundidad

Azure Kinect DK integra una cámara de profundidad TOF (tiempo de vuelo; del inglés, Time-of-Flight) de 1 megapíxel diseñada por Microsoft mediante el [sensor de imagen presentado en el ISSCC 2018](/windows/mixed-reality/ISSCC-2018). La cámara de profundidad admite los modos que se indican a continuación:

 | Modo            | Solución | FoI       | FPS                | Intervalo de funcionamiento* | Tiempo de exposición |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV sin binning   | 640x576    | 75°x65°   | 0, 5, 15, 30       | 0,5 - 3,86 m       | 12,8 ms        |
| NFOV 2x2 con binning (SW) | 320x288    | 75°x65°   | 0, 5, 15, 30       | 0,5 - 5,46 m       | 12,8 ms        |
| WFOV 2x2 con binning | 512x512    | 120°x120° | 0, 5, 15, 30       | 0,25 - 2,88 m      | 12,8 ms        |
| WFOV sin binning   | 1024x1024  | 120°x120° | 0, 5, 15           | 0,25 - 2,21 m      | 20,3 ms        |
| IR pasivo      | 1024x1024  | N/D       | 0, 5, 15, 30       | N/D              | 1,6 ms         |

\*15 % a 95 % de reflectividad a 850 nm, 2,2 μW/cm<sup>2</sup>/nm, desviación estándar de error aleatorio ≤ 17 mm, error sistemático típico < 11 mm + 0,1 % de distancia sin interferencias de múltiples rutas. Se puede proporcionar profundidad fuera del intervalo de funcionamiento indicado anteriormente. Depende de la reflectividad de un objeto.

## <a name="color-camera-supported-operating-modes"></a>Modos de funcionamiento admitidos con la cámara de color

Azure Kinect DK incluye un sensor de obturador CMOS OV12A10 de 12MP. Los modos operativos nativos se enumeran a continuación:

|             Resolución de cámara RGB (HxV)  |          Relación de aspecto  |          Opciones de formato   |          Velocidades de fotogramas (FPS)  |          FOV nominal (HxV) (posterior al procesado)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840x2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1920x1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1280x720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90°x59°                              |
|       4096x3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90°x74,3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90°x74,3°                            |

La cámara RGB admite la clase de vídeo USB y se puede usar sin Sensor SDK. Espacio de colores de la cámara RGB: intervalo completo BT.601 [0-255]. La [submuestra de intensidad de color](https://en.wikipedia.org/wiki/Chroma_subsampling) MJPEG es 4:2:2.

> [!NOTE]
> Sensor SDK puede proporcionar imágenes de color en el formato de píxeles de BGRA. No se trata de un modo nativo que admite el dispositivo y provoca una carga adicional de la CPU cuando se usa. La CPU del host se utiliza para convertir las imágenes MJPEG recibidas del dispositivo.

## <a name="rgb-camera-exposure-time-values"></a>Valores de tiempo de exposición de la cámara RGB

A continuación se muestra la asignación de los valores aceptables de exposición manual de la cámara RGB:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2.500|   2.500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Tiempo sin procesar del sensor de profundidad

Modo de profundidad | IR <br>Pulsos | Pulso <br>Ancho  | Inactivo <br>Puntos| Inactivo | Exposure <br> Time
-|-|-|-|-|-
NFOV sin binning <br>  NFOV 2xx con binning <br> WFOV 2x2 con binning | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV sin binning                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Campo de visión de la cámara

En la imagen siguiente se muestra el campo de visión de la cámara RGB y la profundidad, o los ángulos que los sensores "ven". En este diagrama se muestra la cámara RGB en modo 4:3.

![FOV de cámara](./media/resources/hardware-specs-media/camera-fov.png)

En esta imagen se muestra el campo de visión de la cámara, tal como se ve desde el frontal a una distancia de 2000 mm.

![Frontal FOV de la cámara](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Cuando la profundidad está en modo NFOV, la cámara RGB tiene mejor superposición de píxeles en resoluciones 4:3 que en 16:9.

## <a name="motion-sensor-imu"></a>Sensor de movimiento (IMU)

La unidad de medición inercial (IMU) insertada es un LSM6DSMUS e incluye un acelerómetro y un giroscopio. El acelerómetro y el giroscopio se muestrean simultáneamente a 1,6 kHz. Las muestras se envían al host en 208 Hz.

## <a name="microphone-array"></a>Varios micrófonos

Azure Kinect DK inserta una matriz circular de siete micrófonos de alta calidad que se identifica como un dispositivo estándar de audio USB clase 2.0. Se puede acceder a los siete canales. Las especificaciones de rendimiento son las siguientes:

- Sensibilidad:-22 dBFS (94 dB SPL, 1 kHz)
- Relación señal a ruido > 65 dB
- Punto de sobrecarga acústico: 116 dB

![Burbuja del micrófono](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK es un dispositivo compuesto USB3 que expone los siguientes puntos de conexión de hardware al sistema operativo:

El identificador de proveedor es 0x045E (Microsoft). A continuación se muestra la tabla de identificadores de productos:

|    Interfaz USB        |    IP de PNP    |     Notas            |
|-------------------------|--------------|----------------------|
|    Concentrador USB 3.1 Gen1    |    0x097A    |    Concentrador principal    |
|    Concentrador USB 2.0         |    0x097B    |    USB HS          |
|    Cámara de profundidad       |    0x097C    |    USB 3.0            |
|    Cámara de color       |    0x097D    |    USB 3.0            |
|    Micrófonos        |    0x097E    |    USB HS          |

## <a name="indicators"></a>Indicadores

El dispositivo tiene un indicador del flujo de la cámara en la parte frontal del dispositivo que se puede deshabilitar mediante programación con Sensor SDK.

El LED de estado situado detrás del dispositivo indica su estado:

| Cuando la luz está:     | Significa que:                                                   |
|-----------------------|------------------------------------------------------------|
| Blanco fijo           | El dispositivo está encendido y funciona correctamente.                         |
| Blanco intermitente        | El dispositivo está encendido, pero no tiene una conexión de datos USB 3.0.   |
| Ámbar intermitente        | El dispositivo no tiene suficiente energía para funcionar.               |
| Ámbar intermitente blanco  | Actualización de firmware o recuperación en curso.                    |

## <a name="power-device"></a>Alimentación del dispositivo

El dispositivo se puede alimentar de dos maneras:

1. Uso de la fuente de alimentación integrada. El conector de alimentación tiene un diámetro exterior de 4,5 mm con un diámetro interior de 3,0 mm y un diámetro de clavija de 0,6 mm.
2. Con un cable de tipo C a tipo C tanto para alimentación como para datos.

No se incluye un cable de tipo C a tipo C con Azure Kinect DK.

> [!NOTE]
> - El cable de la fuente de alimentación integrada es un cable USB tipo A a un conector cilíndrico con un solo borne. Use la fuente de alimentación de pared proporcionada con este cable. El dispositivo es capaz de extraer más potencia de la que pueden proporcionar dos tipos USB estándar.
> - Los cables USB son importantes y se recomienda usar cables de alta calidad y verificar la funcionalidad antes de implementar la unidad de forma remota.

> [!TIP]
> Para seleccionar un buen cable de tipo C a tipo C:
> - El [cable certificado para USB](https://www.usb.org/products) debe admitir tanto datos como alimentación.
> - Un cable pasivo debe medir menos 1,5 m. Si es más largo, use un cable activo. 
> - El cable debe admitir al menos 1,5 A. En caso contrario, debe conectar una fuente de alimentación externa.

Compruebe el cable:

- Conecte el dispositivo con el cable al equipo host.
- Compruebe que todos los dispositivos se enumeran correctamente en el administrador de dispositivos de Windows. Las cámaras de profundidad y RGB deben aparecer como se muestra en el ejemplo siguiente.

  ![Azure Kinect DK en Administrador de dispositivos](./media/resources/hardware-specs-media/device-manager.png)

- Compruebe que el cable puede transmitir de forma confiable en todos los sensores de Azure Kinect Viewer, con la siguiente configuración:

  - Cámara de profundidad: NFOV sin binning
  - Cámara RGB: 2160p
  - Micrófonos e IMU habilitados

## <a name="what-does-the-light-mean"></a>Significado de las luces

El indicador de energía es un LED situado la parte posterior de Azure Kinect DK. El color del LED cambia en función del estado del dispositivo.

![La imagen muestra la parte posterior de Azure Kinect DK. Hay tres llamadas numeradas: una para un indicador LED y debajo de ella, dos para los cables.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

En esta ilustración se etiquetan los componentes siguientes:

1. Indicador de alimentación
1. Cable de alimentación (conectado a una fuente de alimentación)
1. Cable de datos USB-C (conectado al equipo)

Asegúrese de que los cables estén conectados tal y como se muestra. Después, consulte la tabla siguiente para obtener información sobre lo que indican los distintos estados de la luz de alimentación.

|Cuando la luz está: |Significa que: |Y debería hacer: |
| ---| --- | --- |
|Blanco fijo |El dispositivo está encendido y funcionando correctamente. |Utilice el dispositivo. |
|Apagada |El dispositivo no está conectado al equipo. |Asegúrese de que el cable del conector de alimentación redondo esté conectado al dispositivo y al adaptador de alimentación USB.<br /><br />Asegúrese de que el cable USB-C esté conectado al dispositivo y al equipo. |
|Blanco intermitente |El dispositivo está encendido pero no tiene una conexión de datos USB 3.0. |Asegúrese de que el cable del conector de alimentación redondo esté conectado al dispositivo y al adaptador de alimentación USB.<br /><br />Asegúrese de que el cable USB-C esté conectado al dispositivo y a un puerto USB 3.0 del equipo.<br /><br />Conecte el dispositivo a otro puerto USB 3.0 del equipo.<br /><br />En el equipo, abra Administrador de dispositivos (**Inicio** > **Panel de control** > **Administrador de dispositivos**) y compruebe que el equipo tiene un controlador de host USB 3.0 compatible. |
|Ámbar intermitente |El dispositivo no tiene suficiente energía para funcionar. |Asegúrese de que el cable del conector de alimentación redondo esté conectado al dispositivo y al adaptador de alimentación USB.<br /><br />Asegúrese de que el cable USB-C esté conectado al dispositivo y al equipo. |
|Ámbar y después blanco intermitente |El dispositivo está encendido y está recibiendo una actualización de firmware, o el dispositivo está restaurando la configuración de fábrica. |Espere a que la luz del indicador de energía se vuelva blanco sólido. Para más información, consulte [Restablecimiento de Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Consumo de energía

Azure Kinect DK consume hasta 5,9 W; el consumo de energía específico depende del uso.

## <a name="calibration"></a>Calibración

Azure Kinect DK está calibrado de fábrica. Los parámetros de calibración para los sensores visuales e inerciales se pueden consultar mediante programación a través de Sensor SDK.

## <a name="device-recovery"></a>Recuperación del dispositivo

El firmware del dispositivo se puede restablecer al firmware original mediante el botón situado debajo del PIN de bloqueo.

![Botón de recuperación de Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Para recuperar el dispositivo, consulte las [instrucciones aquí](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Pasos siguientes

- [Uso de Sensor SDK de Azure Kinect](about-sensor-sdk.md)
- [Configuración del hardware](set-up-azure-kinect-dk.md)