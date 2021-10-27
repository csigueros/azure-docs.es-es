---
title: Compatibilidad de idiomas con Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conozca mejor los idiomas humanos que están disponibles con Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: 5c9df61e7f4430a623f354294863b2a1bc06b7c8
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164263"
---
# <a name="language-support-for-form-recognizer"></a>Compatibilidad de idiomas con Form Recognizer

 En esta tabla se enumeran los idiomas escritos que admite el servicio Form Recognizer.

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

## <a name="layout-and-custom-model"></a>Diseño y modelo personalizado

En las listas siguientes se incluyen los idiomas de disponibilidad general actuales de la versión 2.1 y las nuevas versiones preliminares de la versión preliminar 3.0 de Form Recognizer. Estos idiomas son compatibles con los modelos de diseño y personalizados. La versión preliminar puede incluir mejoras en los idiomas de disponibilidad general actuales.

> [!NOTE]
> **Código de idioma opcional**
>
> Los modelos universales basados en aprendizaje profundo de Form Recognizer extraen todo el texto multilingüe de los documentos, incluidas las líneas de texto con idiomas combinados, y no requieren especificar un código de idioma. No proporcione el código de idioma como parámetro a menos que esté seguro del idioma y desee forzar al servicio a aplicar solo el modelo correspondiente. De lo contrario, el servicio puede devolver texto incompleto e incorrecto.

Para usar los lenguajes de la versión preliminar en los modelos de diseño y personalizados, consulte la [guía de migración de la API REST v3.0](/v3-migration-guide.md#changes-to-the-rest-api-endpoints) para comprender las diferencias de la API v2.1 de disponibilidad general y explorar los [inicios rápidos del SDK de versión preliminar v3.0](quickstarts/try-v3-python-sdk.md) y el [inicio rápido de la API REST de versión preliminar](quickstarts/try-v3-rest-api.md).

### <a name="handwritten-languages"></a>Idiomas manuscritos
En la tabla siguiente se enumeran los idiomas manuscritos admitidos por las características de modelos de diseño y personalizados de Form Recognizer.

|Lenguaje| Código de idioma (opcional) | ¿Versión preliminar?  |
|:-----|:----:|:----:|
|Inglés|`en`||
|Chino simplificado |`zh-Hans`| Vista previa
|Francés |`fr`| Vista previa
|Alemán |`de`| Vista previa
|Italiano|`it`| Vista previa
|Portugués |`pt`| Vista previa
|Español |`es`| Vista previa

### <a name="print-languages"></a>Idiomas de impresión
En la tabla siguiente se enumeran los idiomas de impresión admitidos por las características de modelos de diseño y personalizados de Form Recognizer.

|Lenguaje| Código de idioma (opcional) | ¿Versión preliminar? |
|:-----|:----:|:----:|
|Afrikáans|`af`||
|Albanés |`sq`||
|Asturiano |`ast`| |
|Azerbaiyano (latino) | `az` | Vista previa |
|Vasco  |`eu`| |
|Bielorruso (cirílico) | `be` | Vista previa |
|Bielorruso (latino) | `be` | Vista previa |
|Bislama   |`bi`| |
|Bosnio (latino)   |`bs`| Vista previa |
|Bretón    |`br`| |
|Búlgaro |`bg`| Vista previa |
|Buriato (cirílico)|`bua`| Vista previa |
|Catalán    |`ca`| |
|Cebuano    |`ceb`| |
|Chamorro  |`ch`| |
|Chino simplificado | `zh-Hans`| |
|Chino tradicional | `zh-Hant`| |
|Córnico     |`kw`| |
|Corso      |`co`| |
|Tártaro de Crimea (Latín)|`crh`| |
|Croata |`hr`| Vista previa |
|Checo | `cs` | |
|Danés | `da` | |
|Neerlandés | `nl` | |
|Inglés | `en` | |
|Erzya (cirílico) |`myv`| Vista previa |
|Estonio  |`et`|  |
|Feroés |`fo`| Vista previa |
|Fiyiano |`fj`| |
|Filipino  |`fil`| |
|Finés | `fi` | |
|Francés | `fr` | |
|Friulano  | `fur` | |
|Gagauzo (latino) |`gag`| Vista previa |
|Gallego   | `gl` | |
|Alemán | `de` | |
|Gilbertés    | `gil` | |
|Groenlandés   | `kl` | |
|Criollo haitiano  | `ht` | |
|Hani  | `hni` | |
|Hawaiano |`haw`| Vista previa |
|Hmong Daw (Latín)| `mww` | |
|Húngaro | `hu` | |
|Islandés |`is`| Vista previa |
|Sami inari |`smn`| Vista previa |
|Indonesio   | `id` | |
|Interlingua  | `ia` | |
|Inuktitut (latino) | `iu` | |
|Irlandés    | `ga` | |
|Italiano | `it` | |
|Japonés | `ja` | |
|Javanés | `jv` | |
|Quiché  | `quc` | |
|Caboverdiano | `kea` | |
|Kachin (Latín) | `kac` | |
|Karachái-bálkaro |`krc`| Vista previa |
|Karakalpako (latino) | `kaa` | |
|Karakalpako (cirílico) | `kaa-cyrl` | Vista previa |
|Casubio | `csb` | |
|Kazajo (cirílico) |`kk-cyrl`| Vista previa |
|Kazajo (latino) |`kk-latn`| Vista previa |
|Khasi  | `kha` |  |
|Coreano | `ko` | |
|Koriakia |`kpy`| Vista previa |
|Kosreano |`kos`| Vista previa |
|Cumuco (cirílico) |`kum`| Vista previa |
|Kurdo (latino)| `kur` | |
|Kirguís (cirílico) |`ky`| Vista previa |
|Lakota |`lkt`| Vista previa |
|Latín|`la`| Vista previa |
|Lituano|`lt`| Vista previa |
|Bajo sorbio|`dsb`| Vista previa |
|Sami lule|`smj`| Vista previa |
|Luxemburgués  | `lb` |  |
|Malayo (latino) | `ms` |  |
|Maltés|`mt`| Vista previa |
|Manx  | `gv` |  |
|Maori|`mi`| Vista previa |
|Mongol (cirílico)|`mn`| Vista previa |
|Montenegrino (cirílico)|`cnr-cyrl`| Vista previa |
|Montenegrino (latino)|`cnr-latn`| Vista previa |
|Napolitano   | `nap` | |
|Niueano|`niu`| Vista previa |
|Nogayo|`nog`| Vista previa |
|Sami septentrional (latino)|`sme`| Vista previa |
|Noruego | `no` |  |
|Occitano | `oc` | |
|Osetio|`os`| Vista previa |
|Polaco | `pl` | |
|Portugués | `pt` | |
|Ripuario|`ksh`| Vista previa |
|Rumano | `ro` | Vista previa |
|Romanche  | `rm` | |
|Ruso | `ru` | Vista previa |
|Samoano (latino)|`sm`| Vista previa |
|Escocés  | `sco` | |
|Gaélico escocés  | `gd` | |
|Serbio (latino) | `sr-latn` | Vista previa |
|Sami skolt|`sms`| Vista previa |
|Eslovaco | `sk` | Vista previa |
|Esloveno  | `slv` | |
|Sami meridional|`sma`| Vista previa |
|Español | `es` | |
|Swahili (Latín)  | `sw` | |
|Sueco | `sv` | |
|Tayiko (cirílico)|`tg`| Vista previa |
|Tártaro (Latín)  | `tat` | |
|Tetum    | `tet` |  |
|Tongano|`to`|(versión preliminar) |
|Turco | `tr` | |
|Turcomano (latino)|`tk`| Vista previa |
|Tuvano|`tyv`| Vista previa |
|Alto sorbio  | `hsb` | |
|Uzbeko (cirílico)  | `uz-cyrl` |  |
|Uzbeko (latino)     | `uz` |  |
|Volapük   | `vo` | |
|Walser    | `wae` |  |
|Galés     | `cy` | Vista previa |
|Frisón occidental | `fy` |  |
|Maya Yucateco | `yua` |  |
|Zhuang | `za` | |
|Zulú  | `zu` |  |

## <a name="receipt-and-business-card-models"></a>Modelos de recibos y tarjetas de presentación

>[!NOTE]
 > No es necesario especificar una configuración regional. Se trata de un parámetro opcional. La tecnología de aprendizaje profundo de Form Recognizer detectará automáticamente el idioma del texto de la imagen.

Los recibos compilados previamente y las tarjetas de visita admiten todas las tarjetas de visita y recibos en inglés con las siguientes configuraciones regionales:

|Lenguaje| Código de configuración regional |
|:-----|:----:|
|Inglés (Australia)|`en-au`|
|Inglés (Canadá)|`en-ca`|
|Inglés (Reino Unido)|`en-gb`|
|Inglés (India)|`en-in`|
|Spanish (Traditional Sort) - Spain| `en-us`|

## <a name="invoice-model"></a>Modelo de factura

Lenguaje| Código de configuración regional |
|:-----|:----:|
|Spanish (Traditional Sort) - Spain|es-es|

## <a name="id-documents"></a>Documentos de identificación

Esta tecnología está disponible actualmente para los permisos de conducir de EE. UU. y la página de información personal de los pasaportes internacionales (excepto los visados y otros documentos de viajes).

> [!div class="nextstepaction"]
> [Probar Form Recognizer](https://aka.ms/fott-2.1-ga)

## <a name="general-document"></a>Documento general

Lenguaje| Código de configuración regional |
|:-----|:----:|
|Spanish (Traditional Sort) - Spain|es-es|

