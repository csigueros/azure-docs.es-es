---
title: Compatibilidad de idiomas de Detección de idioma
titleSuffix: Azure Cognitive Services
description: En este artículo se explica qué idiomas naturales son compatibles con la API Detección de idioma.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 1c71e3d04ff770aefc50732c18fe49bd684e854a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093559"
---
# <a name="language-support-for-language-detection"></a>Compatibilidad de idiomas de Detección de idioma

Use este artículo para saber qué idiomas naturales son compatibles con Detección de idioma.


> [!NOTE]
> Los idiomas se agregan a medida que se lanzan nuevas [versiones del modelo](how-to/call-api.md#specify-the-language-detection-model). La versión del modelo actual para Detección de idioma es `2021-01-05`.

La característica Detección de idioma puede detectar una amplia variedad de idiomas, variantes, dialectos y algunos idiomas regionales o culturales, y puede devolver idiomas con su nombre y código. Los parámetros de código de idioma devueltos se ajustan al estándar [BCP-47](https://tools.ietf.org/html/bcp47) y la mayoría de ellos cumplen con los identificadores [ISO-639-1](https://www.iso.org/iso-639-language-codes.html). 

Si tiene contenido que se expresa en un idioma que se usa con menos frecuencia, puede probar Detección de idioma para ver si devuelve un código. La respuesta para los idiomas que no se pueden detectar es `unknown`.

## <a name="languages-supported-by-language-detection"></a>Idiomas admitidos por Detección de idioma

| Idioma            | Código de lenguaje | A partir de la versión del modelo: |
|---------------------|---------------|------------------------------|
| Afrikáans           | `af`          |                              |
| Albanés            | `sq`          |                              |
| Amárico             | `am`          | 05-01-2021                   |
| Árabe              | `ar`          |                              |
| Armenio            | `hy`          |                              |
| Asamés            | `as`          | 05-01-2021                   |
| Azerbaiyano         | `az`          | 05-01-2021                   |
| Vasco              | `eu`          |                              |
| Bielorruso          | `be`          |                              |
| Bengalí             | `bn`          |                              |
| Bosnio             | `bs`          | 01-09-2020                   |
| Búlgaro           | `bg`          |                              |
| Birmano             | `my`          |                              |
| Catalán             | `ca`          |                              |
| Camboyano       | `km`          |                              |
| Chino             | `zh`          |                              |
| Chino simplificado  | `zh_chs`      |                              |
| Chino tradicional | `zh_cht`      |                              |
| Corso            | `co`          | 05-01-2021                   |
| Croata            | `hr`          |                              |
| Checo               | `cs`          |                              |
| Danés              | `da`          |                              |
| Dari                | `prs`         | 01-09-2020                   |
| Divehi              | `dv`          |                              |
| Neerlandés               | `nl`          |                              |
| Inglés             | `en`          |                              |
| Esperanto           | `eo`          |                              |
| Estonio            | `et`          |                              |
| Fiyiano              | `fj`          | 01-09-2020                   |
| Finés             | `fi`          |                              |
| Francés              | `fr`          |                              |
| Gallego            | `gl`          |                              |
| Georgiano            | `ka`          |                              |
| Alemán              | `de`          |                              |
| Griego               | `el`          |                              |
| Gujarati            | `gu`          |                              |
| Haitiano             | `ht`          |                              |
| Hausa               | `ha`          | 05-01-2021                   |
| Hebreo              | `he`          |                              |
| Hindi               | `hi`          |                              |
| Hmong Daw           | `mww`         | 01-09-2020                   |
| Húngaro           | `hu`          |                              |
| Islandés           | `is`          |                              |
| Igbo                | `ig`          | 05-01-2021                   |
| Indonesio          | `id`          |                              |
| Inuktitut           | `iu`          |                              |
| Irlandés               | `ga`          |                              |
| Italiano             | `it`          |                              |
| Japonés            | `ja`          |                              |
| Javanés            | `jv`          | 05-01-2021                   |
| Canarés             | `kn`          |                              |
| Kazajo              | `kk`          | 01-09-2020                   |
| Kinyarwanda         | `rw`          | 05-01-2021                   |
| Kirguís             | `ky`          | 05-01-2021                   |
| Coreano              | `ko`          |                              |
| Kurdo             | `ku`          |                              |
| Lao                 | `lo`          |                              |
| Latín               | `la`          |                              |
| Letón             | `lv`          |                              |
| Lituano          | `lt`          |                              |
| Luxemburgués       | `lb`          | 05-01-2021                   |
| Macedonio          | `mk`          |                              |
| Malgache            | `mg`          | 01-09-2020                   |
| Malayo               | `ms`          |                              |
| Malayalam           | `ml`          |                              |
| Maltés             | `mt`          |                              |
| Maori               | `mi`          | 01-09-2020                   |
| Maratí             | `mr`          | 01-09-2020                   |
| Mongol           | `mn`          | 05-01-2021                   |
| Nepalí              | `ne`          | 05-01-2021                   |
| Noruego           | `no`          |                              |
| Noruego nynorsk   | `nn`          |                              |
| Odia               | `or`          |                              |
| Pastún               | `ps`          |                              |
| Persa             | `fa`          |                              |
| Polaco              | `pl`          |                              |
| Portugués          | `pt`          |                              |
| Punjabi             | `pa`          |                              |
| Otomí Querétaro     | `otq`         | 01-09-2020                   |
| Rumano            | `ro`          |                              |
| Ruso             | `ru`          |                              |
| Samoano              | `sm`          | 01-09-2020                   |
| Serbio             | `sr`          |                              |
| Shona               | `sn`          | 05-01-2021                   |
| Sindhi              | `sd`          | 05-01-2021                   |
| Cingalés             | `si`          |                              |
| Eslovaco              | `sk`          |                              |
| Esloveno           | `sl`          |                              |
| Somalí              | `so`          |                              |
| Español             | `es`          |                              |
| Sundanés           | `su`          | 05-01-2021                   |
| Swahili             | `sw`          |                              |
| Sueco             | `sv`          |                              |
| Tagalo             | `tl`          |                              |
| Tahitiano            | `ty`          | 01-09-2020                   |
| Tayiko               | `tg`          | 05-01-2021                   |
| Tamil               | `ta`          |                              |
| Tatar               | `tt`          | 05-01-2021                   |
| Telugu              | `te`          |                              |
| Tailandés                | `th`          |                              |
| Tibetano             | `bo`          | 05-01-2021                   |
| Tigriña            | `ti`          | 05-01-2021                   |
| Tongano              | `to`          | 01-09-2020                   |
| Turco             | `tr`          | 05-01-2021                   |
| Turcomano             | `tk`          | 05-01-2021                   |
| Ucraniano           | `uk`          |                              |
| Urdu                | `ur`          |                              |
| Uzbeko               | `uz`          |                              |
| Vietnamita          | `vi`          |                              |
| Galés               | `cy`          |                              |  
| Xhosa               | `xh`          | 05-01-2021                   |
| Yidis             | `yi`          |                              |
| Yoruba              | `yo`          | 05-01-2021                   |
| Maya Yucateco        | `yua`         |                              |  
| Zulú                | `zu`          | 05-01-2021                   |

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la detección de idiomas](overview.md)
