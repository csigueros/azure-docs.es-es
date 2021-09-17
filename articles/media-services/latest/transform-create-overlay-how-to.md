---
title: Creación de una superposición de imagen
description: Obtenga información sobre cómo crear una superposición de imagen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 532e26e486e758b3fd8079f6f61f3e00e82dbdca
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662312"
---
# <a name="how-to-create-an-image-overlay"></a>Creación de una superposición de imagen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services permite superponer sobre un vídeo una imagen, un archivo de audio u otro vídeo. La entrada debe especificar exactamente un solo archivo de imagen. Puede especificar un archivo de imagen en formato JPG, PNG, GIF o BMP, un archivo de audio (por ejemplo, un archivo WAV, MP3, WMA o M4A) o un archivo de vídeo en un formato admitido.


## <a name="prerequisites"></a>Prerrequisitos

* Recopile la información de la cuenta que necesita para configurar el archivo *appsettings.json* en el ejemplo. Si no está seguro de cómo hacerlo, consulte [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](../../active-directory/develop/quickstart-register-app.md). Se esperan los valores siguientes en el archivo *appsettings.json*.

```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
```

Si todavía no sabe cómo crear transformaciones, se recomienda que complete las siguientes actividades:

* Lea [Codificación de vídeo y audio con Media Services](encode-concept.md).
* Lea [Procedimiento de codificación con una transformación personalizada - .NET](transform-custom-presets-how-to.md). Siga los pasos que aparecen en ese artículo para configurar la instancia de .NET necesaria para trabajar con las transformaciones y, a continuación, vuelva aquí para probar un ejemplo de valor preestablecido de superposiciones.
* Consulte el [documento de referencia de Transformaciones](/rest/api/media/transforms).

Una vez que esté familiarizado con las Transformaciones, descargue el ejemplo de superposiciones.

## <a name="overlays-preset-sample"></a>Ejemplo de valor preestablecido de superposiciones

Clone el repositorio de .NET de Media Services de ejemplo.

```bash
    git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

Vaya a la carpeta de la solución e inicie Visual Studio Code o Visual Studio 2019.

Hay varios ejemplos de codificación disponibles en la carpeta "VideoEncoding". Abra el proyecto ubicado en la carpeta [VideoEncoding/Encoding_OverlayImage](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_OverlayImage) de la solución para empezar a aprender a usar superposiciones.

El proyecto de ejemplo contiene dos archivos multimedia, a saber, un archivo de vídeo y una imagen de logotipo para superponerla encima del vídeo:
* ignite.mp4
* cloud.png

En este ejemplo primero creamos una transformación personalizada que puede superponer la imagen sobre el vídeo en el método CreateCustomTransform.  Mediante la propiedad *[Filters](/rest/api/media/transforms/create-or-update#filters)* de *[StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)* , asignamos una nueva colección Filters que contiene la configuración de superposición de vídeo.

Un objeto [VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay) contiene una propiedad denominada *InputLabel*, que es necesaria para asignar desde la lista de archivos de entrada de trabajo enviados al trabajo y localizar el archivo de origen de entrada adecuado que se debe usar como imagen superpuesta o vídeo.  Al enviar el trabajo, se usa este mismo nombre de etiqueta para que coincida con el valor de la transformación. En el ejemplo se usa el nombre de etiqueta "logo", como se aprecia en la constante de cadena *OverlayLabel*.

En el siguiente fragmento de código se muestra cómo se formatea la transformación para usar una superposición.

```csharp
new TransformOutput
                {
                    Preset = new StandardEncoderPreset
                    {
                        Filters = new Filters
                        {
                            Overlays = new List<Overlay>
                            {
                                new VideoOverlay
                                {
                                    InputLabel = OverlayLabel,   // same as the one used in the JobInput to identify which asset is the overlay image
                                    Position = new Rectangle( "1200","670") // left, top position of the overlay in absolute pixel position relative to the source videos resolution. 
    
                                }
                            }
                        },
                        Codecs = new List<Codec>
                        {
                            new AacAudio
                            {
                            },
                            new H264Video
                            {
                                KeyFrameInterval = TimeSpan.FromSeconds(2),
                                Layers = new List<H264Layer>
                                {
                                    new H264Layer
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 1000000, // 1Mbps
                                        Width = "1280",
                                        Height = "720"
                                    },
                                    new H264Layer   // Adding a second layer to see that the image also is scaled and positioned the same way on this layer. 
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 600000, // 600 kbps
                                        Width = "480",
                                        Height = "270"
                                    }
                                }
                            }
                        },
                        Formats = new List<Format>
                        {
                            new Mp4Format
                            {
                                FilenamePattern = "{Basename}_{Bitrate}{Extension}",
                            }
                        }
                    }
                }
```

Al enviar el trabajo a la transformación, primero debe crear los dos activos de entrada.

* Activo 1: en este ejemplo, el primer recurso creado es el archivo de vídeo local "ignite.mp4". Este es el vídeo que se usará como fondo de la composición y sobre el que superpondrá una imagen de logotipo. 
* Activo 2: en este ejemplo, el segundo recurso (almacenado en la variable "overlayImageAsset") contiene el archivo .png que se usará para el logotipo. Esta imagen se colocará sobre el vídeo durante la codificación.

Cuando se crea el trabajo en el método *SubmitJobAsync*, primero se construye una matriz JobInput mediante un objeto List<>.  Este objeto contendrá las referencias a los dos activos de origen.

Con el fin de identificar y emparejar el activo de entrada que se va a usar como superposición en el filtro definido en la transformación anterior, se usa de nuevo el nombre de etiqueta "logotipo" para controlar la coincidencia. El nombre de etiqueta se agrega a JobInputAsset para la imagen .png. De este modo, se indica a la transformación qué activo debe usar al realizar la operación de superposición. Puede volver a usar esta misma transformación con distintos activos almacenados en Media Services que contengan varios logotipos o gráficos que quiera superponer y simplemente cambiar el nombre del activo que se pasa al trabajo. Podrá usar el mismo nombre de etiqueta "logo" para que la transformación coincida con él.

``` csharp
    // Add both the Video and the Overlay image assets here as inputs to the job.
    List<JobInput> jobInputs = new List<JobInput>() {
        new JobInputAsset(assetName: inputAssetName),
        new JobInputAsset(assetName: overlayAssetName, label: OverlayLabel)
    };
```

Ejecute el ejemplo seleccionando el proyecto en la ventana "Ejecutar y depurar" de Visual Studio Code. El ejemplo mostrará el progreso de la operación de codificación y, por último, descargará el contenido en la carpeta /Output de la raíz del proyecto o, en el caso de la versión completa de Visual Studio, puede que en la carpeta /bin/Output. 

El ejemplo también publica el contenido para streaming y genera las direcciones URL de archivo de manifiesto HLS, DASH y Smooth Streaming, que se pueden usar en cualquier reproductor compatible.  También puede copiar fácilmente la dirección URL del manifiesto en la [demostración de Azure Media Player](http://ampdemo.azureedge.net/), pegar la dirección URL que termina por "/manifest" en el cuadro URL y, a continuación, hacer clic en *Actualizar reproductor*.

## <a name="api-references"></a>Referencias de API

* [Objeto VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay)
* [Filtros](/rest/api/media/transforms/create-or-update#filters)
* [StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)


[!INCLUDE [reference dotnet sdk references](./includes/reference-dotnet-sdk-references.md)]

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
