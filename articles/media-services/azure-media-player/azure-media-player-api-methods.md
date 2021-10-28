---
title: Métodos de API de Azure Media Player
description: La API de Azure Media Player permite interactuar con el vídeo a través de JavaScript, independientemente de si el explorador está reproduciendo el vídeo a través de vídeo HTML5, Flash, Silverlight o cualquier otra tecnología de reproducción compatible.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 443c2abbd005c3e2517caa98c707132ad0849d3a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234329"
---
# <a name="api"></a>API #

La API de Azure Media Player permite interactuar con el vídeo a través de JavaScript, independientemente de si el explorador está reproduciendo el vídeo a través de vídeo HTML5, Flash, Silverlight o cualquier otra tecnología de reproducción compatible.

## <a name="referencing-the-player"></a>Referencia del reproductor ##

Para usar las funciones de API, debe tener acceso al objeto de reproductor. Afortunadamente, es fácil. Solo tiene que asegurarse de que la etiqueta de vídeo tenga un id. El código para insertar de ejemplo tiene el id. `vid1`. Si tiene varios vídeos en una página, asegúrese de que cada etiqueta de vídeo tenga un id. único.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Si el reproductor todavía no se ha inicializado a través del atributo de configuración de datos u otro método, también se inicializará el reproductor.

## <a name="wait-until-the-player-is-ready"></a>Espera hasta que el reproductor esté preparado ##

El tiempo que Azure Media Player tarda en configurar el vídeo y la API variará en función de la tecnología de reproducción que se use. HTML5 suele ser mucho más rápido de cargar que Flash o Silverlight. Por ese motivo, se debe usar la función "ready" del reproductor para desencadenar cualquier código que requiera la API del reproductor.

```javascript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

O BIEN

```javascript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Métodos de API ##

Ahora que tiene acceso a un reproductor preparado, puede controlar el vídeo, obtener valores o responder a eventos del vídeo. Los nombres de las funciones de la API de Azure Media Player intentan seguir a los de la [API multimedia HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). La principal diferencia es que se usan funciones de captador y establecedor para las propiedades de vídeo.

```javascript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registro de eventos ##
Los eventos se deben registrar directamente después de inicializar el reproductor por primera vez para asegurarse de que todos los eventos se notifican correctamente a la aplicación y se debe realizar fuera del evento preparado.

```javascript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Pasos siguientes ##

<!---Some context for the following links goes here--->
- [Inicio rápido de Azure Media Player](azure-media-player-quickstart.md)