---
title: Ropa detectada de personas en un vídeo
description: En este tema se proporciona información general sobre la característica de ropa detectada de una persona en un vídeo.
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: juliako
ms.openlocfilehash: 4bc0c0fd21a68d80bf8c8803f1a64e6e0ea097c7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495308"
---
# <a name="peoples-detected-clothing-preview"></a>Ropa detectada de personas (versión preliminar)

Video Analyzer for Media detecta la ropa asociada a la persona que la lleva en el vídeo y proporciona información como, por ejemplo, el tipo de ropa detectada y la marca de tiempo de la aparición (inicio, fin). La API devuelve el nivel de confianza de la detección.
 
Estos son dos ejemplos en los que esta característica podría ser útil:
 
* Mejore la eficacia al crear datos sin procesar para creadores de contenido como publicidad en vídeo, noticias o juegos deportivos (por ejemplo, buscar personas con una camiseta roja en un archivo de vídeo).
* Análisis posterior al evento: detecte y realice un seguimiento del movimiento de una persona para analizar mejor un accidente o un evento delictivo después de producirse (por ejemplo, una explosión, el robo de un banco o un incidente).
 
La característica recién agregada de detección de ropa está disponible al indexar el archivo si elige el valor preestablecido **Opción avanzada** -> **Advanced video** (Vídeo avanzado) o **Advanced video + audio** (Vídeo y audio avanzado) en Indexación de audio y vídeo. La indexación estándar no incluirá este nuevo modelo avanzado.
 
:::image type="content" source="./media/detected-clothing/index-video.png" alt-text="Esta captura de pantalla representa una opción de indexación de vídeo":::  

Si decide ver **Información** del vídeo en el sitio web de [Video Analyzer for Media](https://www.videoindexer.ai/) (anteriormente, Video Indexer), la ropa detectada de las personas se podrá ver en la información de seguimiento de **Personas observadas**. Al elegir una miniatura de una persona, la ropa detectada pasa a estar disponible.

:::image type="content" source="./media/detected-clothing/observed-people.png" alt-text="Captura de pantalla de personas observadas":::  
 
Si está interesado en ver la ropa detectada de las personas en la escala de tiempo del vídeo en el sitio web de Video Analyzer for Media, vaya a **Ver** -> **Mostrar información** y seleccione la opción **Todo** o **Ver** -> **Vista personalizada** y, después, **Personas observadas**. 

:::image type="content" source="./media/detected-clothing/observed-person.png" alt-text="Captura de pantalla de personas observadas":::  
 
La búsqueda de una ropa específica que devuelva todas las personas observadas que la llevan puesta se habilita mediante la barra de búsqueda de **Información** o en la **Escala de tiempo** del vídeo en el sitio web de Video Analyzer for Media.

La siguiente respuesta JSON muestra lo que Video Analyzer for Media devuelve al realizar el seguimiento de las personas observadas para las que ha detectado ropa asociada:

```json
"observedPeople": [
    {
        "id": 1,
        "thumbnailId": "68bab0f2-f084-4c2b-859b-a951ed03c209",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:05.5055",
                "adjustedEnd": "0:00:09.9766333",
                "start": "0:00:05.5055",
                "end": "0:00:09.9766333"
            }
        ]
    },
    {
        "id": 2,
        "thumbnailId": "449bf52d-06bf-43ab-9f6b-e438cde4f217",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:07.2072",
                "adjustedEnd": "0:00:10.5105",
                "start": "0:00:07.2072",
                "end": "0:00:10.5105"
            }
        ]
    },
]
```

## <a name="limitations-and-assumptions"></a>Suposiciones y limitaciones

Es importante tener en cuenta las limitaciones de la ropa detectada para evitar o mitigar los efectos de los falsos negativos (detecciones incorrectas).
 
* Para optimizar los resultados del detector, use secuencias de vídeo de cámaras estáticas (aunque una cámara móvil o escenas mixtas también ofrecerán resultados).
* No se detectan personas si aparecen con un tamaño pequeño (la altura mínima de la persona es de 200 píxeles).
* El tamaño máximo del fotograma es HD.
* No se detectan personas si no están de pie o caminando.
* Un vídeo de baja calidad (por ejemplo, con condiciones de iluminación oscuras) puede afectar a los resultados de la detección.
* Velocidad de fotogramas recomendada: 30 FPS como mínimo.
* La entrada de vídeo recomendada debe contener un máximo de 10 personas en un solo fotograma. La característica podría funcionar con más personas en un solo fotograma, pero el resultado de la detección recupera 10 personas como máximo en un fotograma con el mayor grado de confianza de la detección.
* Las personas con ropa similar (por ejemplo, personas que llevan uniformes o jugadores de equipos deportivos) podrían detectarse como una misma persona con el mismo número de identificación.
* Oclusiones: es posible que haya errores en los que haya oclusiones (escenas propias u oclusiones de otras personas).
* Postura: las pistas se pueden dividir mediante diferentes posturas (trasera o frontal)

## <a name="next-steps"></a>Pasos siguientes 

[Seguimiento de las personas observadas en un vídeo](observed-people-tracing.md)
