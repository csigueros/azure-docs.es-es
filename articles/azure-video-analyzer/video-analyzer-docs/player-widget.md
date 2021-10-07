---
title: Uso del widget de reproductor de Azure Video Analyzer
description: En este artículo de referencia se explica cómo agregar un widget del reproductor de Video Analyzer a la aplicación.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: ffc17e756a303723fe1d21c6ba221fed31147eaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620580"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Uso del widget de reproductor de Azure Video Analyzer

En este tutorial aprenderá a usar un widget de reproductor dentro de su aplicación. Este código es un widget fácil de insertar con el que sus usuarios podrán reproducir vídeo y desplazarse por las partes de un archivo de vídeo segmentado. Para ello, generará una página HTML estática con el widget insertado y todos los componentes necesarios para que funcione.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Creación de un token
> * Enumeración de los vídeos
> * Obtención de la dirección URL base para reproducir un [recurso de aplicación de vídeo](./terminology.md#video)
> * Creación de una página con el reproductor
> * Pasar un punto de conexión de streaming y un token al reproductor.

## <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial:

* Una cuenta de Azure que tenga una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.
* [Visual Studio Code](https://code.visualstudio.com/) u otro editor para el archivo HTML.
* [Grabación y reproducción continua de vídeo](./use-continuous-video-recording.md) o [Detección de movimiento y grabación de vídeo en dispositivos perimetrales](./detect-motion-record-video-clips-cloud.md)
* Creación de un [token](./access-policies.md#creating-a-token)
* Creación de una [directiva de acceso](./access-policies.md#creating-an-access-policy)

## <a name="list-video-resources"></a>Enumeración de los recursos de vídeo

A continuación, genere una lista de recursos de vídeo. Debe realizar una llamada REST al punto de conexión de la cuenta que ha usado anteriormente y autenticarse con el token que ha generado.

Aunque hay muchas maneras de enviar una solicitud GET a una API de REST, en este caso va a usar una función de JavaScript. En el siguiente código se usa un elemento [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) junto con los valores que va a almacenar en los campos `clientApiEndpointUrl` y `token` de la página para enviar una solicitud `GET` sincrónica. A continuación, el código toma la lista de vídeos resultante y los almacena en el área de texto `videoList` que ha configurado en la página.

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
   >El elemento `clientApiEndPoint` y el token se recopilan de la opción [Creación de un token](./access-policies.md#creating-a-token).

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
   const avaPlayer = document.getElementById("avaPlayer");
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
   
## <a name="add-the-zone-drawer-component"></a>Incorporación del componente de cajón de zonas

1. Agregue un elemento AVA-Zone-Drawer al documento:
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer"></ava-zone-drawer>
   ```
1. Obtenga un vínculo al cajón de zonas de Video Analyzer que se encuentra en la página:
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. Cargue el cajón de zonas en el reproductor:
   ```javascript
   zoneDrawer.load();
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
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();

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
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="avaPlayer"></ava-player>
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

## <a name="play-a-video"></a>Reproducción de un vídeo

Ahora que tiene la página hospedada, acceda a ella y siga los pasos para reproducir un vídeo.

1. Indique los valores **Dirección URL del punto de conexión de la API de cliente** y **Token**.
1. Seleccione **Obtener vídeos**.
1. En la lista de vídeos, seleccione un nombre de vídeo e indíquelo en el campo **Nombre del vídeo**.
1. Seleccione **Reproducir vídeo**.

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

| Nombre   | Tipo             | Descripción                         |
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

* Más información sobre la [API de widget](https://github.com/Azure/video-analyzer/tree/main/widgets)
