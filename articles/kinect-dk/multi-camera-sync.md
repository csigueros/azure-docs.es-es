---
title: Sincronización de varios dispositivos de Azure Kinect DK
description: En este artículo se exploran las ventajas de la sincronización de varios dispositivos, así como la manera de configurar los dispositivos para la sincronización.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure, kinect, especificaciones, hardware, DK, funcionalidades, profundidad, color, RGB, IMU, micrófono, matriz, profundidad
ms.openlocfilehash: 5e4936e5c7b795745b7f1b131efbf99a562d8ed0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254691"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Sincronización de varios dispositivos de Azure Kinect DK

Cada dispositivo de Azure Kinect DK incluye puertos de sincronización de 3,5 mm (**Sync in** y **Sync out**) que se pueden usar para vincular varios dispositivos. Después de conectar los dispositivos, el software puede coordinar la temporización del desencadenador entre ellos. 

En este artículo se describe cómo conectar y sincronizar los dispositivos.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Ventajas de usar varios dispositivos de Azure Kinect DK

Hay muchos motivos para usar varios dispositivos de Azure Kinect DK, entre los que se incluyen los siguientes:

- Rellenar las oclusiones. Aunque las transformaciones de datos de Azure Kinect DK producen una sola imagen, las dos cámaras (profundidad y RGB) están separadas por una distancia pequeña. El desplazamiento hace posible las oclusiones. Una oclusión se produce cuando un objeto del primer plano bloquea la vista de parte de un objeto del segundo plano para una de las dos cámaras de un dispositivo. En la imagen de color resultante, el objeto de primer plano parece arrojar una sombra sobre el objeto en segundo plano.  
   Por ejemplo, en el diagrama siguiente, la cámara del lado izquierdo ve el píxel gris "P2". Sin embargo, el objeto blanco del primer plano bloquea el haz IR de la cámara del lado derecho. La cámara del lado derecho no tiene datos para "P2".  
   ![En el diagrama se muestran dos cámaras que se dirigen al mismo punto; una de ellas, bloqueada.](./media/occlusion.png)  
   Los dispositivos sincronizados adicionales pueden proporcionar los datos ocluidos.
- Examinar objetos en tres dimensiones.
- Aumentar la velocidad de fotogramas efectiva a un valor superior a 30 fotogramas por segundo (FPS).
- Capturar varias imágenes de color 4K de la misma escena, todas alineadas en 100 microsegundos (&mu;s) del centro de exposición.
- Aumentar la cobertura de la cámara en el espacio.

## <a name="plan-your-multi-device-configuration"></a>Planeación de la configuración de varios dispositivos

Antes de empezar, asegúrese de revisar [Especificaciones de hardware de Azure Kinect DK](hardware-specification.md) y [Cámara de profundidad de Azure Kinect DK](depth-camera.md).

> [!NOTE]  
> Quite la tapa de plástico externa para quedar expuestos los conectores de entrada y salida de sincronización.

### <a name="select-a-device-configuration"></a>Selección de una configuración de dispositivo

Puede usar cualquiera de los enfoques siguientes para la configuración del dispositivo:

- **Configuración en cadena de margarita**. Sincronice un dispositivo maestro y hasta ocho dispositivos subordinados.  
   ![Diagrama que muestra cómo conectar dispositivos de Azure Kinect DK en una configuración en cadena de margarita.](./media/multicam-sync-daisychain.png)
- **Configuración en estrella**. Sincronice un dispositivo maestro y hasta dos dispositivos subordinados.  
   ![Diagrama que muestra cómo configurar varios dispositivos de Azure DK en una configuración en estrella.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Uso de un desencadenador de sincronización externo

En ambas configuraciones, el dispositivo maestro ofrece la señal desencadenante para los dispositivos subordinados. Sin embargo, puede usar un origen externo personalizado para el desencadenador de sincronización. Por ejemplo, puede usar esta opción para sincronizar capturas de imagen con otro equipo. Tanto en la configuración en la cadena de margarita como en la configuración en estrella, el origen del desencadenador externo se conecta al dispositivo maestro.

El origen del desencadenador externo debe funcionar de la misma manera que el dispositivo maestro. Debe proporcionar una señal de sincronización que tenga las siguientes características:

- Activo alto
- Ancho de pulso: mayor que 8 &mu;s.
- 5 V TTL/CMOS
- Capacidad máxima de conducción: No menos de 8 miliamperios (mA)
- Frecuencias admitidas: Exactamente 30 FPS, 15 FPS y 5 FPS (la frecuencia de la señal VSYNC maestra de la cámara de color)

El origen del desencadenador debe enviar la señal al puerto **Sync in** del dispositivo maestro mediante un cable de audio de 3,5 mm. Puede usar un cable estéreo o mono. Azure Kinect DK conecta todas las fundas y anillos del conector del cable de audio y los conecta a tierra. Como se muestra en el diagrama siguiente, el dispositivo recibe la señal de sincronización solo de la punta del conector.

![Configuraciones de cable para una señal de desencadenador externa](./media/resources/camera-trigger-signal.jpg)

Para obtener más información sobre cómo trabajar con equipos externos, consulte [Uso de la grabadora de Azure Kinect con dispositivos sincronizados externos](record-external-synchronized-units.md).

> [!NOTE]  
> La salida de sincronización es la señal VSync de la cámara RGB. Las marcas de tiempo de todos los dispositivos se establecen en cero y en recuento. Microsoft no ha caracterizado el ancho mínimo y máximo del pulso de sincronización y recomienda imitar el pulso generado por la salida de sincronización de Azure Kinect DK.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Planeación de la configuración de la cámara y configuración del software

Para obtener información sobre cómo configurar el software para controlar las cámaras y usar los datos de imagen, consulte [SDK del Sensor de Azure Kinect](about-sensor-sdk.md).

En esta sección se describen varios factores que afectan a los dispositivos sincronizados (pero no a los dispositivos individuales). El software debe tener en cuenta estos factores.

#### <a name="exposure-considerations"></a>Consideraciones de exposición
Si desea controlar el tiempo preciso de cada dispositivo, se recomienda usar una configuración de exposición manual. En la configuración de exposición automática, cada cámara de color puede cambiar dinámicamente la exposición real. Dado que la exposición afecta al tiempo, tales cambios provocan que las cámaras pierdan la sincronización rápidamente.

En el bucle de captura de imágenes, evite establecer repetidamente la misma configuración de exposición. Llame a la API solo una vez cuando sea necesario.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Evitación de interferencias entre varias cámaras de profundidad

Cuando varias cámaras de profundidad muestran campos de visión en superposición, cada cámara debe mostrar su propio láser asociado. Para evitar que los láseres interfieran entre sí, las capturas de la cámara deben desplazarse entre ellas 160 μs o más.

Para cada captura de la cámara de profundidad, el láser se enciende nueve veces y está activo solo durante 125 &mu;s cada vez. A continuación, el láser está inactivo durante 1450 &mu;s o 2390 &mu;s, según el modo de funcionamiento. Este comportamiento significa que el punto inicial del cálculo de desplazamiento es de 125 &mu;s.

Además, las diferencias entre el reloj de la cámara y el reloj de firmware del dispositivo aumentan el desplazamiento mínimo a 160 &mu;s. Para calcular un desplazamiento más preciso para la configuración, tenga en cuenta el modo de profundidad que está usando y consulte la [tabla del tiempo sin procesar del sensor de profundidad](hardware-specification.md#depth-sensor-raw-timing). Con los datos de esta tabla, puede calcular el desplazamiento mínimo (el tiempo de exposición de cada cámara) mediante la siguiente ecuación:

> *Tiempo de exposición* = (*impulsos de IR* &times; *ancho del pulso*) + (*períodos de inactividad* &times; *tiempo de inactividad*)

Cuando se usa un desplazamiento de 160 &mu;s, se pueden configurar hasta nueve cámaras de profundidad adicionales para que cada láser se encienda mientras los demás láser están inactivos.

En el software, use ```depth_delay_off_color_usec``` o ```subordinate_delay_off_master_usec``` para asegurarse de que cada láser de IR se active en su propia ventana de 160 &mu;s o que tenga un campo de visión diferente.

> [!NOTE]  
> El ancho real del pulso es 125&mu;s; sin embargo, está fijado en 160 us para dar cierto margen. Tomando NFOV UNBINNED como ejemplo, cada pulso de 125&mu;s va seguido de un período de inactividad de 1450&mu;s. Al sumar estos (9 x 125) + (8 x 1450) da como resultado el tiempo de exposición de 12,8 ms. Para intercalar las exposiciones de los dos dispositivos, asegúrese de que el primer pulso de la segunda cámara entre dentro del primer período de inactividad de la primera cámara. El retraso entre la primera y la segunda cámara puede ser tan pequeño como 125&mu;us (el ancho de un pulso), pero recomendamos cierto margen; por lo tanto, 160&mu;s. Con 160&mu;s se pueden intercalar los períodos de exposición de 10 cámaras como máximo.

## <a name="prepare-your-devices-and-other-hardware"></a>Preparación de los dispositivos y demás hardware

Además de varios dispositivos de Azure Kinect DK, es posible que tenga que obtener equipos host adicionales y otro hardware para admitir la configuración que desea crear. Utilice la información de esta sección para asegurarse de que todos los dispositivos y el hardware estén listos antes de comenzar la configuración.

### <a name="azure-kinect-dk-devices"></a>Dispositivos de Azure Kinect DK

Para cada dispositivo de Azure Kinect DK que desee sincronizar, haga lo siguiente:

- Asegúrese de tener instalado el firmware más reciente en el dispositivo. Para obtener más información sobre cómo actualizar los dispositivos, vaya a [Actualización del firmware de Azure Kinect DK](update-device-firmware.md). 
- Quite la cubierta del dispositivo para ver los puertos de sincronización.
- Anote el número de serie de cada dispositivo. Usará este número más adelante en el proceso de configuración.

### <a name="host-computers"></a>Equipos host

Normalmente, cada Azure Kinect DK usa su propio equipo host. Puede usar un controlador de host dedicado, en función de cómo use el dispositivo y la cantidad de datos que se transfieran a través de la conexión USB. 

Asegúrese de que el SDK del Sensor de Azure Kinect esté instalado en cada equipo host. Para obtener más información sobre cómo instalar el SDK del Sensor, vaya a [Inicio rápido: Configuración de Azure Kinect DK](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Equipos Linux: Memoria USB en Ubuntu

De manera predeterminada, los equipos host basados en Linux asignan al controlador USB solo 16 MB de memoria de kernel para controlar las transferencias USB. Esta cantidad suele ser suficiente para admitir un único dispositivo de Azure Kinect DK. Sin embargo, para admitir varios dispositivos, el controlador USB tiene que disponer de más memoria. Para aumentar la memoria, siga estos pasos:

1. Edite /**etc/default/grub**.
1. Busque la línea siguiente:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Reemplácela por esta línea:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Estos comandos establecen la memoria USB en 32 MB. Este es un ejemplo de configuración con el doble del valor predeterminado. Puede establecer un valor mucho mayor, según sea necesario para su solución.
1. Ejecute **sudo update-grub**.
1. Reinicie el equipo.

### <a name="cables"></a>Cables

Para conectar los dispositivos entre sí y con los equipos host, debe usar cables macho a macho de 3,5 mm (también conocido como cable de audio de 3,5 mm). Los cables deben tener una longitud inferior a 10 metros y pueden ser estéreo o mono.

El número de cables que debe tener dependerá del número de dispositivos que use y de la configuración específica del dispositivo. No se incluyen cables con el dispositivo de Azure Kinect DK. Deben comprarlos por separado.

Si conecta los dispositivos en configuración en estrella, también debe tener un divisor de auriculares.

## <a name="connect-your-devices"></a>Conectar dispositivos

**Para conectar dispositivos de Azure Kinect DK en una configuración en cadena de margarita**

1. Conecte cada Azure Kinect DK a la alimentación.
1. Conecte cada dispositivo a su equipo host. 
1. Seleccione un dispositivo como dispositivo maestro y conecte un cable de audio de 3,5 mm a su puerto **Sync out**.
1. Conecte el otro extremo del cable en el puerto **Sync in** del primer dispositivo subordinado.
1. Para conectar otro dispositivo, conecte otro cable en el puerto **Sync out** del primer dispositivo subordinado y en el puerto **Sync in** del siguiente dispositivo.
1. Repita el paso anterior hasta haber conectado todos los dispositivos. El último dispositivo debe tener una sola conexión de cable. El puerto **Sync out** debe estar vacío.

**Para conectar dispositivos de Azure Kinect DK en una configuración en estrella**

1. Conecte cada Azure Kinect DK a la alimentación.
1. Conecte cada dispositivo a su equipo host. 
1. Seleccione un dispositivo para que sea el dispositivo maestro y conecte el extremo único del divisor de auriculares en el puerto **Sync out**.
1. Conecte los cables de audio de 3,5 mm a los extremos "divididos" del divisor de auriculares.
1. Conecte el otro extremo de cada cable en el puerto **Sync in** de uno de los dispositivos subordinados.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Verificación de que los dispositivos están conectados y en comunicación

Para verificar que los dispositivos están conectados correctamente, use [Azure Kinect Viewer](azure-kinect-viewer.md). Repita este procedimiento según sea necesario para probar cada dispositivo subordinado en combinación con el dispositivo maestro

> [!IMPORTANT]  
> Para este procedimiento, debe conocer el número de serie de cada Azure Kinect DK.

1. Abra dos instancias de Azure Kinect Viewer.
1. En **Open Device** (Abrir dispositivo), seleccione el número de serie del dispositivo subordinado que desea probar.  
   ![Abrir dispositivo](./media/open-devices.png)
   > [!IMPORTANT]  
   > Para obtener una alineación precisa de captura de imágenes entre todos los dispositivos, debe iniciar el dispositivo maestro en último lugar.  
1. En **Sincronización externa**, seleccione **Sub**.  
   ![Inicio de cámara subordinada](./media/sub-device-start.png)
1.  Seleccione **Inicio**.  
    > [!NOTE]  
    > Dado que se trata de un dispositivo subordinado, Azure Kinect Viewer no muestra ninguna imagen una vez que se inicia el dispositivo. No se muestra ninguna imagen hasta que el dispositivo subordinado recibe una señal de sincronización del dispositivo maestro.
1. Una vez que se ha iniciado el dispositivo subordinado, use la otra instancia de Azure Kinect Viewer para abrir el dispositivo maestro.
1. En **Sincronización externa**, seleccione **Maestro**.
1. Seleccione **Inicio**.

Cuando se inicia el dispositivo maestro de Azure Kinect, ambas instancias de Azure Kinect Viewer deberían mostrar imágenes.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Calibración de los dispositivos como conjunto sincronizado

Después de verificar que los dispositivos se comunican correctamente, está listo para calibrarlos para generar imágenes en un único dominio.

En un único dispositivo, las cámaras de profundidad y RGB están calibradas de fábrica para funcionar de manera conjunta. Sin embargo, cuando varios dispositivos tienen que funcionar juntos, tienen que calibrarse para determinar cómo transformar una imagen del dominio de la cámara que la capturó al dominio de la cámara que desea usar para procesar imágenes.

Hay varias opciones para la calibración cruzada de dispositivos. Microsoft proporciona la [muestra de código de pantalla verde de GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), que usa el método OpenCV. El archivo Léame para este ejemplo de código proporciona más detalles e instrucciones para calibrar los dispositivos.

Para obtener más información sobre la calibración, consulte [Uso de las funciones de calibración de Azure Kinect](use-calibration-functions.md).

## <a name="next-steps"></a>Pasos siguientes

Después de configurar los dispositivos sincronizados, también puede aprender a usar la
> [!div class="nextstepaction"]
> [API de grabación y reproducción del SDK del Sensor Azure Kinect](record-playback-api.md)

## <a name="related-topics"></a>Temas relacionados

- [Acerca del SDK del Sensor de Azure Kinect](about-sensor-sdk.md)
- [Especificaciones de hardware de Azure Kinect DK](hardware-specification.md) 
- [Inicio rápido: Configuración de Azure Kinect DK](set-up-azure-kinect-dk.md) 
- [Actualización del firmware de Azure Kinect DK](update-device-firmware.md) 
- [Restablecimiento de Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect Viewer](azure-kinect-viewer.md) 
