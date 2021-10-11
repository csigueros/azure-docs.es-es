---
title: Creación de streaming en vivo con OBS Studio
description: Aprenda a crear streaming en vivo de Azure Media Services mediante el portal y OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: 43f1602b22a761461f84761ed3a3806397011ae2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389521"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Creación de un streaming en vivo de Azure Media Services con OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Con este inicio rápido podrá crear un evento en directo de Media Services mediante Azure Portal y difundirlo con Open Broadcasting Studio (OBS). Se supone que tiene una suscripción a Azure y que ha creado una cuenta de Media Services.

En este inicio rápido, trataremos lo siguiente:

- Configuración de un codificador local con OBS.
- Configuración de streaming en vivo.
- Configuración de las salidas de streaming en vivo.
- Ejecución de un punto de conexión de streaming predeterminado.
- Uso de Azure Media Player para ver streaming en vivo y la salida a petición.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Abra el explorador web y vaya a [Microsoft Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Configuración de un codificador local mediante OBS

1. Descargue la versión de OBS para su sistema operativo en el [sitio web de Open Broadcaster Software](https://obsproject.com/) e instálela.
1. Inicie la aplicación y manténgala abierta.

## <a name="run-the-default-streaming-endpoint"></a>Ejecución del punto de conexión de streaming predeterminado

1. En la lista de Media Services, seleccione **Streaming endpoints** (Puntos de conexión de streaming).

   ![Elemento de menú Streaming endpoints (Puntos de conexión de streaming)](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Si el estado del punto de conexión de streaming predeterminado es detenido, selecciónelo. Este paso le lleva a la página de ese punto de conexión.
1. Seleccione **Inicio**.

   ![Botón Start (Iniciar) del punto de conexión de streaming](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Configuración de streaming en vivo de Azure Media Services

1. Vaya a la cuenta de Azure Media Services en el portal y, luego, seleccione **Live streaming** (Streaming en vivo) en la lista de **Media Services**.

   ![Vínculo de streaming en vivo](media/live-events-obs-quickstart/select-live-streaming.png)
1. Seleccione **Add live event** (Agregar evento en directo) para crear un nuevo evento de streaming en vivo.

   ![Icono de Add live event (Agregar evento en directo)](media/live-events-obs-quickstart/add-live-event.png)
1. Escriba un nombre para el nuevo evento, por ejemplo, *TestLiveEvent*, en el campo **Live event name** (Nombre del evento en directo).

   ![Cuadro Live event name (Nombre del evento en directo)](media/live-events-obs-quickstart/live-event-name.png)
1. Escriba una descripción opcional del evento en el campo **Description** (Descripción).
1. Seleccione la opción **Pass-through – no cloud encoding** (Tránsito: sin codificación en la nube) básica.

   ![Opción de codificación en la nube](media/live-events-obs-quickstart/cloud-encoding.png)
1. Seleccione la opción **RTMP**.
1. Asegúrese de que está seleccionada la opción **No** en **Start live event** (Iniciar el evento en directo), para evitar que se le facture el evento en directo antes de que esté listo. (La facturación comenzará cuando se inicie el evento en directo).

   ![Opción Start live event (Iniciar el evento en directo)](media/live-events-obs-quickstart/start-live-event-no.png)
1. Seleccione el botón **Review + create** (Revisar y crear) para revisar la configuración.
1. Seleccione el botón **Create** (Crear) para crear el evento en directo. A continuación, volverá a la lista de eventos en directo.
1. Seleccione el vínculo al evento en directo que acaba de crear. Observe que el evento está detenido.
1. Mantenga esta página abierta en el explorador. Volveremos a él más adelante.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Configuración de streaming en vivo mediante OBS Studio

OBS comienza con una escena predeterminada pero sin datos de entrada seleccionados.

   ![Pantalla predeterminada de OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Incorporación de un origen de vídeo

1. En el panel **Sources** (Orígenes), haga clic en el icono **Add** (Agregar) para seleccionar un dispositivo de origen nuevo. Se abrirá el menú **Sources** (Orígenes).

1. Seleccione **Video Capture Device** (Dispositivo de captura de vídeo) en el menú del dispositivo de origen. Se abrirá el menú **Create/Select Source** (Crear o seleccionar origen).

   ![Menú de orígenes de OBS con un dispositivo de vídeo seleccionado](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Seleccione el botón de radio **Add Existing** (Agregar existente) y, después, **OK** (Aceptar). Se abrirá el menú **Properties for Video Device** (Propiedades de dispositivo de vídeo).

   ![Menú de origen de vídeo nuevo de OBS con la opción para agregar uno existente seleccionada](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. En la lista desplegable **Device** (Dispositivo), seleccione la entrada de vídeo que desea usar para la difusión. Deje el resto de la configuración intacta y haga clic en **OK** (Aceptar). El origen de entrada se agregará al panel **Sources** (Orígenes) y la vista de la entrada de vídeo se mostrará en el área **Preview** (Vista previa).

   ![Configuración de la cámara de OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Incorporación de un origen de audio

1. En el panel **Sources** (Orígenes), haga clic en el icono **Add** (Agregar) para seleccionar un dispositivo de origen nuevo. Se abrirá el menú Source Device (Dispositivo de origen).

1. Seleccione **Audio Input Capture** (Captura de entrada de audio) en el menú del dispositivo de origen. Se abrirá el menú **Create/Select Source** (Crear o seleccionar origen).

   ![Menú de orígenes de OBS con un dispositivo de audio seleccionado](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Seleccione el botón de radio **Add Existing** (Agregar existente) y, después, **OK** (Aceptar). Se abrirá el menú **Properties for Audio Input Capture** (Propiedades de captura de entrada de audio).

   ![Origen de audio de OBS con la adición de uno existente seleccionado](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. En la lista desplegable **Device** (Dispositivo), seleccione el dispositivo de captura de audio que desea usar para la difusión. Deje el resto de la configuración intacta y seleccione OK (Aceptar). El dispositivo de captura de audio se agregará al panel del mezclador de audio.

   ![Lista desplegable de selección de dispositivos de audio de OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>Configuración de la configuración de streaming y de codificación avanzada en OBS

En el siguiente procedimiento, volverá a Azure Media Services en el explorador para copiar la dirección URL de entrada que va a escribir en la configuración de salida.

1. En la página de Azure Media Services del portal, seleccione **Start** (Iniciar) para iniciar el evento de streaming en vivo. (La facturación comienza ahora).

   ![Icono Start (Iniciar)](media/live-events-obs-quickstart/start.png)
1. En **RTMP**, seleccione **RTMPS**.
1. En el cuadro **Input URL** (Dirección URL de entrada), copie la dirección URL en el portapapeles.

   ![Input URL (URL de entrada)](media/live-events-obs-quickstart/input-url.png)

1. Cambie a la aplicación OBS.

1. Haga clic en el botón **Settings** (Configuración) del panel **Controls** (Controles). Se abrirán las opciones de Settings (Configuración).

   ![Panel Controls (Controles) de OBS con la configuración seleccionada](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Seleccione **Stream** (Transmisión) en el menú **Settings** (Configuración).

1. En la lista desplegable **Service** (Servicio), seleccione Mostrar todo y, después, seleccione **Custom...** (Personalizado).

1. En el campo **Server** (Servidor), pegue la dirección URL de RTMPS que copió en el Portapapeles.

1. Escriba algo en el campo **Stream key** (Clave de transmisión).  En realidad, da igual lo que sea, pero debe tener un valor.

    ![Configuración de streaming de OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Seleccione **Output** (Salida) en el menú **Settings** (Configuración).

1. Seleccione la lista desplegable **Output Mode** (Modo de salida) en la parte superior de la página y elija **Advanced** (Opciones avanzadas) para tener acceso a todas las opciones de configuración del codificador disponibles.

1. Seleccione la pestaña **Streaming** para configurar el codificador.

1. Seleccione el codificador adecuado para su sistema.  Si el hardware admite la aceleración de GPU, elija entre NVIDIA **NVENC** H.264 o Intel **QuickSync** H.264. Si el sistema no tiene una GPU compatible, seleccione la opción de codificador de software **X264**.

#### <a name="x264-encoder-settings"></a>Configuración del codificador X264

1. Si ha seleccionado la opción de codificación **X264**, seleccione el cuadro de **Rescale Output** (Cambiar escala de salida). Seleccione 1920x1080 si está usando un evento en directo prémium en Media Services o 1280x720 si usa un evento en directo estándar (720P).  Si utiliza un evento en directo de tránsito básico o estándar, puede elegir cualquier resolución disponible.

1. Establezca la **velocidad de bits** en cualquier lugar entre 1500 kbps y 4000 kbps. Se recomiendan 2500 Kbps si usa un evento en directo de codificación estándar en 720P. Si utiliza un evento en directo prémium en 1080P, se recomienda 4000 kbps. Es posible que desee ajustar la velocidad de bits en función de las capacidades de CPU disponibles y el ancho de banda de la red para lograr la configuración de calidad deseada.

1. Escriba *2* en el campo **Keyframe interval** (Intervalo de fotogramas clave). El valor establece el intervalo de fotogramas clave en dos segundos, que controla el tamaño final de los fragmentos entregados a través de HLS o DASH desde Media Services. No establezca nunca el intervalo de fotogramas clave en un valor superior a cuatro segundos.  Si observa una latencia elevada al difundir, debe siempre volver a comprobar o informar a los usuarios de la aplicación que establezcan este valor en dos segundos. Al intentar lograr la entrega en directo con menor latencia, puede establecer este valor en un valor tan bajo como un segundo.

1. OPCIONAL: Establezca el valor preestablecido de uso de CPU en **veryfast** y ejecute algunos experimentos para ver si la CPU local puede controlar la combinación de velocidad de bits y preestablecida con suficiente sobrecarga. Intente evitar la configuración que daría como resultado un promedio de CPU superior al 80 % para evitar problemas durante el streaming en vivo. Para mejorar la calidad, puede probar con valores preestablecidos **más rápidos** y con los **rápidos** hasta alcanzar las limitaciones de la CPU.

   ![Configuración del codificador de OBS X264](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Deje el resto de la configuración intacta y haga clic en **OK** (Aceptar).

#### <a name="nvidia-nvenc-encoder-settings"></a>Configuración del codificador de NVIDIA NVENC

1. Si ha seleccionado la opción de codificación de GPU **NVENC**, active la casilla **Rescale Output** (Cambiar escala de salida) y seleccione 1920 x 1080 si usa un evento prémium en directo en Media Services, o 1280x720 si está usando un evento en directo estándar (720p). Si utiliza un evento en directo de tránsito básico o estándar, puede elegir cualquier resolución disponible.

1. Establezca **Rate Control** (Control de velocidad) en CBR para el control de la velocidad de bits constante.

1. Establezca la **velocidad de bits** entre 1500 kbps y 4000 kbps. Se recomiendan 2500 Kbps si usa un evento en directo de codificación estándar en 720P. Si utiliza un evento en directo prémium en 1080P, se recomienda 4000 kbps. Puede elegir ajustar esto en función de las capacidades de CPU disponibles y el ancho de banda de la red para lograr la configuración de calidad deseada.

1. Establezca **Keyframe Interval** (Intervalo de fotogramas clave) en dos segundos, como se indicó anteriormente en las opciones de X264. No supere los cuatro segundos, ya que esto puede afectar significativamente a la latencia de la difusión en directo.

1. Establezca el **valor predeterminado** en Low-Latency (Baja latencia), Low-Latency Performance (Rendimiento de baja latencia) o Low-Latency Quality (Calidad de baja latencia) en función de la velocidad de la CPU en el equipo local. Experimente con estos valores para lograr el mejor equilibrio entre la calidad y el uso de la CPU en su propio hardware.

1. Establezca el **perfil** en "main" (principal) o "high" (alto) si usa una configuración de hardware más eficaz.

1. Deje **Look-ahead** (Lectura previa) desactivada. Si tiene una máquina muy eficaz, puede comprobarlo.

1. Deje desactivada **Psycho Visual Tuning** (Optimización visual Psycho). Si tiene una máquina muy eficaz, puede comprobarlo.

1. Establezca **GPU** en 0 para decidir automáticamente qué GPU desea asignar. También, puede restringir el uso de la GPU.

1. Establezca **Max B-frames** (Número máximo de fotogramas B) en 2.

   ![Configuración del codificador de GPU NVidia NVENC de OBS NVidia.](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Configuración del codificador de Intel QuickSync

1. Si ha seleccionado la opción de codificación de GPU Intel **QuickSync**, active la casilla **Rescale Output** (Cambiar escala de salida) y seleccione 1920x1080 si usa un evento en directo prémium en Media Services, o 1280x720 si usa un evento en directo estándar (720p). Si utiliza un evento en directo de tránsito básico o estándar, puede elegir cualquier resolución disponible.

1. Establezca **Target Usage** (Uso de destino) en "balanced" (equilibrado) o ajústelo según sea necesario en función de la carga combinada de CPU y GPU. Ajústelo según sea necesario y experimente para lograr un 80 % del uso máximo de la CPU en promedio con la calidad que el hardware puede generar. Si está en un hardware más restringido, pruebe con "fast" (rápido) o "very fast" (muy rápido) si tiene problemas de rendimiento.

1. Establezca el **perfil** en "main" (principal) o "high" (alto) si usa una configuración de hardware más eficaz.

1. Establezca **Key Frame Interval** (Intervalo de fotogramas clave) en dos segundos, como se indicó anteriormente en las opciones de X264. No supere los cuatro segundos, ya que esto puede afectar significativamente a la latencia de la difusión en directo.

1. Establezca **Rate Control** (Control de velocidad) en CBR para el control de la velocidad de bits constante.

1. Establezca **Bitrate** (Velocidad de bits) entre 1500 y 4000 kbps.  Se recomiendan 2500 Kbps si usa un evento en directo de codificación estándar en 720P. Si utiliza un evento en directo prémium en 1080P, se recomienda 4000 kbps. Puede elegir ajustar esto en función de las capacidades de CPU disponibles y el ancho de banda de la red para lograr la configuración de calidad deseada.

1. Establezca **Latency** (Latencia) en "Low" (Baja).

1. Establezca **B frames** (Fotogramas B) en 2.

1. Deje desactivada **Subjective Video Enhancements** (Mejoras de vídeo subjetivas).

   ![Configuración del codificador de GPU de OBS Intel QuickSync.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Establecimiento de la configuración de audio

En el procedimiento siguiente, ajustará la configuración de codificación de audio.

1. Seleccione la pestaña Output->Audio (Salida > Audio) en Settings (Configuración).

1. Establezca **Audio Bitrate** (Velocidad de bits de audio) de la pista 1 en 128 kbps.

   ![Configuración de la velocidad de bits de audio OBS.](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Seleccione la pestaña Audio en Settings (Configuración).

1. Establezca **Sample Rate** (Frecuencia de muestreo) en 44,1 kHz.

   ![Configuración de la velocidad de ejemplo de audio en OBS.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Iniciar streaming

1. En el panel **Controls** (Controles), haga clic en **Start Streaming** (Iniciar streaming).

    ![Botón Start streaming (Iniciar streaming) de OBS](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Cambie a la pantalla de eventos en directo de Azure Media Services Live en el explorador y haga clic en el vínculo **Reload Player** (Volver a cargar reproductor). Ahora verá la transmisión en el reproductor de vista previa.

## <a name="set-up-outputs"></a>Configuración de las salidas

En esta parte se configurarán las salidas y se le permitirá guardar una grabación de streaming en vivo.  

> [!NOTE]
> Para transmitir esta salida, el punto de conexión de streaming debe estar en ejecución. Consulte la sección [Ejecución del punto de conexión de streaming predeterminado](#run-the-default-streaming-endpoint).

1. Seleccione el vínculo **Create outputs** (Crear salidas) que aparece debajo del visor de vídeo **Outputs** (Salidas).
1. Si lo desea, modifique el nombre de la salida en el cuadro **Name** (Nombre) y use uno más descriptivo para que sea fácil de encontrar más adelante.

   ![Cuadro de nombre de salida](media/live-events-wirecast-quickstart/output-name.png)
1. Deje todos los demás campos como están por ahora.
1. Seleccione **Next** (Siguiente) para agregar un localizador de streaming.
1. Si lo desea, cambie el nombre del localizador por otro más descriptivo.

   ![Campo de nombre del localizador](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Deje todo lo demás en esta pantalla por ahora.
1. Seleccione **Crear**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Reproducción de la retransmisión de salida con Azure Media Player

1. Copie la dirección URL de streaming que aparece en el reproductor de vídeo de **salida**.
1. En un explorador web, abra la [demostración de Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Pegue la dirección URL de streaming en el cuadro **URL** de Azure Media Player.
1. Seleccione el botón **Update Player** (Actualizar reproductor).
1. Seleccione el icono de **Reproducir** del vídeo para ver el streaming en vivo.

## <a name="stop-the-broadcast"></a>Detención de la retransmisión

Cuando crea que ha transmitido suficiente contenido, detenga la retransmisión.

1. En el portal, seleccione **Stop** (Detener).

1. En OBS, seleccione el botón **Stop Streaming** (Detener streaming) en el panel **Controls** (Controles). Este paso detiene la retransmisión desde OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Reproducción de la salida a petición con Azure Media Player

La salida que creó ahora está disponible para streaming a petición siempre y cuando el punto de conexión de streaming esté en ejecución.

1. Vaya a la lista de Media Services y seleccione **Assets** (Recursos).
1. Busque la salida del evento que creó anteriormente y seleccione el vínculo al recurso. Se abre la página de salida del recurso.
1. Copie la dirección URL de streaming situada bajo el reproductor de vídeo del recurso.
1. Vuelva a Azure Media Player en el explorador y pegue la dirección URL de streaming en el cuadro URL.
1. Seleccione **Update Player** (Actualizar reproductor).
1. Seleccione el icono de **Reproducir** del vídeo para ver el recurso a petición.

## <a name="clean-up-resources"></a>Limpieza de recursos

> [!IMPORTANT]
> Detenga los servicios. Cuando haya completado los pasos de este inicio rápido, asegúrese de detener el evento en directo y el punto de conexión de streaming o se le facturará el tiempo que sigan en ejecución. Para detener el evento en directo, consulte el procedimiento [Detención de la retransmisión](#stop-the-broadcast), pasos 2 y 3.

Para detener el punto de conexión de streaming:

1. En la lista de Media Services, seleccione **Puntos de conexión de streaming**.
2. Seleccione el punto de conexión de streaming que inició anteriormente. Este paso abre la página del punto de conexión.
3. Seleccione **Detener**.

> [!TIP]
> Si quiere conservar los recursos de este evento, asegúrese de eliminarlos para evitar que se le facture el almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Eventos en directo y salidas activas en Media Services](./live-event-outputs-concept.md)
