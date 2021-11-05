---
title: Servicio de Azure Video Analyzer para solucionar problemas
description: En este artículo se tratan los pasos de solución de problemas del servicio Azure Video Analyzer.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 26d4675274d05500b54bbc43ecb84838b114f0c9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554704"
---
# <a name="troubleshoot-azure-video-analyzer-service"></a>Servicio de Azure Video Analyzer para solucionar problemas

[!INCLUDE [header](includes/cloud-env.md)]

En este artículo se tratan los pasos de solución de problemas en escenarios de error habituales que se pueden producir al usar el servicio.

## <a name="enable-diagnostics"></a>Habilitación de diagnósticos

La [supervisión y el registro](./monitor-log-cloud.md) le ayudarán a entender la taxonomía de los eventos del servicio Video Analyzer y a generar registros que le permitirán depurar problemas.
- Vaya a la sección **Supervisión** de Azure Portal de su cuenta de Video Analyzer y seleccione **Configuración de diagnóstico**. 
- Haga clic en **Agregar configuración de diagnóstico** para habilitar los registros de los tipos de evento deseados: `Diagnostics`, `Audit` y `Operational`. Para más detalles, consulte [aquí](./monitor-log-cloud.md).


## <a name="view-diagnostics"></a>Consulta del diagnóstico

Una vez que haya habilitado el diagnóstico, podrá acceder a los registros de la siguiente manera:

> [!TIP]
> Se recomienda activar al menos una canalización en directo para emitir eventos. 

- En el portal, localice la cuenta de almacenamiento en la que se escribieron los registros.
- Abra la hoja de administración de la cuenta de almacenamiento en cuestión.
- Vaya al Explorador de almacenamiento -> expanda la cuenta de almacenamiento y, después, verá el contenedor de blobs "insights-logs-category". Este blob tendrá los registros en formato de archivo JSON. Puede descargar estos registros para investigar el problema.

## <a name="view-metrics"></a>Visualización de métricas 

El analizador de vídeo también emite métricas para la ingesta y las canalizaciones, lo que puede ayudar a identificar problemas como se muestra a continuación.
- IngressBytes: número total de bytes recibidos por una canalización. Un valor que aumenta constantemente indica que el estado de la canalización es correcto y que recibe datos de vídeo de la cámara RTSP.
- Canalizaciones: permite consultar el estado y los recuentos de las canalizaciones.

## <a name="view-activity-logs"></a>Visualización de registros de actividad

Se genera automáticamente un **registro de actividad** y se puede acceder a él desde la sección "Registro de actividad" Activity log' de la hoja de administración de la cuenta de Video Analyzer en Azure Portal. Puede ver el historial de las llamadas API de ARM realizadas a su cuenta y los detalles pertinentes.

## <a name="common-error-scenarios"></a>Escenarios de error habituales

A continuación se describen algunos de los errores habituales que se producen con el servicio Video Analyzer.

### <a name="unable-to-play-video-after-activating-live-pipeline"></a>No se puede reproducir vídeo después de activar una canalización en directo

- Si usa el widget de Video Analyzer para reproducir el vídeo, pruebe a utilizar Azure Portal si tiene acceso a él. Si el vídeo se reproduce en Azure Portal, pero no en el widget, consulte la sección del widget [más abajo](#playback-error-with-the-widget).

- Si el vídeo no se reproduce en Azure Portal, compruebe si el servicio Video Analyzer recibe datos de vídeo de la cámara RTSP. Seleccione la métrica "Bytes de entrada" yendo a la sección del portal Supervisión -> Métricas. Si la agregación está aumentando, significa que la conexión entre la cámara RTSP y el servicio es correcta; la suma de bytes de entrada debe mostrarse debajo del gráfico. 

- Si el servicio no recibe datos de vídeo de la cámara RTSP, el siguiente paso es [consultar los registros de diagnóstico pertinentes](#view-diagnostics). Es probable que se produzca un error como [ProtocolError](#diagnostic-logs-have-a-protocolerror-with-code-401), y podrá continuar con la solución del problema tal como se indica a continuación.

### <a name="diagnostic-logs-have-a-protocolerror-with-code-401"></a>En los registros de diagnóstico se muestra el error ProtocolError con el código 401

- Si en los registros de diagnóstico ve Microsoft.VideoAnalyzer.Diagnostics.ProtocolError, con el código 401 que se indica a continuación, el primer paso es volver a comprobar las credenciales de RTSP. El evento de ejemplo que verá es el siguiente:

   ```
   {
       "time": "2021-10-15T02:56:18.7890000Z",
       "resourceId": "/SUBSCRIPTIONS/{GUID}/RESOURCEGROUPS/8AVA/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/AVASAMPLEZ2OHI3VBIRQPC",
       "region": "westcentralus",
       "category": "Diagnostics",
       "operationName": "Microsoft.VideoAnalyzer.Diagnostics.ProtocolError",
       "operationVersion": "1.0",
       "level": "Error",
       "traceContext": "{\n  \"traceId\": \"f728d155-b4fd-4aec-8307-bbe2a324f4c3\"\n}",
       "properties": {
           "subject": "/livePipelines/your-pipeline/sources/rtspSource",
           "body": {
               "code": "401",
               "target": "rtsp://127.0.0.1:33643/some-path",
               "protocol": "rtsp"
           }
       }
   }

   ```

- Si usa una canalización en directo para conectarse a una cámara a la que se puede acceder a través de Internet, compruebe la URL, el nombre de usuario y la contraseña de RTSP que se usaron al crear la canalización en directo. Puede llamar a GET en la canalización en directo para ver la URL y el nombre de usuario, pero la contraseña no se devolverá. Debe comprobar el código que se usó para crear la canalización en directo.

- Si está empleando un [adaptador de dispositivo remoto](./use-remote-device-adapter.md), pruebe los pasos que se indican a continuación.

   - Compruebe que el [centro de IoT esté vinculado a su cuenta de Video Analyzer](../create-video-analyzer-account.md#post-deployment-steps). Es necesario para usar un adaptador de dispositivo remoto.
   - Ejecute el método directo `remoteDeviceAdapterList` en el módulo Edge y compruebe la dirección IP. [Aquí](../edge/direct-methods.md) se muestran una solicitud y una respuesta de ejemplo.
   - Examine la respuesta del adaptador de dispositivo remoto que está usando en la canalización en directo en la que hay el problema y, después, compárela con el ejemplo que hay en [este artículo](use-remote-device-adapter.md). Compruebe que la dirección IP de la cámara sea correcta.
   - Vaya a Azure Portal -> Cuenta de Video Analyzer -> Activos -> Canalizaciones -> Editar canalización en directo -> vuelva a escribir el nombre de usuario y la contraseña de RTSP. Compruebe que la dirección URL de RTSP que proporciona comience por `rtsp://localhost:554/…`. En este caso, es necesario usar `localhost`.

- Si los pasos anteriores no le permiten resolver el problema y la reproducción de vídeo sigue sin funcionar, inicie sesión en Azure Portal y abra una incidencia de soporte técnico. Es posible que deba adjuntar los registros del módulo perimetral de Video Analyzer; consulte la sección "Uso del comando support-bundle" del [documento de solución de problemas de Edge](../edge/troubleshoot.md#common-error-resolutions).

### <a name="unable-to-record-to-a-video-resource"></a>No se puede grabar en un recurso de vídeo

Con Video Analyzer, debe usar un recurso de vídeo diferente cuando efectúe una grabación de una cámara RTSP distinta. También tendría que cambiar a un nuevo recurso de vídeo si cambiase la configuración de la cámara (por ejemplo, la resolución). Algunas de las topologías de canalización de ejemplo tienen nombres cifrados de forma rígida para el recurso de vídeo en las propiedades del nodo del receptor de vídeo. Si usa estas topologías directamente con diferentes cámaras, se encontrará con este problema. Modifique la propiedad `videoName` en el nodo del receptor de vídeo para asegurarse de su unicidad.

### <a name="interrupted-recording-or-playback"></a>Grabación o reproducción interrumpidas

Al crear una canalización en directo, es necesario especificar la velocidad de bits máxima (`bitrateKbps`) a la que la cámara RTSP debe enviar vídeo al servicio.
Si la cámara supera ese límite, el servicio Video Analyzer se desconectará de la cámara brevemente. Puede volver a intentar conectarse a la cámara en caso de que haya habido un pico temporal en la velocidad de bits. Para identificar esta situación, busque el evento Microsoft.VideoAnalyzer.Diagnostics.RtspIngestionSessionEnded en los registros de diagnóstico, con el código de error `SourceBitrateExceeded`.
Para solucionar este error, reduzca la velocidad de bits en la cámara o aumente el valor `bitrateKbps` de la canalización en directo para que coincida con el de la cámara.

### <a name="playback-error-with-the-widget"></a>Error de reproducción con el widget

Si se le muestra el error "Acceso prohibido" en el widget de Video Analyzer, se mostrará un evento de advertencia en el registro de auditoría.

- Compruebe que haya generado el token JWT de la API de cliente y la directiva de acceso correspondiente; consulte la documentación para [crear un token](../access-policies.md) y [crear una directiva de acceso](../access-policies.md#creating-an-access-policy). El reproductor no funcionará si la directiva de acceso no se ha configurado correctamente y el token JWT no coincide con la directiva. 
- Si el problema no se resuelve, recopile los registros del widget y abra una incidencia de soporte técnico mediante Azure Portal.
- Recopilación de registros del widget:
    - Presione F12 para habilitar las herramientas de desarrollador del explorador, vaya a la pestaña Consola y, después, habilite el registro "Todos los niveles".   
    - En el icono Configuración, seleccione Preferencias --> Consola --> Mostrar marcas de tiempo. Guarde los registros.

## <a name="collect-logs-for-submitting-a-support-ticket"></a>Recopilación de registros para enviar una incidencia de soporte técnico
   
Cuando los pasos autoguiados de solución de problemas no sirvan para solucionar el problema y no necesite ayuda con nada más, abra una incidencia de soporte técnico usando Azure Portal con los detalles y registros pertinentes. También puede ponerse en contacto con nosotros enviándonos un correo electrónico a videoanalyzerhelp@microsoft.com.
   
> [!WARNING]
> Los registros pueden contener información de identificación personal (PII) como su dirección IP. Se eliminarán todas las copias locales de los registros en cuanto terminemos de examinarlos y cerremos la incidencia de soporte técnico.
   
## <a name="next-steps"></a>Pasos siguientes

[P+F](./faq.yml)
