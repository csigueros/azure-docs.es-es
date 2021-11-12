---
title: 'Compatibilidad con idiomas: Computer Vision'
titleSuffix: Azure Cognitive Services
description: 'En este artículo se proporciona una lista de lenguajes naturales que admiten las características de Computer Vision: OCR y análisis de imagen.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: a79954ba0a96f6db6f12af13c6c74d7386e88231
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473421"
---
# <a name="language-support-for-computer-vision"></a>Compatibilidad con idiomas para Computer Vision

Algunas funcionalidades de Computer Vision admiten varios idiomas; las que no se mencionan aquí solo admiten inglés.

## <a name="optical-character-recognition-ocr"></a>Reconocimiento óptico de caracteres (OCR)

Las API de OCR de Computer Vision admiten muchos idiomas. La operación de lectura puede extraer texto de imágenes y documentos con idiomas mixtos, incluida la misma línea de texto sin necesidad de un parámetro de idioma. Consulte [Información general sobre el reconocimiento óptico de caracteres (OCR)](overview-ocr.md) para más información.


> [!NOTE]
> **Código de idioma (opcional)**
>
> Los modelos universales basados en aprendizaje profundo de OCR de Read extraen todo el texto multilingüe de los documentos, incluidas las líneas de texto con idiomas mixtos, y no es necesario especificar un código de idioma. No proporcione el código de idioma como parámetro a menos que esté seguro del idioma y desee forzar al servicio a aplicar solo el modelo correspondiente. De lo contrario, el servicio puede devolver texto incompleto e incorrecto.

El modelo en versión preliminar incluye todas las mejoras de la versión de GA actual de la API. Consulte los [procedimientos para especificar la versión del modelo](./Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional) para usar las características y los idiomas de la versión preliminar. 

### <a name="handwritten-languages"></a>Idiomas manuscritos

En la tabla siguiente se enumeran los idiomas que admite la operación Read para texto manuscrito.

|Lenguaje| Código de idioma (opcional) | Lectura |
|:-----|:----:|:-----|
|Inglés|`en`|✅ |
|Chino simplificado |`zh-Hans`|✅ versión preliminar |
|Francés|`fr`|✅ versión preliminar|
|Alemán |`de`|✅ versión preliminar |
|Italiano|`it`|✅ versión preliminar |
|Portugués |`pt`|✅ versión preliminar |
|Español |`es`|✅ versión preliminar |

### <a name="print-languages"></a>Idiomas de impresión

En la tabla siguiente se enumeran los idiomas que admiten las API de OCR para texto impreso.

|Lenguaje| Código de idioma (opcional) | Lectura | OCR |
|:-----|:----:|:-----|:---:|
|Afrikáans|`af`|✅ | |
|Albanés |`sq`|✅ | |
|Árabe | `ar`|  | ✅ |
|Asturiano |`ast`|✅ | |
|Azerbaiyano (latino) | `az` | ✅ versión preliminar | |
|Vasco  |`eu`| ✅ | |
|Bielorruso (cirílico) | `be` |✅ versión preliminar | |
|Bielorruso (latino) | `be` |✅ versión preliminar | |
|Bislama   |`bi`|✅ | |
|Bosnio (latino)   |`bs`|✅ versión preliminar | |
|Bretón    |`br`|✅ | |
|Búlgaro |`bg`|✅ versión preliminar | |
|Buriato (cirílico)|`bua`|✅ versión preliminar | |
|Catalán    |`ca`|✅ | |
|Cebuano    |`ceb`|✅ | |
|Chamorro  |`ch`|✅| |
|Chino simplificado | `zh-Hans`|✅ |✅ |
|Chino tradicional | `zh-Hant`|✅ |✅ |
|Córnico     |`kw`|✅ | |
|Corso      |`co`|✅ | |
|Tártaro de Crimea (Latín)|`crh`| ✅ | |
|Croata |`hr`|✅ versión preliminar | |
|Checo | `cs` |✅ | ✅ |
|Danés | `da` |✅ | ✅ |
|Neerlandés | `nl` |✅ |✅ |
|Inglés | `en` |✅ |✅|
|Erzya (cirílico) |`myv`|✅ versión preliminar | |
|Estonio  |`et`|✅ | |
|Feroés |`fo`|✅ versión preliminar | |
|Fiyiano |`fj`|✅ | |
|Filipino  |`fil`|✅ | |
|Finés | `fi` |✅ |✅ |
|Francés | `fr` |✅ |✅ |
|Friulano  | `fur` |✅ | |
|Gagauzo (latino) |`gag`|✅ versión preliminar | |
|Gallego   | `gl` |✅ | |
|Alemán | `de` |✅ |✅ |
|Gilbertés    | `gil` |✅ | |
|Griego | `el` | |✅ |
|Groenlandés   | `kl` |✅ | |
|Criollo haitiano  | `ht` |✅ | |
|Hani  | `hni` |✅ | |
|Hawaiano |`haw`|✅ versión preliminar | |
|Hmong Daw (Latín)| `mww` | ✅ | |
|Húngaro | `hu` | ✅ |✅ |
|Islandés |`is`|✅ versión preliminar | |
|Sami inari |`smn`|✅ versión preliminar | |
|Indonesio   | `id` |✅ | |
|Interlingua  | `ia` |✅ | |
|Inuktitut (latino) | `iu` | ✅ | |
|Irlandés    | `ga` |✅ | |
|Italiano | `it` |✅ |✅ |
|Japonés | `ja` |✅ |✅ |
|Javanés | `jv` |✅ | |
|Quiché  | `quc` |✅ | |
|Caboverdiano | `kea` |✅ | |
|Kachin (Latín) | `kac` |✅ | |
|Karakalpako (latino) | `kaa` | ✅ | |
|Karakalpako (cirílico) | `kaa-cyrl` | ✅ versión preliminar | |
|Karachái-bálkaro |`krc`|✅ versión preliminar | |
|Casubio | `csb` |✅ | |
|Kazajo (cirílico) |`kk-cyrl`|✅ versión preliminar | |
|Kazajo (latino) |`kk-latn`|✅ versión preliminar | |
|Khasi  | `kha` | ✅ | |
|Coreano | `ko` |✅ |✅ |
|Koriakia |`kpy`|✅ versión preliminar | |
|Kosreano |`kos`|✅ versión preliminar | |
|Cumuco (cirílico) |`kum`|✅ versión preliminar | |
|Kurdo (latino)| `ku` |✅ | |
|Kirguís (cirílico) |`ky`|✅ versión preliminar | |
|Lakota |`lkt`|✅ versión preliminar | |
|Latín|`la`|✅ versión preliminar | |
|Lituano|`lt`|✅ versión preliminar | |
|Bajo sorbio|`dsb`|✅ versión preliminar | |
|Sami lule|`smj`|✅ versión preliminar | |
|Luxemburgués  | `lb` | ✅ | |
|Malayo (latino) | `ms` | ✅ | |
|Maltés|`mt`|✅ versión preliminar | |
|Manx  | `gv` | ✅ | |
|Maori|`mi`|✅ versión preliminar | |
|Mongol (cirílico)|`mn`|✅ versión preliminar | |
|Montenegrino (cirílico)|`cnr-cyrl`|✅ versión preliminar | |
|Montenegrino (latino)|`cnr-latn`|✅ versión preliminar | |
|Napolitano   | `nap` | ✅ | |
|Niueano|`niu`|✅ versión preliminar | |
|Nogayo|`nog`|✅ versión preliminar | |
|Sami septentrional (latino)|`sme`|✅ versión preliminar | |
|Noruego | `no` | ✅ | |
|Occitano | `oc` | ✅ | |
|Osetio|`os`|✅ versión preliminar | |
|Polaco | `pl` | ✅ |✅ |
|Portugués | `pt` |✅ |✅ |
|Ripuario|`ksh`|✅ versión preliminar | |
|Rumano | `ro` | ✅ versión preliminar | ✅|
|Romanche  | `rm` | ✅ | |
|Ruso | `ru` |✅ versión preliminar |✅ |
|Samoano (latino)|`sm`|✅ versión preliminar | |
|Escocés  | `sco` | ✅ | |
|Gaélico escocés  | `gd` |✅ | |
|Serbio (cirílico) | `sr-cyrl` | |✅ |
|Serbio (latino) | `sr-latn` | ✅ versión preliminar |✅ |
|Sami skolt|`sms`|✅ versión preliminar | |
|Eslovaco | `sk` | ✅ versión preliminar |✅ |
|Esloveno  | `sl` | ✅ ||
|Sami meridional|`sma`|✅ versión preliminar | |
|Español | `es` |✅ |✅ |
|Swahili (Latín)  | `sw` |✅ | |
|Sueco | `sv` |✅ |✅ |
|Tayiko (cirílico)|`tg`|✅ versión preliminar | |
|Tártaro (Latín)  | `tt` | ✅ |
|Tetum    | `tet` |✅ |  |
|Tongano|`to`|✅ versión preliminar | |
|Turco | `tr` |✅ | ✅ |
|Turcomano (latino)|`tk`|✅ versión preliminar | |
|Tuvano|`tyv`|✅ versión preliminar | |
|Alto sorbio  | `hsb` |✅ |  |
|Uzbeko (cirílico)  | `uz-cyrl` |✅ |  |
|Uzbeko (latino)     | `uz` |✅ |  |
|Volapük   | `vo` | ✅ | |
|Walser    | `wae` | ✅ | |
|Galés     | `cy` |✅ versión preliminar |  |
|Frisón occidental | `fy` | ✅ | |
|Maya Yucateco | `yua` | ✅ | |
|Zhuang | `za` |✅ |  |
|Zulú  | `zu` | ✅ | |

## <a name="image-analysis"></a>Análisis de imágenes

Algunas características de la API [Analyze: Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) pueden devolver resultados en otros idiomas, especificados con el parámetro de consulta `language`. Otras acciones devuelven resultados en inglés, independientemente del idioma que se especifique, y otros generan una excepción para los idiomas no admitidos. Las acciones se especifican con los parámetros de consulta `visualFeatures` y `details`; vea la [información general](overview-image-analysis.md) para obtener una lista de todas las acciones que puede hacer con el análisis de imágenes. Los idiomas de etiquetado solo están disponibles en la versión de API 3.2 o posteriores.

|Idioma | Código de lenguaje | Categorías | Etiquetas | Descripción | Adultos | Marcas | Color | Caras | ImageType | Objetos | Celebridades | Puntos de referencia |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Árabe |`ar`| | ✅| |||||| |||
|Azerí (Azerbaiyán) |`az`| | ✅| |||||| |||
|Búlgaro |`bg`| | ✅| |||||| |||
|Bosnio (latino) |`bs`| | ✅| |||||| |||
|Catalán |`ca`| | ✅| |||||| |||
|Checo |`cs`| | ✅| |||||| |||
|Galés |`cy`| | ✅| |||||| |||
|Danés |`da`| | ✅| |||||| |||
|Alemán |`de`| | ✅| |||||| |||
|Griego |`el`| | ✅| |||||| |||
|Inglés |`en`|✅ | ✅| ✅|✅|✅|✅|✅|✅|✅|✅|✅|
|Español |`es`|✅ | ✅| ✅|||||| |✅|✅|
|Estonio |`et`| | ✅| |||||| |||
|Vasco |`eu`| | ✅| |||||| |||
|Finés |`fi`| | ✅| |||||| |||
|Francés |`fr`| | ✅| |||||| |||
|Irlandés |`ga`| | ✅| |||||| |||
|Gallego |`gl`| | ✅| |||||| |||
|Hebreo |`he`| | ✅| |||||| |||
|Hindi |`hi`| | ✅| |||||| |||
|Croata |`hr`| | ✅| |||||| |||
|Húngaro |`hu`| | ✅| |||||| |||
|Indonesio |`id`| | ✅| |||||| |||
|Italiano |`it`| | ✅| |||||| |||
|Japonés  |`ja`|✅ | ✅| ✅|||||| |✅|✅|
|Kazajo |`kk`| | ✅| |||||| |||
|Coreano |`ko`| | ✅| |||||| |||
|Lituano |`It`| | ✅| |||||| |||
|Letón |`Iv`| | ✅| |||||| |||
|Macedonio |`mk`| | ✅| |||||| |||
|Malayo (Malasia) |`ms`| | ✅| |||||| |||
|Noruego (bokmal) |`nb`| | ✅| |||||| |||
|Neerlandés |`nl`| | ✅| |||||| |||
|Polaco |`pl`| | ✅| |||||| |||
|Dari |`prs`| | ✅| |||||| |||
| Portugués (Brasil)|`pt-BR`| | ✅| |||||| |||
| Portugués (Portugal) |`pt`/`pt-PT`|✅ | ✅| ✅|||||| |✅|✅|
|Rumano |`ro`| | ✅| |||||| |||
|Ruso |`ru`| | ✅| |||||| |||
|Eslovaco |`sk`| | ✅| |||||| |||
|Esloveno |`sl`| | ✅| |||||| |||
|Serbio (cirílico, Serbia) |`sr-Cryl`| | ✅| |||||| |||
|Serbio (latino, Serbia) |`sr-Latn`| | ✅| |||||| |||
|Sueco |`sv`| | ✅| |||||| |||
|Tailandés |`th`| | ✅| |||||| |||
|Turco |`tr`| | ✅| |||||| |||
|Ucraniano |`uk`| | ✅| |||||| |||
|Vietnamita |`vi`| | ✅| |||||| |||
|Chino simplificado |`zh`/ `zh-Hans`|✅ | ✅| ✅|||||| |✅|✅|
|Chino tradicional |`zh-Hant`| | ✅| |||||| |||
