---
title: 'Compatibilidad con idiomas: servicio de voz'
titleSuffix: Azure Cognitive Services
description: El servicio de voz admite un gran número de idiomas para la conversión de texto a voz y voz a texto, junto con la traducción de voz. En este artículo se proporciona una lista completa de idiomas compatibles por servicio.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: pafarley
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 84841dff43fa827a942eee28cb9cbcc1499e1fcc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011814"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Compatibilidad con idiomas y voces en el servicio de voz

La compatibilidad con los idiomas varía según la funcionalidad del servicio de voz. En las tablas siguientes se resume la compatibilidad con idiomas para las ofertas de servicio [conversión de voz en texto](#speech-to-text), [texto a voz](#text-to-speech), [traducción de voz](#speech-translation) y [reconocimiento del hablante (Speaker Recognition)](#speaker-recognition).

## <a name="speech-to-text"></a>Voz a texto

El SDK de Voz de Microsoft y la API REST admiten los siguientes idiomas (configuraciones regionales). 

Para mejorar la precisión, se ofrece la posibilidad de personalizar un subconjunto de idiomas mediante la carga de **audio y transcripciones etiquetadas por humanos** o **texto relacionado: oraciones**. La compatibilidad con la personalización del modelo acústico con **audio + transcripciones etiquetadas por usuarios** se limita a los modelos base específicos que se enumeran a continuación. Otros modelos e idiomas base solo usarán el texto de las transcripciones para entrenar modelos personalizados como con **Texto relacionado: oraciones**. Para más información sobre la personalización, consulte [Introducción a Custom Speech](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Idioma                 | Configuración regional (BCP-47) | Personalizaciones  | [Identificación del idioma](how-to-automatic-language-detection.md) | [Evaluación de la pronunciación](how-to-pronunciation-assessment.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|--------------------------|
| Árabe (Argelia)                   | `ar-DZ` | Texto                                   |                           |                          |
| Árabe (Bahréin), estándar moderno  | `ar-BH` | Texto                                   |                           |                          |
| Árabe (Egipto)                     | `ar-EG` | Texto                                   | Sí                          |                          |
| Árabe (Iraq)                      | `ar-IQ` | Texto                                   |                           |                          |
| Árabe (Israel)                    | `ar-IL` | Texto                                   |                           |                          |
| Árabe (Jordania)                    | `ar-JO` | Texto                                   |                           |                          |
| Árabe (Kuwait)                    | `ar-KW` | Texto                                   |                           |                          |
| Árabe (Líbano)                   | `ar-LB` | Texto                                   |                           |                          |
| Árabe (Libia)                     | `ar-LY` | Texto                                   |                           |                          |
| Árabe (Marruecos)                   | `ar-MA` | Texto                                   |                           |                          |
| Árabe (Omán)                      | `ar-OM` | Texto                                   |                           |                          |
| Árabe (Qatar)                     | `ar-QA` | Texto                                   |                           |                          |
| Árabe (Arabia Saudí)              | `ar-SA` | Texto                                   |                           |                          |
| Árabe (Autoridad Palestina)     | `ar-PS` | Texto                                   |                           |                          |
| Árabe (Siria)                     | `ar-SY` | Texto                                   |                           |                          |
| Árabe (Túnez)                   | `ar-TN` | Texto                                   |                           |                          |
| Árabe (Emiratos Árabes Unidos)      | `ar-AE` | Texto                                   |                           |                          |
| Árabe (Yemen)                     | `ar-YE` | Texto                                   |                           |                          |
| Búlgaro (Bulgaria)               | `bg-BG` | Texto                                   |                           |                          |
| Catalán (España)                    | `ca-ES` | Texto<br>Pronunciación                  | Sí                          |                          |
| Chino (cantonés, tradicional)   | `zh-HK` | Audio (20201015)<br>Texto                 |        Sí                   |                          |
| Chino (mandarín, simplificado)     | `zh-CN` | Audio (20200910)<br>Texto                 |     Sí                      | Sí                         |
| Chino (mandarín, Taiwán)       | `zh-TW` | Audio (20190701, 20201015)<br>Texto                 |           Sí                |                          |
| Croata (Croacia)                 | `hr-HR` | Texto<br>Pronunciación                  |                           |                          |
| Checo (República Checa)             | `cs-CZ` | Texto<br>Pronunciación                  |                           |                          |
| Danés (Dinamarca)                   | `da-DK` | Texto<br>Pronunciación                  | Sí                          |                          |
| Neerlandés (Países Bajos)                | `nl-NL` | Audio (20201015)<br>Texto<br>Pronunciación|    Sí                       |                          |
| Inglés (Australia)                | `en-AU` | Audio (20201019)<br>Texto<br>Pronunciación| Sí                          |                          |
| Inglés (Canadá)                   | `en-CA` | Audio (20201019)<br>Texto<br>Pronunciación| Sí                          |                          |
| Inglés (Ghana)                    | `en-GH` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Hong Kong)                | `en-HK` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (India)                    | `en-IN` | Audio (20200923)<br>Texto<br>Pronunciación |                          |                          |
| Inglés (Irlanda)                  | `en-IE` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Kenia)                    | `en-KE` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Nueva Zelanda)              | `en-NZ` | Audio (20201019)<br>Texto<br>Pronunciación |                          |                          |
| Inglés (Nigeria)                  | `en-NG` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Filipinas)              | `en-PH` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Singapur)                | `en-SG` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Sudáfrica)             | `en-ZA` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Tanzania)                 | `en-TZ` | Texto<br>Pronunciación                  |                           |                          |
| Inglés (Reino Unido)           | `en-GB` | Audio (20201019)<br>Texto<br>Pronunciación| Sí                          | Sí                         |
| Spanish (Traditional Sort) - Spain            | `en-US` | Audio (20201019, 20210223)<br>Texto<br>Pronunciación| Sí                          | Sí                         |
| Estonio (Estonia)                  | `et-EE` | Texto<br>Pronunciación                  |                           |                          |
| Filipino (Filipinas)             | `fil-PH`| Texto<br>Pronunciación                  |                           |                          |
| Finés (Finlandia)                  | `fi-FI` | Texto<br>Pronunciación                  |     Sí                      |                          |
| Francés (Canadá)                    | `fr-CA` | Audio (20201015)<br>Texto<br>Pronunciación|     Sí                      |                          |
| Francés (Francia)                    | `fr-FR` | Audio (20201015)<br>Texto<br>Pronunciación|      Sí                     |                          |
| Francés (Suiza)               | `fr-CH` | Texto<br>Pronunciación                  |                           |                          |
| Alemán (Austria)                   | `de-AT` | Texto<br>Pronunciación                  |                           |                          |
| Alemán (Suiza)               | `de-CH` | Texto<br>Pronunciación                  |                           |                          |
| Alemán (Alemania)                   | `de-DE` | Audio (20190701, 20200619, 20201127)<br>Texto<br>Pronunciación|  Sí                         |                          |
| Griego (Grecia)                     | `el-GR` | Texto                                   |  Sí                         |                          |
| Gujarati (India)                  | `gu-IN` | Texto                                   |                           |                          |
| Hebreo (Israel)                    | `he-IL` | Texto                                   |                           |                          |
| Hindi (India)                      | `hi-IN` | Audio (20200701)<br>Texto                 |     Sí                      |                          |
| Húngaro (Hungría)                | `hu-HU` | Texto<br>Pronunciación                  |                           |                          |
| Indonesio (Indonesia)             | `id-ID` | Texto<br>Pronunciación                  |                           |                          |
| Irlandés (Irlanda)                    | `ga-IE` | Texto<br>Pronunciación                  |                           |                          |
| Italiano (Italia)                    | `it-IT` | Audio (20201016)<br>Texto<br>Pronunciación|      Sí                     |                          |
| Japonés (Japón)                   | `ja-JP` | Texto                                   |      Sí                     |                          |
| Canarés (India)                   | `kn-IN` | Texto                                   |                           |                          |
| Coreano (Corea)                     | `ko-KR` | Audio (20201015)<br>Texto                 |      Sí                     |                          |
| Letón (Letonia)                   | `lv-LV` | Texto<br>Pronunciación                  |                           |                          |
| Lituano (Lituania)             | `lt-LT` | Texto<br>Pronunciación                  |                           |                          |
| Malayo (Malasia)                   | `ms-MY` | Texto                                   |                           |                          |
| Maltés (Malta)                    | `mt-MT` | Texto                                   |                           |                          |
| Maratí (India)                    | `mr-IN` | Texto                                   |                           |                          |
| Noruego (Bokmål, Noruega)         | `nb-NO` | Texto                                   |     Sí                      |                          |
| Persa (Irán)                     | `fa-IR` | Texto                                   |                           |                          |
| Polaco (Polonia)                    | `pl-PL` | Texto<br>Pronunciación                  |       Sí                    |                          |
| Portugués (Brasil)                | `pt-BR` | Audio (20190620, 20201015)<br>Texto<br>Pronunciación|          Sí                 |                          |
| Portugués (Portugal)              | `pt-PT` | Texto<br>Pronunciación                  |             Sí              |                          |
| Rumano (Rumanía)                 | `ro-RO` | Texto<br>Pronunciación                  |  Sí                         |                          |
| Ruso (Rusia)                   | `ru-RU` | Audio (20200907)<br>Texto                 |                Sí           |                          |
| Eslovaco (Eslovaquia)                  | `sk-SK` | Texto<br>Pronunciación                  |                           |                          |
| Esloveno (Eslovenia)               | `sl-SI` | Texto<br>Pronunciación                  |                           |                          |
| Español (Argentina)                | `es-AR` | Texto<br>Pronunciación                  |                           |                          |
| Español (Bolivia)                  | `es-BO` | Texto<br>Pronunciación                  |                           |                          |
| Español (Chile)                    | `es-CL` | Texto<br>Pronunciación                  |                           |                          |
| Español (Colombia)                 | `es-CO` | Texto<br>Pronunciación                  |                           |                          |
| Español (Costa Rica)               | `es-CR` | Texto<br>Pronunciación                  |                           |                          |
| Español (Cuba)                     | `es-CU` | Texto<br>Pronunciación                  |                           |                          |
| Español (República Dominicana)       | `es-DO` | Texto<br>Pronunciación                  |                           |                          |
| Español (Ecuador)                  | `es-EC` | Texto<br>Pronunciación                  |                           |                          |
| Español (El Salvador)              | `es-SV` | Texto<br>Pronunciación                  |                           |                          |
| Español (Guinea Ecuatorial)        | `es-GQ` | Texto                                   |                           |                          |
| Español (Guatemala)                | `es-GT` | Texto<br>Pronunciación                  |                           |                          |
| Español (Honduras)                 | `es-HN` | Texto<br>Pronunciación                  |                           |                          |
| Español (México)                   | `es-MX` | Audio (20200907)<br>Texto<br>Pronunciación|    Sí                       |                          |
| Español (Nicaragua)                | `es-NI` | Texto<br>Pronunciación                  |                           |                          |
| Español (Panamá)                   | `es-PA` | Texto<br>Pronunciación                  |                           |                          |
| Español (Paraguay)                 | `es-PY` | Texto<br>Pronunciación                  |                           |                          |
| Español (Perú)                     | `es-PE` | Texto<br>Pronunciación                  |                           |                          |
| Español (Puerto Rico)              | `es-PR` | Texto<br>Pronunciación                  |                           |                          |
| Español (España)                    | `es-ES` | Audio (20201015)<br>Texto<br>Pronunciación|  Sí                         |                          |
| Español (Uruguay)                  | `es-UY` | Texto<br>Pronunciación                  |                           |                          |
| Español (EE. UU.)                      | `es-US` | Texto<br>Pronunciación                  |                           |                          |
| Español (Venezuela)                | `es-VE` | Texto<br>Pronunciación                  |                           |                          |
| Swahili (Kenia)                    | `sw-KE` | Texto                                   |                           |                          |
| Sueco (Suecia)                   | `sv-SE` | Texto<br>Pronunciación                  |   Sí                        |                          |
| Tamil (India)                      | `ta-IN` | Texto                                   |                           |                          |
| Telugu (India)                     | `te-IN` | Texto                                   |                           |                          |
| Tailandés (Tailandia)                    | `th-TH` | Texto                                   |      Sí                     |                          |
| Turco (Turquía)                   | `tr-TR` | Texto                                   |                           |                          |
| Vietnamita (Vietnam)               | `vi-VN` | Texto                                   |                           |                          |

## <a name="text-to-speech"></a>Texto a voz

Tanto el SDK de Voz de Microsoft como las API REST admiten estas voces, y cada una de ellas admite un idioma y un dialecto específicos, que se identifican mediante la configuración regional. También puede obtener una lista completa de los idiomas y las voces que se admiten para cada región o punto de conexión específico a través de la [API de la lista de voces](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Los precios son distintos para voces estándar, personalizadas y neuronales. Consulte la página de [precios](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para más información.

### <a name="neural-voices"></a>Voces neuronales

Texto a voz neuronal es un nuevo tipo de síntesis de voz con tecnología de redes neuronales profundas. Cuando se usa una voz neuronal, es prácticamente imposible distinguir la voz sintetizada de las grabaciones humanas.

Las voces neuronales se pueden usar para que las interacciones con los bots de chat y los asistentes de voz sean más naturales y atractivas, para convertir textos digitales (por ejemplo, los libros electrónicos) en audiolibros y para mejorar los sistemas de navegación de los automóviles. Con su prosodia natural similar a la humana y la clara articulación de las palabras, las voces neuronales reducen considerablemente la fatiga de la escucha que aparece cuando los usuarios interactúan con sistemas de inteligencia artificial.

> [!NOTE]
> Las voces neuronales se crean a partir de ejemplos que usan una frecuencia de muestreo de 24 kHz.
> Todas las voces pueden aumentar o reducir las frecuencias de muestreo al sintetizar.

| Idioma | Configuración regional | Sexo | Nombre de voz | Compatibilidad de estilo |
|---|---|---|---|---|
| Afrikáans (Sudáfrica) | af-ZA | Female | `af-ZA-AdriNeural` <sup>Nuevo</sup>  | General |
| Afrikáans (Sudáfrica) | af-ZA | Male | `af-ZA-WillemNeural` <sup>Nuevo</sup>  | General |
| Amárico (Etiopía) | am-ET | Female | `am-ET-MekdesNeural` <sup>Nuevo</sup>  | General |
| Amárico (Etiopía) | am-ET | Male | `am-ET-AmehaNeural` <sup>Nuevo</sup>  | General |
| Árabe (Argelia) | ar-DZ | Female | `ar-DZ-AminaNeural` <sup>Nuevo</sup>  | General |
| Árabe (Argelia) | ar-DZ | Male | `ar-DZ-IsmaelNeural` <sup>Nuevo</sup>  | General |
| Árabe (Bahréin) | ar-BH | Female | `ar-BH-LailaNeural` <sup>Nuevo</sup>  | General |
| Árabe (Bahréin) | ar-BH | Male | `ar-BH-AliNeural` <sup>Nuevo</sup>  | General |
| Árabe (Egipto) | `ar-EG` | Female | `ar-EG-SalmaNeural` | General |
| Árabe (Egipto) | `ar-EG` | Male | `ar-EG-ShakirNeural` | General |
| Árabe (Iraq) | ar-IQ | Female | `ar-IQ-RanaNeural` <sup>Nuevo</sup>  | General |
| Árabe (Iraq) | ar-IQ | Male | `ar-IQ-BasselNeural` <sup>Nuevo</sup>  | General |
| Árabe (Jordania) | ar-JO | Female | `ar-JO-Sana Neural` <sup>Nuevo</sup>  | General |
| Árabe (Jordania) | ar-JO | Male | `ar-JO-Taim Neural` <sup>Nuevo</sup>  | General |
| Árabe (Kuwait) | ar-KW | Female | `ar-KW-NouraNeural` <sup>Nuevo</sup>  | General |
| Árabe (Kuwait) | ar-KW | Male | `ar-KW-FahedNeural` <sup>Nuevo</sup>  | General |
| Árabe (Libia) | ar-LY | Female | `ar-LY-ImanNeural` <sup>Nuevo</sup>  | General |
| Árabe (Libia) | ar-LY | Male | `ar-LY-OmarNeural` <sup>Nuevo</sup>  | General |
| Árabe (Marruecos) | ar-MA | Female | `ar-MA-MounaNeural` <sup>Nuevo</sup>  | General |
| Árabe (Marruecos) | ar-MA | Male | `ar-MA-JamalNeural` <sup>Nuevo</sup>  | General |
| Árabe (Qatar) | ar-QA | Female | `ar-QA-AmalNeural` <sup>Nuevo</sup>  | General |
| Árabe (Qatar) | ar-QA | Male | `ar-QA-MoazNeural` <sup>Nuevo</sup>  | General |
| Árabe (Arabia Saudí) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | General |
| Árabe (Arabia Saudí) | `ar-SA` | Male | `ar-SA-HamedNeural` | General |
| Árabe (Siria) | ar-SY | Female | `ar-SY-AmanyNeural` <sup>Nuevo</sup>  | General |
| Árabe (Siria) | ar-SY | Male | `ar-SY-LaithNeural` <sup>Nuevo</sup>  | General |
| Árabe (Túnez) | ar-TN | Female | `ar-TN-ReemNeural` <sup>Nuevo</sup>  | General |
| Árabe (Túnez) | ar-TN | Male | `ar-TN-HediNeural` <sup>Nuevo</sup>  | General |
| Árabe (Emiratos Árabes Unidos) | ar-AE | Female | `ar-AE-FatimaNeural` <sup>Nuevo</sup>  | General |
| Árabe (Emiratos Árabes Unidos) | ar-AE | Male | `ar-AE-HamdanNeural` <sup>Nuevo</sup>  | General |
| Árabe (Yemen) | ar-YE | Female | `ar-YE-MaryamNeural` <sup>Nuevo</sup>  | General |
| Árabe (Yemen) | ar-YE | Male | `ar-YE-SalehNeural` <sup>Nuevo</sup>  | General |
| Bengalí (Bangladesh) | bn-BD | Female | `bn-BD-NabanitaNeural` <sup>Nuevo</sup>  | General |
| Bengalí (Bangladesh) | bn-BD | Male | `bn-BD-PradeepNeural` <sup>Nuevo</sup>  | General |
| Búlgaro (Bulgaria) | `bg-BG` | Female | `bg-BG-KalinaNeural` | General |
| Búlgaro (Bulgaria) | `bg-BG` | Male | `bg-BG-BorislavNeural` | General |
| Birmano (Myanmar) | my-MM | Female | `my-MM-NilarNeural` <sup>Nuevo</sup>  | General |
| Birmano (Myanmar) | my-MM | Male | `my-MM-ThihaNeural` <sup>Nuevo</sup>  | General |
| Catalán (España) | `ca-ES` | Female | `ca-ES-AlbaNeural` | General |
| Catalán (España) | `ca-ES` | Female | `ca-ES-JoanaNeural` | General |
| Catalán (España) | `ca-ES` | Male | `ca-ES-EnricNeural` | General |
| Chino (cantonés, tradicional) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | General |
| Chino (cantonés, tradicional) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | General |
| Chino (cantonés, tradicional) | `zh-HK` | Male | `zh-HK-WanLungNeural` | General |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | General, varios estilos disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | General, varios estilos y representaciones disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | Voz de adulto de edad avanzada, varios estilos disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | General, varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | General, varios estilos y representaciones disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Voz de niño, optimizado para la narración de historias |
| Chino (mandarín, simplificado) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | General, varios estilos disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Optimizado para lectura de noticias,<br /> varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chino (mandarín, simplificado) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Optimizado para la narración de historias |
| Chino (mandarín, Taiwán) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | General |
| Chino (mandarín, Taiwán) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | General |
| Chino (mandarín, Taiwán) | `zh-TW` | Male | `zh-TW-YunJheNeural` | General |
| Croata (Croacia) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | General |
| Croata (Croacia) | `hr-HR` | Male | `hr-HR-SreckoNeural` | General |
| Checo (República Checa) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | General |
| Checo (República Checa) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | General |
| Danés (Dinamarca) | `da-DK` | Female | `da-DK-ChristelNeural` | General |
| Danés (Dinamarca) | `da-DK` | Male | `da-DK-JeppeNeural` | General |
| Neerlandés (Bélgica) | `nl-BE` | Female | `nl-BE-DenaNeural` | General | 
| Neerlandés (Bélgica) | `nl-BE` | Male | `nl-BE-ArnaudNeural` | General | 
| Neerlandés (Países Bajos) | `nl-NL` | Female | `nl-NL-ColetteNeural` | General |
| Neerlandés (Países Bajos) | `nl-NL` | Female | `nl-NL-FennaNeural` | General |
| Neerlandés (Países Bajos) | `nl-NL` | Male | `nl-NL-MaartenNeural` | General |
| Inglés (Australia) | `en-AU` | Female | `en-AU-NatashaNeural` | General |
| Inglés (Australia) | `en-AU` | Male | `en-AU-WilliamNeural` | General |
| Inglés (Canadá) | `en-CA` | Female | `en-CA-ClaraNeural` | General |
| Inglés (Canadá) | `en-CA` | Male | `en-CA-LiamNeural` | General |
| Inglés (Hong Kong) | `en-HK` | Female | `en-HK-YanNeural` | General |
| Inglés (Hong Kong) | `en-HK` | Male | `en-HK-SamNeural` | General |
| Inglés (India) | `en-IN` | Female | `en-IN-NeerjaNeural` | General |
| Inglés (India) | `en-IN` | Male | `en-IN-PrabhatNeural` | General |
| Inglés (Irlanda) | `en-IE` | Female | `en-IE-EmilyNeural` | General |
| Inglés (Irlanda) | `en-IE` | Male | `en-IE-ConnorNeural` | General |
| Inglés (Kenia) | en-KE | Female | `en-KE-AsiliaNeural` <sup>Nuevo</sup>  | General |
| Inglés (Kenia) | en-KE | Male | `en-KE-ChilembaNeural` <sup>Nuevo</sup>  | General |
| Inglés (Nueva Zelanda) | `en-NZ` | Female | `en-NZ-MollyNeural` | General |
| Inglés (Nueva Zelanda) | `en-NZ` | Male | `en-NZ-MitchellNeural` | General |
| Inglés (Nigeria) | en-NG | Female | `en-NG-EzinneNeural` <sup>Nuevo</sup>  | General |
| Inglés (Nigeria) | en-NG | Male | `en-NG-AbeoNeural` <sup>Nuevo</sup>  | General |
| Inglés (Filipinas) | `en-PH` | Female | `en-PH-RosaNeural` | General | 
| Inglés (Filipinas) | `en-PH` | Male | `en-PH-JamesNeural` | General | 
| Inglés (Singapur) | `en-SG` | Female | `en-SG-LunaNeural` | General |
| Inglés (Singapur) | `en-SG` | Male | `en-SG-WayneNeural` | General |
| Inglés (Sudáfrica) | `en-ZA` | Female | `en-ZA-LeahNeural` | General |
| Inglés (Sudáfrica) | `en-ZA` | Male | `en-ZA-LukeNeural` | General |
| Inglés (Tanzania) | en-TZ | Female | `en-TZ-ImaniNeural` <sup>Nuevo</sup>  | General |
| Inglés (Tanzania) | en-TZ | Male | `en-TZ-ElimuNeural` <sup>Nuevo</sup>  | General |
| Inglés (Reino Unido) | `en-GB` | Female | `en-GB-LibbyNeural` | General |
| Inglés (Reino Unido) | `en-GB` | Female | `en-GB-MiaNeural`<sup>Se retirará el 30 de octubre; consulte la información que tiene a continuación.</sup> | General |
| Inglés (Reino Unido) | `en-GB` | Female | `en-GB-SoniaNeural` | General |
| Inglés (Reino Unido) | `en-GB` | Male | `en-GB-RyanNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-AmberNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-AriaNeural` | General, varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-AshleyNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-CoraNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-ElizabethNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-JennyNeural` | General, varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-MichelleNeural`| General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-MonicaNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-SaraNeural` | General, varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Spanish (Traditional Sort) - Spain | `en-US` | Niño | `en-US-AnaNeural`| General |
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-BrandonNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-ChristopherNeural`  | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-EricNeural` | General |
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-GuyNeural` | General, varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-JacobNeural` | General |
| Estonio (Estonia) | `et-EE` | Female | `et-EE-AnuNeural` | General |
| Estonio (Estonia) | `et-EE` | Male | `et-EE-KertNeural` | General |
| Filipino (Filipinas) | fil-PH | Female | `fil-PH-BlessicaNeural` <sup>Nuevo</sup>  | General |
| Filipino (Filipinas) | fil-PH | Male | `fil-PH-AngeloNeural` <sup>Nuevo</sup>  | General |
| Finés (Finlandia) | `fi-FI` | Female | `fi-FI-NooraNeural` | General |
| Finés (Finlandia) | `fi-FI` | Female | `fi-FI-SelmaNeural` | General |
| Finés (Finlandia) | `fi-FI` | Male | `fi-FI-HarriNeural` | General |
| Francés (Bélgica) | `fr-BE` | Female | `fr-BE-CharlineNeural` | General | 
| Francés (Bélgica) | `fr-BE` | Male | `fr-BE-GerardNeural` | General | 
| Francés (Canadá) | `fr-CA` | Female | `fr-CA-SylvieNeural` | General |
| Francés (Canadá) | `fr-CA` | Male | `fr-CA-AntoineNeural` | General |
| Francés (Canadá) | `fr-CA` | Male | `fr-CA-JeanNeural` | General |
| Francés (Francia) | `fr-FR` | Female | `fr-FR-DeniseNeural` | General |
| Francés (Francia) | `fr-FR` | Male | `fr-FR-HenriNeural` | General |
| Francés (Suiza) | `fr-CH` | Female | `fr-CH-ArianeNeural` | General |
| Francés (Suiza) | `fr-CH` | Male | `fr-CH-FabriceNeural` | General |
| Gallego (España) | gl-ES | Female | `gl-ES-SabelaNeural` <sup>Nuevo</sup>  | General |
| Gallego (España) | gl-ES | Male | `gl-ES-RoiNeural` <sup>Nuevo</sup>  | General |
| Alemán (Austria) | `de-AT` | Female | `de-AT-IngridNeural` | General |
| Alemán (Austria) | `de-AT` | Male | `de-AT-JonasNeural` | General |
| Alemán (Alemania) | `de-DE` | Female | `de-DE-KatjaNeural` | General |
| Alemán (Alemania) | `de-DE` | Male | `de-DE-ConradNeural` | General |
| Alemán (Suiza) | `de-CH` | Female | `de-CH-LeniNeural` | General |
| Alemán (Suiza) | `de-CH` | Male | `de-CH-JanNeural` | General |
| Griego (Grecia) | `el-GR` | Female | `el-GR-AthinaNeural` | General |
| Griego (Grecia) | `el-GR` | Male | `el-GR-NestorasNeural` | General |
| Gujarati (India) | `gu-IN` | Female | `gu-IN-DhwaniNeural` | General |
| Gujarati (India) | `gu-IN` | Male | `gu-IN-NiranjanNeural` | General |
| Hebreo (Israel) | `he-IL` | Female | `he-IL-HilaNeural` | General |
| Hebreo (Israel) | `he-IL` | Male | `he-IL-AvriNeural` | General |
| Hindi (India) | `hi-IN` | Female | `hi-IN-SwaraNeural` | General |
| Hindi (India) | `hi-IN` | Male | `hi-IN-MadhurNeural` | General |
| Húngaro (Hungría) | `hu-HU` | Female | `hu-HU-NoemiNeural` | General |
| Húngaro (Hungría) | `hu-HU` | Male | `hu-HU-TamasNeural` | General |
| Indonesio (Indonesia) | `id-ID` | Female | `id-ID-GadisNeural` | General |
| Indonesio (Indonesia) | `id-ID` | Male | `id-ID-ArdiNeural` | General |
| Irlandés (Irlanda) | `ga-IE` | Female | `ga-IE-OrlaNeural` | General |
| Irlandés (Irlanda) | `ga-IE` | Male | `ga-IE-ColmNeural` | General |
| Italiano (Italia) | `it-IT` | Female | `it-IT-ElsaNeural` | General |
| Italiano (Italia) | `it-IT` | Female | `it-IT-IsabellaNeural` | General |
| Italiano (Italia) | `it-IT` | Male | `it-IT-DiegoNeural` | General |
| Japonés (Japón) | `ja-JP` | Female | `ja-JP-NanamiNeural` | General |
| Japonés (Japón) | `ja-JP` | Male | `ja-JP-KeitaNeural` | General |
| Javanés (Indonesia) | jv-ID | Female | `jv-ID-SitiNeural` <sup>Nuevo</sup>  | General |
| Javanés (Indonesia) | jv-ID | Male | `jv-ID-DimasNeural` <sup>Nuevo</sup>  | General |
| Jemer (Camboya) | km-KH | Female | `km-KH-SreymomNeural` <sup>Nuevo</sup>  | General |
| Jemer (Camboya) | km-KH | Male | `km-KH-PisethNeural` <sup>Nuevo</sup>  | General |
| Coreano (Corea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | General |
| Coreano (Corea) | `ko-KR` | Male | `ko-KR-InJoonNeural` | General |
| Letón (Letonia) | `lv-LV` | Female | `lv-LV-EveritaNeural` | General |
| Letón (Letonia) | `lv-LV` | Male | `lv-LV-NilsNeural` | General |
| Lituano (Lituania) | `lt-LT` | Female | `lt-LT-OnaNeural` | General |
| Lituano (Lituania) | `lt-LT` | Male | `lt-LT-LeonasNeural` | General |
| Malayo (Malasia) | `ms-MY` | Female | `ms-MY-YasminNeural` | General |
| Malayo (Malasia) | `ms-MY` | Male | `ms-MY-OsmanNeural` | General |
| Maltés (Malta) | `mt-MT` | Female | `mt-MT-GraceNeural` | General |
| Maltés (Malta) | `mt-MT` | Male | `mt-MT-JosephNeural` | General |
| Maratí (India) | `mr-IN` | Female | `mr-IN-AarohiNeural` | General |
| Maratí (India) | `mr-IN` | Male | `mr-IN-ManoharNeural` | General |
| Noruego (Bokmål, Noruega) | `nb-NO` | Female | `nb-NO-IselinNeural` | General |
| Noruego (Bokmål, Noruega) | `nb-NO` | Female | `nb-NO-PernilleNeural` | General |
| Noruego (Bokmål, Noruega) | `nb-NO` | Male | `nb-NO-FinnNeural` | General |
| Persa (Irán) | fa-IR | Female | `fa-IR-DilaraNeural` <sup>Nuevo</sup>  | General |
| Persa (Irán) | fa-IR | Male | `fa-IR-FaridNeural` <sup>Nuevo</sup>  | General |
| Polaco (Polonia) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | General |
| Polaco (Polonia) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | General |
| Polaco (Polonia) | `pl-PL` | Male | `pl-PL-MarekNeural` | General |
| Portugués (Brasil) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | General, varios estilos de voz disponibles [mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portugués (Brasil) | `pt-BR` | Male | `pt-BR-AntonioNeural` | General |
| Portugués (Portugal) | `pt-PT` | Female | `pt-PT-FernandaNeural` | General |
| Portugués (Portugal) | `pt-PT` | Female | `pt-PT-RaquelNeural` | General |
| Portugués (Portugal) | `pt-PT` | Male | `pt-PT-DuarteNeural` | General |
| Rumano (Rumanía) | `ro-RO` | Female | `ro-RO-AlinaNeural` | General |
| Rumano (Rumanía) | `ro-RO` | Male | `ro-RO-EmilNeural` | General |
| Ruso (Rusia) | `ru-RU` | Female | `ru-RU-DariyaNeural` | General |
| Ruso (Rusia) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | General |
| Ruso (Rusia) | `ru-RU` | Male | `ru-RU-DmitryNeural` | General |
| Eslovaco (Eslovaquia) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | General |
| Eslovaco (Eslovaquia) | `sk-SK` | Male | `sk-SK-LukasNeural` | General |
| Esloveno (Eslovenia) | `sl-SI` | Female | `sl-SI-PetraNeural` | General |
| Esloveno (Eslovenia) | `sl-SI` | Male | `sl-SI-RokNeural` | General |
| Somalí (Somalia) | so-SO | Female | `so-SO-UbaxNeural` <sup>Nuevo</sup>  | General |
| Somalí (Somalia) | so-SO | Male | `so-SO-MuuseNeural` <sup>Nuevo</sup>  | General |
| Español (Argentina) | `es-AR` | Female | `es-AR-ElenaNeural` | General |
| Español (Argentina) | `es-AR` | Male | `es-AR-TomasNeural` | General |
| Español (Bolivia) | es-BO | Female | `es-BO-SofiaNeural` <sup>Nuevo</sup>  | General |
| Español (Bolivia) | es-BO | Male | `es-BO-MarceloNeural` <sup>Nuevo</sup>  | General |
| Español (Chile) | es-CL | Female | `es-CL-CatalinaNeural` <sup>Nuevo</sup>  | General |
| Español (Chile) | es-CL | Male | `es-CL-LorenzoNeural` <sup>Nuevo</sup>  | General |
| Español (Colombia) | `es-CO` | Female | `es-CO-SalomeNeural` | General |
| Español (Colombia) | `es-CO` | Male | `es-CO-GonzaloNeural` | General |
| Español (Costa Rica) | es-CR | Female | `es-CR-MariaNeural` <sup>Nuevo</sup>  | General |
| Español (Costa Rica) | es-CR | Male | `es-CR-JuanNeural` <sup>Nuevo</sup>  | General |
| Español (Cuba) | es-CU | Female | `es-CU-BelkysNeural` <sup>Nuevo</sup>  | General |
| Español (Cuba) | es-CU | Male | `es-CU-ManuelNeural` <sup>Nuevo</sup>  | General |
| Español (República Dominicana) | es-DO | Female | `es-DO-RamonaNeural` <sup>Nuevo</sup>  | General |
| Español (República Dominicana) | es-DO | Male | `es-DO-EmilioNeural` <sup>Nuevo</sup>  | General |
| Español (Ecuador) | es-EC | Female | `es-EC-AndreaNeural` <sup>Nuevo</sup>  | General |
| Español (Ecuador) | es-EC | Male | `es-EC-LuisNeural` <sup>Nuevo</sup>  | General |
| Español (El Salvador) | es-SV | Female | `es-SV-LorenaNeural` <sup>Nuevo</sup>  | General |
| Español (El Salvador) | es-SV | Male | `es-SV-RodrigoNeural` <sup>Nuevo</sup>  | General |
| Español (Guinea Ecuatorial) | es-GQ | Female | `es-GQ-TeresaNeural` <sup>Nuevo</sup>  | General |
| Español (Guinea Ecuatorial) | es-GQ | Male | `es-GQ-JavierNeural` <sup>Nuevo</sup>  | General |
| Español (Guatemala) | es-GT | Female | `es-GT-MartaNeural` <sup>Nuevo</sup>  | General |
| Español (Guatemala) | es-GT | Male | `es-GT-AndresNeural` <sup>Nuevo</sup>  | General |
| Español (Honduras) | es-HN | Female | `es-HN-KarlaNeural` <sup>Nuevo</sup>  | General |
| Español (Honduras) | es-HN | Male | `es-HN-CarlosNeural` <sup>Nuevo</sup>  | General |
| Español (México) | `es-MX` | Female | `es-MX-DaliaNeural` | General |
| Español (México) | `es-MX` | Male | `es-MX-JorgeNeural` | General |
| Español (Nicaragua) | es-NI | Female | `es-NI-YolandaNeural` <sup>Nuevo</sup>  | General |
| Español (Nicaragua) | es-NI | Male | `es-NI-FedericoNeural` <sup>Nuevo</sup>  | General |
| Español (Panamá) | es-PA | Female | `es-PA-MargaritaNeural` <sup>Nuevo</sup>  | General |
| Español (Panamá) | es-PA | Male | `es-PA-RobertoNeural` <sup>Nuevo</sup>  | General |
| Español (Paraguay) | es-PY | Female | `es-PY-TaniaNeural` <sup>Nuevo</sup>  | General |
| Español (Paraguay) | es-PY | Male | `es-PY-MarioNeural` <sup>Nuevo</sup>  | General |
| Español (Perú) | es-PE | Female | `es-PE-CamilaNeural` <sup>Nuevo</sup>  | General |
| Español (Perú) | es-PE | Male | `es-PE-AlexNeural` <sup>Nuevo</sup>  | General |
| Español (Puerto Rico) | es-PR | Female | `es-PR-Karina Neural` <sup>Nuevo</sup>  | General |
| Español (Puerto Rico) | es-PR | Male | `es-PR-Victor Neural` <sup>Nuevo</sup>  | General |
| Español (España) | `es-ES` | Female | `es-ES-ElviraNeural` | General |
| Español (España) | `es-ES` | Male | `es-ES-AlvaroNeural` | General |
| Español (Uruguay) | es-UY | Female | `es-UY-ValentinaNeural` <sup>Nuevo</sup>  | General |
| Español (Uruguay) | es-UY | Male | `es-UY-MateoNeural` <sup>Nuevo</sup>  | General |
| Español (EE. UU.) | `es-US` | Female | `es-US-PalomaNeural` | General |
| Español (EE. UU.) | `es-US` | Male | `es-US-AlonsoNeural` | General |
| Español (Venezuela) | es-VE | Female | `es-VE-PaolaNeural` <sup>Nuevo</sup>  | General |
| Español (Venezuela) | es-VE | Male | `es-VE-SebastianNeural` <sup>Nuevo</sup>  | General |
| Sundanés (Indonesia) | su-ID | Female | `su-ID-TutiNeural` <sup>Nuevo</sup>  | General |
| Sundanés (Indonesia) | su-ID | Male | `su-ID-JajangNeural` <sup>Nuevo</sup>  | General |
| Swahili (Kenia) | `sw-KE` | Female | `sw-KE-ZuriNeural` | General |
| Swahili (Kenia) | `sw-KE` | Male | `sw-KE-RafikiNeural` | General |
| Swahili (Tanzania) | sw-TZ | Female | `sw-TZ-RehemaNeural` <sup>Nuevo</sup>  | General |
| Swahili (Tanzania) | sw-TZ | Male | `sw-TZ-DaudiNeural` <sup>Nuevo</sup>  | General |
| Sueco (Suecia) | `sv-SE` | Female | `sv-SE-HilleviNeural` | General |
| Sueco (Suecia) | `sv-SE` | Female | `sv-SE-SofieNeural` | General |
| Sueco (Suecia) | `sv-SE` | Male | `sv-SE-MattiasNeural` | General |
| Tamil (India) | `ta-IN` | Female | `ta-IN-PallaviNeural` | General |
| Tamil (India) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | General |
| Tamil (Singapur) | ta-SG | Female | `ta-SG-VenbaNeural` <sup>Nuevo</sup>  | General |
| Tamil (Singapur) | ta-SG | Male | `ta-SG-AnbuNeural` <sup>Nuevo</sup>  | General |
| Tamil (Sri Lanka) | ta-LK | Female | `ta-LK-SaranyaNeural` <sup>Nuevo</sup>  | General |
| Tamil (Sri Lanka) | ta-LK | Male | `ta-LK-KumarNeural` <sup>Nuevo</sup>  | General |
| Telugu (India) | `te-IN` | Female | `te-IN-ShrutiNeural` | General |
| Telugu (India) | `te-IN` | Male | `te-IN-MohanNeural` | General |
| Tailandés (Tailandia) | `th-TH` | Female | `th-TH-AcharaNeural` | General |
| Tailandés (Tailandia) | `th-TH` | Female | `th-TH-PremwadeeNeural` | General |
| Tailandés (Tailandia) | `th-TH` | Male | `th-TH-NiwatNeural` | General |
| Turco (Turquía) | `tr-TR` | Female | `tr-TR-EmelNeural` | General |
| Turco (Turquía) | `tr-TR` | Male | `tr-TR-AhmetNeural` | General |
| Ucraniano (Ucrania) | `uk-UA` | Female | `uk-UA-PolinaNeural` | General | 
| Ucraniano (Ucrania) | `uk-UA` | Male | `uk-UA-OstapNeural` | General | 
| Urdu (India) | ur-IN | Female | `ur-IN-GulNeural` <sup>Nuevo</sup>  | General |
| Urdu (India) | ur-IN | Male | `ur-IN-SalmanNeural` <sup>Nuevo</sup>  | General |
| Urdú (Pakistán) | `ur-PK` | Female | `ur-PK-UzmaNeural`  | General | 
| Urdú (Pakistán) | `ur-PK` | Male | `ur-PK-AsadNeural` | General | 
| Uzbeko (Uzbekistán) | uz-UZ | Female | `uz-UZ-MadinaNeural` <sup>Nuevo</sup>  | General |
| Uzbeko (Uzbekistán) | uz-UZ | Male | `uz-UZ-SardorNeural` <sup>Nuevo</sup>  | General |
| Vietnamita (Vietnam) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | General |
| Vietnamita (Vietnam) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | General |
| Galés (Reino Unido) | `cy-GB` | Female | `cy-GB-NiaNeural` | General | 
| Galés (Reino Unido) | `cy-GB` | Male | `cy-GB-AledNeural` | General | 
| Zulú (Sudáfrica) | zu-ZA | Female | `zu-ZA-ThandoNeural` <sup>Nuevo</sup>  | General |
| Zulú (Sudáfrica) | zu-ZA | Male | `zu-ZA-ThembaNeural` <sup>Nuevo</sup>  | General |

> [!IMPORTANT]
> La voz `en-GB-MiaNeural` en inglés (Reino Unido) se retirará el **30 de octubre de 2021**. Todas las solicitudes de servicio a `en-GB-MiaNeural` se volverán a dirigir automáticamente a `en-GB-SoniaNeural` después del **30 de octubre de 2021**.
> Si usa el contenedor TTS neuronal, [descargue](speech-container-howto.md#get-the-container-image-with-docker-pull) e implemente la versión más reciente antes del **30 de octubre de 2021**. Después del **30 de octubre de 2021**, se rechazarán todas las solicitudes con versiones anteriores.

#### <a name="neural-voices-in-preview"></a>Voces neuronales en versión preliminar

Las siguientes voces neuronales se encuentran en versión preliminar pública. 

| Idioma                         | Configuración regional  | Sexo | Nombre de voz                             | Compatibilidad de estilo |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-JennyMultilingualNeural` <sup>Nuevo</sup> | Funcionalidades generales multilingües disponibles [mediante SSML](speech-synthesis-markup.md#create-an-ssml-document) |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaochenNeural` <sup>Nuevo</sup> | Optimizado para conversaciones espontáneas |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoyanNeural` <sup>Nuevo</sup> | Optimizado para el servicio al cliente |
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoshuangNeural` <sup>Nuevo</sup> | Voz secundaria, optimizada para la historia y el chat secundarios; varios estilos de voz [disponibles mediante SSML](speech-synthesis-markup.md#adjust-speaking-styles).|
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-XiaoqiuNeural` <sup>Nuevo</sup> | Optimizado para la narración |

> [!IMPORTANT]
> Las voces en versión preliminar pública solo están disponibles en tres regiones del servicio: Este de EE. UU., Oeste de Europa y Sudeste de Asia.

> [!TIP]
> `en-US-JennyNeuralMultilingual` admite varios idiomas. Compruebe la [API de la lista de voces](rest-text-to-speech.md#get-a-list-of-voices) para obtener la lista de idiomas admitidos.

Para más información acerca de la disponibilidad regional, consulte las [regiones](regions.md#neural-and-standard-voices).

Para más información sobre cómo configurar y ajustar las voces neuronales, por ejemplo, los estilos de habla, consulte [Lenguaje de marcado de síntesis de voz](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> La voz `en-US-JessaNeural` ha cambiado a `en-US-AriaNeural`. Si usaba "Jessa" antes, conviértalo a "Aria".

> [!TIP]
> Puede seguir usando la asignación de nombre completo de servicio, como "Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)", en sus solicitudes de síntesis de voz.

### <a name="standard-voices"></a>Voces estándar

Hay más de 75 voces estándar disponibles en más de 45 idiomas y configuraciones regionales, lo que le permite convertir texto en voz sintetizada. Para más información acerca de la disponibilidad regional, consulte las [regiones](regions.md#neural-and-standard-voices).

> [!IMPORTANT]
> Vamos a retirar las voces estándar el **31 de agosto de 2024** y ya no se admitirán después de esa fecha.Lo anunciamos en los correos electrónicos enviados a todas las suscripciones de Voz existentes creadas antes del **31 de agosto de 2021**. Durante el período de retirada (**del 31 de agosto de 2021** - **al 31 de agosto de 2024**), los usuarios de voz estándar existentes pueden seguir usando sus voces estándar, pero todos los nuevos usuarios o nuevos recursos de voz deben usar las voces neuronales.

> [!NOTE]
> Con dos excepciones, las voces neuronales estándar se crean a partir de ejemplos que usan una frecuencia de muestreo de 16 kHz.
> Las voces **en-US-AriaRUS** y **en-US-GuyRUS** también se crean a partir de ejemplos que usan una frecuencia de muestreo de 24 kHz.
> Todas las voces pueden aumentar o reducir las frecuencias de muestreo al sintetizar.

| Idioma | Configuración regional (BCP-47) | Sexo | Nombre de voz |
|--|--|--|--|
| Árabe (árabe) | `ar-EG` | Female | `ar-EG-Hoda`|
| Árabe (Arabia Saudí) | `ar-SA` | Male | `ar-SA-Naayf`|
| Búlgaro (Bulgaria) | `bg-BG` | Male | `bg-BG-Ivan`|
| Catalán (España) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Chino (cantonés, tradicional) | `zh-HK` | Male | `zh-HK-Danny`|
| Chino (cantonés, tradicional) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Chino (mandarín, simplificado) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Chino (mandarín, simplificado) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Chino (mandarín, Taiwán) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Chino (mandarín, Taiwán) |  `zh-TW` | Female | `zh-TW-Yating`|
| Chino (mandarín, Taiwán) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| Croata (Croacia) | `hr-HR` | Male | `hr-HR-Matej`|
| Checo (República Checa) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| Danés (Dinamarca) | `da-DK` | Female | `da-DK-HelleRUS`|
| Neerlandés (Países Bajos) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Inglés (Australia) | `en-AU` | Female | `en-AU-Catherine`|
| Inglés (Australia) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Inglés (Canadá) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Inglés (Canadá) | `en-CA` | Female | `en-CA-Linda`|
| Inglés (India) | `en-IN` | Female | `en-IN-Heera`|
| Inglés (India) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Inglés (India) | `en-IN` | Male | `en-IN-Ravi`|
| Inglés (Irlanda) | `en-IE` | Male | `en-IE-Sean`|
| Inglés (Reino Unido) | `en-GB` | Male | `en-GB-George`|
| Inglés (Reino Unido) | `en-GB` | Female | `en-GB-HazelRUS`|
| Inglés (Reino Unido) | `en-GB` | Female | `en-GB-Susan`|
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-BenjaminRUS`|
| Spanish (Traditional Sort) - Spain | `en-US` | Male | `en-US-GuyRUS`|
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-AriaRUS`|
| Spanish (Traditional Sort) - Spain | `en-US` | Female | `en-US-ZiraRUS`|
| Finés (Finlandia) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Francés (Canadá) | `fr-CA` | Female | `fr-CA-Caroline`|
| Francés (Canadá) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| Francés (Francia) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| Francés (Francia) | `fr-FR` | Female | `fr-FR-Julie`|
| Francés (Francia) | `fr-FR` | Male | `fr-FR-Paul`|
| Francés (Suiza) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Alemán (Austria) | `de-AT` | Male | `de-AT-Michael`|
| Alemán (Alemania) | `de-DE` | Female | `de-DE-HeddaRUS`|
| Alemán (Alemania) | `de-DE` | Male | `de-DE-Stefan`|
| Alemán (Suiza) | `de-CH` | Male | `de-CH-Karsten`|
| Griego (Grecia) | `el-GR` | Male | `el-GR-Stefanos`|
| Hebreo (Israel) | `he-IL` | Male | `he-IL-Asaf`|
| Hindi (India) | `hi-IN` | Male | `hi-IN-Hemant`|
| Hindi (India) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Húngaro (Hungría) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonesio (Indonesia) | `id-ID` | Male | `id-ID-Andika`|
| Italiano (Italia) | `it-IT` | Male | `it-IT-Cosimo`|
| Italiano (Italia) | `it-IT` | Female | `it-IT-LuciaRUS`|
| Japonés (Japón) | `ja-JP` | Female | `ja-JP-Ayumi`|
| Japonés (Japón) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| Japonés (Japón) | `ja-JP` | Male | `ja-JP-Ichiro`|
| Coreano (Corea) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| Malayo (Malasia) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Noruego (Bokmål, Noruega) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| Polaco (Polonia) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portugués (Brasil) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portugués (Brasil) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| Portugués (Portugal) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| Rumano (Rumanía) | `ro-RO` | Male | `ro-RO-Andrei`|
| Ruso (Rusia) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| Ruso (Rusia) | `ru-RU` | Female | `ru-RU-Irina`|
| Ruso (Rusia) | `ru-RU` | Male | `ru-RU-Pavel`|
| Eslovaco (Eslovaquia) | `sk-SK` | Male | `sk-SK-Filip`|
| Esloveno (Eslovenia) | `sl-SI` | Male | `sl-SI-Lado`|
| Español (México) | `es-MX` | Female | `es-MX-HildaRUS`|
| Español (México) | `es-MX` | Male | `es-MX-Raul`|
| Español (España) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Español (España) | `es-ES` | Female | `es-ES-Laura`|
| Español (España) | `es-ES` | Male | `es-ES-Pablo`|
| Sueco (Suecia) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamil (India) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Telugu (India) | `te-IN` | Female | `te-IN-Chitra`|
| Tailandés (Tailandia) | `th-TH` | Male | `th-TH-Pattara`|
| Turco (Turquía) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamita (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> La voz `en-US-Jessa` ha cambiado a `en-US-Aria`. Si usaba "Jessa" antes, conviértalo a "Aria".

> [!TIP]
> Puede seguir usando la asignación de nombre completo de servicio, como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)", en sus solicitudes de síntesis de voz.

### <a name="customization"></a>Personalización

Voz personalizada está disponible en el nivel neuronal (conocido también como Voz neuronal personalizada). En función de la tecnología TTS neuronal y el modelo universal multilingüe de varios hablantes, Voz neuronal personalizada permite crear voces sintéticas ricas en estilos de habla o en idiomas cruzados adaptables. Compruebe a continuación los idiomas admitidos.  

> [!IMPORTANT]
> El nivel estándar, incluidos los métodos de entrenamiento concatenativo y paramétrico estadístico de la voz personalizada, quedará en desuso y se retirará el 29/2/2024. Si usa Voz personalizada estándar o no neuronal, migre a Voz neuronal personalizada inmediatamente para disfrutar de la mejor calidad e implementar las voces de forma responsable. 

| Idioma | Configuración regional | Neuronal | Multilingüe |
|--|--|--|--|
| Búlgaro (Bulgaria)| `bg-BG` | Sí | No |
| Chino (mandarín, simplificado) | `zh-CN` | Sí | Sí |
| Chino (mandarín, simplificado), inglés bilingüe | `zh-CN` bilingüe | Sí | Sí |
| Neerlandés (Países Bajos)   | `nl-NL` | Sí | No |
| Inglés (Australia) | `en-AU` | Sí | Sí |
| Inglés (India) | `en-IN` | Sí | No |
| Inglés (Reino Unido) | `en-GB` | Sí | Sí |
| Spanish (Traditional Sort) - Spain | `en-US` | Sí | Sí |
| Francés (Canadá) | `fr-CA` | Sí | Sí |
| Francés (Francia) | `fr-FR` | Sí | Sí |
| Alemán (Alemania) | `de-DE` | Sí | Sí |
| Italiano (Italia) | `it-IT` | Sí | Sí |
| Japonés (Japón) | `ja-JP` | Sí | Sí |
| Coreano (Corea) | `ko-KR` | Sí | Sí |
| Noruego (Bokmål, Noruega) | `nb-NO` | Sí | No |
| Portugués (Brasil) | `pt-BR` | Sí | Sí |
| Ruso (Rusia) | `ru-RU` | Sí | Sí |
| Español (México) | `es-MX` | Sí | Sí |
| Español (España) | `es-ES` | Sí | Sí |

Seleccione la configuración regional adecuada que coincida con los datos de entrenamiento que tiene para entrenar un modelo de voz personalizado. Por ejemplo, si los datos de grabación que tienen que hablar en inglés con acento británico, seleccione `en-GB`.

> [!NOTE]
> No se admite el entrenamiento de modelos bilingües en la funcionalidad de voz personalizada, excepto en el caso de chino-inglés bilingüe. Seleccione la opción de chino-inglés bilingüe si quiere entrenar una voz china que pueda hablar también inglés. El entrenamiento de modelos de chino-inglés bilingüe con el método estándar está disponible únicamente en Norte de Europa y Centro y norte de EE. UU. El entrenamiento de Voz neuronal personalizada está disponible en Sur de Reino Unido y Este de EE. UU.

## <a name="speech-translation"></a>Traducción de voz

**Speech Translation** API admite varios idiomas para la traducción de voz a voz y de texto a voz. El idioma de origen debe incluirse siempre en la siguiente tabla de conversión de voz en texto. Los idiomas de destino admitidos dependen de si el destino de traducción es voz o texto. Puede traducir la voz entrante a cualquiera de los [idiomas admitidos](https://www.microsoft.com/translator/business/languages/). Un subconjunto de estos idiomas está disponible para la [síntesis de voz](language-support.md#text-languages).

### <a name="text-languages"></a>Idiomas de texto

| Idioma de texto           | Código de lenguaje |
|:------------------------|:-------------:|
| Afrikáans | `af` |
| Albanés | `sq` |
| Amárico | `am` |
| Árabe | `ar` |
| Armenio | `hy` |
| Asamés | `as` |
| Azerbaiyano | `az` |
| Bengalí | `bn` |
| Bosnio (latino) | `bs` |
| Búlgaro | `bg` |
| Cantonés (tradicional) | `yue` |
| Catalán | `ca` |
| Chino (clásico) | `lzh` |
| Chino simplificado | `zh-Hans` |
| Chino tradicional | `zh-Hant` |
| Croata | `hr` |
| Checo | `cs` |
| Danés | `da` |
| Dari | `prs` |
| Neerlandés | `nl` |
| Inglés | `en` |
| Estonio | `et` |
| Fiyiano | `fj` |
| Filipino | `fil` |
| Finés | `fi` |
| Francés | `fr` |
| Francés (Canadá) | `fr-ca` |
| Alemán | `de` |
| Griego | `el` |
| Gujarati | `gu` |
| Criollo haitiano | `ht` |
| Hebreo | `he` |
| Hindi | `hi` |
| Hmong Daw | `mww` |
| Húngaro | `hu` |
| Islandés | `is` |
| Indonesio | `id` |
| Inuktitut | `iu` |
| Irlandés | `ga` |
| Italiano | `it` |
| Japonés | `ja` |
| Canarés | `kn` |
| Kazajo | `kk` |
| Jemer | `km` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Coreano | `ko` |
| Kurdo (central) | `ku` |
| Kurdo (norte) | `kmr` |
| Lao | `lo` |
| Letón | `lv` |
| Lituano | `lt` |
| Malgache | `mg` |
| Malayo | `ms` |
| Malayalam | `ml` |
| Maltés | `mt` |
| Maori | `mi` |
| Maratí | `mr` |
| Myanmar | `my` |
| Nepalí | `ne` |
| Noruego | `nb` |
| Odia | `or` |
| Pastún | `ps` |
| Persa | `fa` |
| Polaco | `pl` |
| Portugués (Brasil) | `pt` |
| Portugués (Portugal) | `pt-pt` |
| Punjabi | `pa` |
| Otomí Querétaro | `otq` |
| Rumano | `ro` |
| Ruso | `ru` |
| Samoano | `sm` |
| Serbio (cirílico) | `sr-Cyrl` |
| Serbio (latino) | `sr-Latn` |
| Eslovaco | `sk` |
| Esloveno | `sl` |
| Español | `es` |
| Swahili | `sw` |
| Sueco | `sv` |
| Tahitiano | `ty` |
| Tamil | `ta` |
| Telugu | `te` |
| Tailandés | `th` |
| Tigriña | `ti` |
| Tongano | `to` |
| Turco | `tr` |
| Ucraniano | `uk` |
| Urdu | `ur` |
| Vietnamita | `vi` |
| Galés | `cy` |
| Maya Yucateco | `yua` |

## <a name="speaker-recognition"></a>Speaker Recognition

El reconocimiento del hablante es principalmente independiente del lenguaje. Hemos creado un modelo universal para el reconocimiento del hablante independiente del texto mediante la combinación de varios orígenes de datos de diversos idiomas. Hemos optimizado y evaluado el modelo en los idiomas y configuraciones regionales que aparecen en la tabla siguiente. Consulte la [introducción](speaker-recognition-overview.md) para obtener información adicional sobre Speaker Recognition.

| Idioma | Configuración regional (BCP-47) | Comprobación dependiente del texto | Comprobación independiente del texto | Identificación independiente del texto |
|----|----|----|----|----|
|Inglés (EE.UU.)  |  `en-US`  |  sí  |  sí  |  sí |
|Chino (mandarín, simplificado) | `zh-CN`     |     N/D |     sí |     sí|
|Inglés (Australia)     | `en-AU`    | N/D     | sí     | sí|
|Inglés (Canadá)     | `en-CA`     | N/D |     sí |     sí|
|Inglés (India)     | `en-IN`     | N/D |     sí |     sí|
|English (Reino Unido)     | `en-GB`     | N/D     | sí     | sí|
|Francés (Canadá)     | `fr-CA`     | N/D     | sí |     sí|
|Francés (Francia)     | `fr-FR`     | N/D     | sí     | sí|
|Alemán (Alemania)     | `de-DE`     | N/D     | sí     | sí|
|Italiano | `it-IT`     |     N/D     | sí |     sí|
|Japonés     | `ja-JP` | N/D     | sí     | sí|
|Portugués (Brasil) | `pt-BR` |     N/D |     sí |     sí|
|Español (México)     | `es-MX`     | N/D |     sí |     sí|
|Español (España)     | `es-ES` | N/D     | sí |     sí|

## <a name="custom-keyword-and-keyword-verification"></a>Palabra clave personalizada y verificación de palabras clave

En la tabla siguiente se detallan los idiomas admitidos para la palabra clave personalizada y la verificación de palabras clave.

| Idioma | Configuración regional (BCP-47) | Palabra clave personalizada | Verificación de palabras clave |
| -------- | --------------- | -------------- | -------------------- |
| Chino (mandarín, simplificado) | zh-CN | Sí | Sí |
| Spanish (Traditional Sort) - Spain | es-ES | Sí | Sí |
| Japonés (Japón) | ja-JP | No | Sí |
| Portugués (Brasil) | pt-BR | No | Sí |

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una cuenta de Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
* [Vea cómo funciona el reconocimiento de voz en C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
