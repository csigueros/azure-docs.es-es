---
title: Uso del widget de reproductor de Azure Video Analyzer
description: En este artículo de referencia se explica cómo agregar un widget del reproductor de Video Analyzer a la aplicación.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: b70bfc9a10e357c6f1e64c1737fdb4c049b505f5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037449"
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
* [Visual Studio Code](https://code.visualstudio.com/) u otro editor para el archivo HTML
* [Grabación y reproducción continua de vídeo](./use-continuous-video-recording.md) o [detección de movimiento y grabación de vídeo en dispositivos perimetrales](./detect-motion-record-video-clips-cloud.md)

Además, le resultará útil familiarizarse con los siguientes recursos:

- [Componentes web](https://developer.mozilla.org/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="create-a-token"></a>Creación de un token

En esta sección creará un JSON Web Token (JWT) para usarlo más adelante en el artículo. Usará una aplicación de ejemplo que generará el token JWT y le proporcionará todos los campos necesarios para crear la directiva de acceso.

> [!NOTE] 
> Si sabe cómo generar un token JWT basado en un certificado RSA o ECC, puede omitir esta sección.

1. Clone el [repositorio de ejemplos de C# de AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp). A continuación, vaya a la carpeta *src/jwt-token-issuer* y localice la aplicación *JWTTokenIssuer*.

    > [!NOTE] 
    > Para obtener más información sobre cómo configurar los valores de audiencia, consulte [Directivas de acceso](./access-policies.md).

2. Abra Visual Studio Code y vaya a la carpeta donde haya descargado la aplicación *JWTTokenIssuer*. Esta carpeta debe contener el archivo *\*.csproj*.
3. En el panel del explorador, vaya al archivo *program.cs*.
4. En la línea 77, cambie el público por el punto de conexión de Azure Video Analyzer, seguido de /videos/\*. Debería tener este aspecto:

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Puede encontrar el punto de conexión de Video Analyzer en la sección de información general del recurso en Azure Portal. Más adelante, en la sección [Enumeración de los recursos de vídeo](#list-video-resources), se hace referencia a este valor como `clientApiEndpointUrl`.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="Captura de pantalla en la que se muestra el punto de conexión del widget de reproductor":::
    
5. En la línea 78, cambie el emisor por el valor de emisor de su certificado (por ejemplo, `https://contoso.com`).
6. Guarde el archivo. Es posible que aparezca el mensaje **Faltan recursos necesarios para compilar y depurar del emisor de token jwt. ¿Agregarlos?** . Seleccione **Sí**.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Captura de pantalla en la que se muestra la solicitud de los activos necesarios en Visual Studio Code":::
   
7. Abra la ventana del símbolo del sistema y vaya a la carpeta con los archivos *JWTTokenIssuer*. Ejecute los dos comandos siguientes en orden: `dotnet build` y `dotnet run`. Si tiene la extensión de C# en Visual Studio Code, también puede seleccionar F5 para ejecutar la aplicación *JWTTokenIssuer*.

La aplicación se compila y se ejecuta. Una vez compilada, crea un certificado autofirmado y genera a partir de este la información del token JWT. También puede ejecutar el archivo *JWTTokenIssuer.exe*, ubicado en la carpeta de depuración del directorio desde el que ha compilado *JWTTokenIssuer*. La ventaja de ejecutar la aplicación es que puede especificar las opciones de entrada de la siguiente manera:

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

*JWTTokenIssuer* crea el JWT y los siguientes componentes necesarios:

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

**Asegúrese de copiar y guardar estos valores para usarlos posteriormente.**

## <a name="create-an-access-policy"></a>Creación de una directiva de acceso

Las directivas de acceso definen los permisos y la duración del acceso a una secuencia de vídeo determinada. En este tutorial va a configurar una directiva de acceso para Video Analyzer en Azure Portal.  

1. Inicie sesión en Azure Portal y vaya al grupo de recursos en el que se encuentra su cuenta de Video Analyzer.
1. Seleccione el recurso de Video Analyzer.
1. En **Video Analyzer**, seleccione **Directivas de acceso**.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Captura de pantalla en la que se muestra la opción &quot;Directivas de acceso&quot;":::
   
1. Seleccione **Nueva** e indique la siguiente información:

   - **Nombre de directiva de acceso:** puede elegir cualquier nombre.

   - **Emisor:** este valor debe coincidir con el emisor del JWT. 

   - **Público:** público del JWT. `${System.Runtime.BaseResourceUrlPattern}` es el valor predeterminado. Para obtener más información sobre el público y `${System.Runtime.BaseResourceUrlPattern}`, consulte [Directivas de acceso](./access-policies.md).

   - **Tipo de clave**: RSA 

   - **Algoritmo:** los valores admitidos son RS256, RS384 y RS512.

   - **Id. de clave:** este Id. se genera a partir de su certificado. Para obtener más información, consulte [Creación de un token](#create-a-token).

   - **Módulo de clave RSA:** este valor se genera a partir de su certificado. Para obtener más información, consulte [Creación de un token](#create-a-token).

   - **Exponente de clave RSA:** este valor se genera a partir de su certificado. Para obtener más información, consulte [Creación de un token](#create-a-token).

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Captura de pantalla en la que se muestra el portal de directivas de acceso"::: 
   
   > [!NOTE] 
   > Estos valores proceden de la aplicación *JWTTokenIssuer* creada en el paso anterior.

1. Seleccione **Guardar**.

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
   >El elemento `clientApiEndPoint` y el token se recopilan de [Creación de un token](#create-a-token).

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
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<ava-player width="720px" id="avaPlayer"></ava-player>
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
import { Player } from '@video-analyzer/widgets';
```

Si quiere crear un widget de reproductor de forma dinámica, puede hacerlo del siguiente modo para JavaScript:
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

Si usa este método para la importación, deberá crear el objeto del reproductor mediante programación una vez completada la importación. En el ejemplo anterior, ha agregado el módulo a la página usando la etiqueta HTML `ava-player`. Para crear un objeto de reproductor mediante código, puede hacer lo siguiente en JavaScript:

```javascript
const avaPlayer = new ava.widgets.player();
```

También puede hacer lo siguiente en TypeScript:

```typescript
const avaPlayer = new Player();
```

A continuación, debe agregarlo al código HTML:

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre la [API de widget](https://github.com/Azure/video-analyzer/tree/main/widgets)
