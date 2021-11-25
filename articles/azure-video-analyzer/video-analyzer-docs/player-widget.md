---
title: Uso del widget de reproductor de Video Analyzer
description: En este artículo se explica cómo agregar un widget del reproductor de Video Analyzer a la aplicación.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/12/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8dbf85609196a930d7f51a713753f725b3016396
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487477"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Uso del widget de reproductor de Azure Video Analyzer

En este tutorial aprenderá a usar un widget de reproductor dentro de su aplicación. Este código es un widget fácil de insertar con el que sus usuarios podrán reproducir vídeo y desplazarse por las partes de un archivo de vídeo segmentado. Para ello, generará una página HTML estática con el widget insertado y todos los componentes necesarios para que funcione.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Creación de una página con el reproductor
> * Enumeración de los vídeos
> * Pasar un punto de conexión de streaming y un token al reproductor.
> * Agregar un reproductor de Cajón de zonas
> * Ver vídeos recortados a las horas de inicio y finalización especificadas

## <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial:

* Una cuenta de Azure que tenga una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.
* [Visual Studio Code](https://code.visualstudio.com/) u otro editor para el archivo HTML.
* Ejecutar topologías de [Grabación y reproducción continua de vídeo](edge/use-continuous-video-recording.md) o [detección de movimiento y grabación de vídeo en dispositivos perimetrales](./detect-motion-record-video-clips-cloud.md)
* Creación de un [token](./access-policies.md#creating-a-token)
* Creación de una [directiva de acceso](./access-policies.md#creating-an-access-policy)


## <a name="create-a-web-page-with-a-video-player"></a>Creación de una página web con un reproductor de vídeo

Use el código de ejemplo siguiente para crear una página web.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
JWT Auth Token for Client API: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
</body>
</body>
</html>
```
## <a name="list-video-resources"></a>Enumeración de los recursos de vídeo

A continuación, genere una lista de recursos de vídeo. Debe realizar una llamada REST al punto de conexión de la cuenta que ha usado anteriormente y autenticarse con el token que ha generado.

Aunque hay muchas maneras de enviar una solicitud GET a una API de REST, en este caso va a usar una función de JavaScript. En el siguiente código se usa un elemento [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) junto con los valores que va a almacenar en los campos `clientApiEndpointUrl` y `token` de la página para enviar una solicitud `GET` sincrónica. A continuación, el código toma la lista de vídeos resultante y los almacena en el área de texto `videoList` que ha configurado en la página.

El siguiente fragmento de código le ayudará a solicitar la lista de vídeos.

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```
   > [!NOTE]
   >El elemento `clientApiEndPoint` y `token` se recopilan de la opción [Creación de un token](./access-policies.md#creating-a-token)

## <a name="add-the-video-analyzer-player-component"></a>Incorporación del componente de reproductor de Video Analyzer

Ahora que tiene una dirección URL del punto de conexión de la API de cliente, un token y un nombre de vídeo, puede agregar el reproductor a la página.

1. Incluya el módulo del propio reproductor agregando el paquete directamente a la página. Puede incluir la dirección del paquete NPM en su aplicación o hacer que se inserte dinámicamente en el tiempo de ejecución del siguiente modo:
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. Agregue un elemento `AVA-Player` al documento:
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. Obtenga un vínculo al widget del reproductor de Video Analyzer que se encuentra en la página:
   ```javascript
   const avaPlayer = document.getElementById("videoPlayer");
   ```
1. Para configurar el reproductor con los valores que tiene, deberá configurarlos como un objeto, tal y como se muestra a continuación:
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Cargue el vídeo en el reproductor para empezar:
   ```javascript
   avaPlayer.load();
   ```
   
## <a name="add-the-zone-drawer-component"></a>Incorporación del componente de Cajón de zonas

El componente de Cajón de zonas permite dibujar líneas y polígonos sobre el reproductor de Video Analyzer. 

1. Agregue un elemento AVA-Zone-Drawer al documento:
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer">
        <ava-player id="videoPlayer2"></ava-player>
   </ava-zone-drawer>
   ```
1. Obtenga un vínculo al widget del reproductor de Video Analyzer que se reproducirá dentro del cajón de zonas:
   ```javascript
   const avaPlayer2 = document.getElementById("videoPlayer2");
   ```
1. Configure el reproductor que se reproducirá dentro del cajón de zonas:
   ```javascript
   avaPlayer2.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Cargue el vídeo en el reproductor dentro del cajón de zonas:
   ```javascript
   avaPlayer2.load();
   ```
1. Obtenga un vínculo al cajón de zonas que se encuentra en la página:
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. Cargue el cajón de zonas en el reproductor:
   ```javascript
   zoneDrawer.load();
   ```
1. Configure el cajón de zonas:
   ```javascript
   zoneDrawer.configure();
   ```
1. Para crear y guardar zonas, debe agregar agentes de escucha de eventos aquí:
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
   ```

## <a name="put-it-all-together"></a>Colocación de todo junto

Al combinar los elementos web anteriores, se obtiene la siguiente página HTML estática. Esta página le permite usar un punto de conexión de cuenta y un token para ver un vídeo.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("videoPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
        
        const avaPlayer2 = document.getElementById("videoPlayer2");
        avaPlayer2.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer2.load();
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();
        zoneDrawer.configure();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
JWT Auth Token for Client API: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<div id="container" style="width:720px" class="widget-container">
    <ava-player width="720px" id="videoPlayer"></ava-player>
</div>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="videoPlayer2"></ava-player>
</ava-zone-drawer>
</body>
</html>
```

## <a name="host-the-page"></a>Hospedaje de la página

Aunque puede probar esta página de forma local, puede que le interese hacerlo en una versión hospedada. En caso de que no tenga una forma rápida de hospedar una página, a continuación encontrará instrucciones sobre cómo hacerlo usando [sitios web estáticos](../../storage/blobs/storage-blob-static-website.md) con Azure Storage. Los siguientes pasos son una versión condensada de [estas instrucciones más completas](../../storage/blobs/storage-blob-static-website-how-to.md). Los pasos se han actualizado con relación a los archivos que se usan en este tutorial.

1. Cree una cuenta de almacenamiento.
1. En **Administración de datos**, seleccione **Sitio web estático**.
1. Habilite el sitio web estático en la cuenta de almacenamiento.
1. En **Nombre del documento de índice**, escriba **index.html**.
1. En **Ruta de acceso del documento de error**, escriba **404.html**.
1. Seleccione **Guardar**.
1. Anote el **punto de conexión principal** que aparece. Este será su sitio web.
1. Encima de **Punto de conexión principal**, seleccione **$web**.
1. Usando el botón **Cargar** de la parte superior, cargue la página HTML estática como **index.html**.

### <a name="play-a-video"></a>Reproducción de un vídeo

Ahora que tiene la página hospedada, acceda a ella y siga los pasos para reproducir un vídeo.

1. Indique los valores **Dirección URL del punto de conexión de la API de cliente** y **Token**.
1. Seleccione **Obtener vídeos**.
1. En la lista de vídeos, seleccione un nombre de vídeo e indíquelo en el campo **Nombre del vídeo**.
1. Seleccione **Reproducir vídeo**.

### <a name="live-video-playback"></a>Reproducción de vídeo en directo

Si el objeto livePipeline está en un estado `activated` y el vídeo se está grabando, el reproductor carga automáticamente la vista **EN DIRECTO**. Esta reproducción de vídeo es casi en tiempo real y tendrá una latencia corta de aproximadamente 2 segundos.

En la vista **EN DIRECTO**, hará lo siguiente:
1. Verá la reproducción de vídeo casi en tiempo real.
1. No verá la escala de tiempo.
1. Al hacer clic en el icono de **Cuadro**, se mostrarán los cuadros de límite, si existen.

> [!Tip]
> Para cambiar a la vista en la que puede ver todos los clips grabados previamente, haga clic en el botón **EN DIRECTO**.
 
### <a name="capture-lines-and-zones"></a>Capturar líneas y zonas

1. Vaya al reproductor de **Cajón de zonas**
1. Haga clic en el primer icono de la esquina superior izquierda para dibujar zonas.
1. Para dibujar zonas y líneas, solo tiene que hacer clic en los puntos en los que desea tener los puntos finales. No hay ninguna funcionalidad de arrastre para dibujar las zonas y las líneas.
1. Verá las zonas y las líneas creadas en la sección derecha del reproductor.
1. Para obtener las coordenadas de las líneas y las zonas, haga clic en el botón de **Guardar**.
1. Si lo hace, mostrará la respuesta JSON con las coordenadas de punto, que puede usar las topologías adecuadas.

### <a name="video-clips"></a>Clips de vídeo
Permite crear clips de vídeo seleccionando una hora de inicio y finalización.

El widget del reproductor de vídeo de Video Analyzer admite la reproducción de clips de vídeo mediante la especificación de una fecha de inicio y finalización, como se muestra a continuación:

> [!Note] 
> El widget del reproductor de vídeo de Video Analyzer usa el estándar de hora UTC, por lo que la hora de inicio y finalización seleccionada debe convertirse a este formato.

Use el código siguiente en el archivo HTML para abrir un reproductor de vídeo que cargará un vídeo a partir de la startTime y la endTime que especifique.

```javascript
    const avaPlayer = document.getElementById("videoPlayer");
    const startUTCDate = new Date(Date.UTC(selectedClip.start.getFullYear(), selectedClip.start.getMonth(), selectedClip.start.getDate(), selectedClip.start.getHours(), selectedClip.start.getMinutes(), selectedClip.start.getSeconds()));
    const endUTCDate = new Date(Date.UTC(selectedClip.end.getFullYear(), selectedClip.end.getMonth(), selectedClip.end.getDate(), selectedClip.end.getHours(), selectedClip.end.getMinutes(), selectedClip.end.getSeconds()));
    avaPlayer.load({ startTime: startUTCDate, endTime: endUTCDate });
``` 

## <a name="additional-details"></a>Detalles adicionales

Las siguientes secciones contienen detalles adicionales importantes que se deben tener en cuenta.
### <a name="refresh-the-access-token"></a>Actualización del token de acceso

El reproductor usa el token de acceso que ha generado anteriormente para obtener un token de autorización de reproducción. Los tokens expiran periódicamente, por lo que se deben actualizar. Hay dos maneras de actualizar el token de acceso para el reproductor una vez que se ha generado uno nuevo:

* Llamando activamente al método `setAccessToken` del widget.
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* Escuchar el evento `TOKEN_EXPIRED` para actuar en consecuencia.
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

El evento `TOKEN_EXPIRED` se producirá 5 segundos antes de que expire el token. Si va a configurar un cliente de escucha de eventos, hágalo antes de llamar a la función `load` del widget del reproductor.

### <a name="configuration-details"></a>Detalles de configuración

Aunque el reproductor anterior tiene una configuración sencilla, puede usar una gama más amplia de opciones para los valores de configuración. Estos son los campos admitidos:

| Nombre   | Tipo             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | string | Token JWT del widget |
| `videoName` | string | Nombre del recurso de vídeo  |
| `clientApiEndpointUrl` | string | Dirección URL del punto de conexión de la API de cliente |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>Formas alternativas de cargar el código en la aplicación

El paquete que se usa para obtener el código en su aplicación es un [paquete NPM](https://www.npmjs.com/package/@azure/video-analyzer-widgets). En el ejemplo anterior, se ha cargado la versión más reciente en el tiempo de ejecución directamente desde el repositorio. No obstante, también puede descargar e instalar el paquete localmente mediante lo siguiente:

```bash
npm install @azure/video-analyzer/widgets
```

También puede importarlo dentro del código de la aplicación usando lo siguiente para TypeScript:

```typescript
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

Si quiere crear un widget de reproductor de forma dinámica, puede hacerlo del siguiente modo para JavaScript:
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


Si usa este método para la importación, deberá crear el objeto del cajón de zonas y del reproductor mediante programación una vez completada la importación.  En el ejemplo anterior, agregó el módulo a la página mediante la etiqueta HTML `ava-player`. Para crear un objeto de cajón de zonas y de reproductor mediante código, puede hacer lo siguiente en JavaScript:


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

También puede hacer lo siguiente en TypeScript:

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

A continuación, debe agregarlo al código HTML:

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>Pasos siguientes

* Pruebe nuestra [reproducción de ejemplo mediante widgets](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/video-player).
* Obtenga información sobre cómo se pueden implementar las distintas características del widget visitando nuestro [repositorio de widgets](https://github.com/Azure/video-analyzer-widgets).
