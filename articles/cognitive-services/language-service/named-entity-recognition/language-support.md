---
title: Compatibilidad con idiomas de Reconocimiento de entidades con nombre (NER)
titleSuffix: Azure Cognitive Services
description: En este artículo se explica qué idiomas naturales admite la característica NER de Azure Cognitive Service for Language.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 7f80599fe9b0a43b7b472bd6eff71ef11b62fe13
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093553"
---
# <a name="named-entity-recognition-ner-language-support"></a>Compatibilidad con idiomas de Reconocimiento de entidades con nombre (NER) 

Use este artículo para obtener información sobre qué idiomas naturales admite la característica NER de Azure Cognitive Service for Language.

> [!NOTE]
> * Se agregan idiomas a medida que se publican nuevas [versiones del modelo](how-to-call.md#specify-the-ner-model). 
> * Solo se devuelven las entidades "Persona", "Ubicación" y "Organización" para los idiomas marcados con *.
> * La versión del modelo actual para NER es `2021-06-01`.

## <a name="ner-language-support"></a>Compatibilidad con idiomas de NER

| Idioma              | Código de lenguaje | A partir de la versión del modelo: | Notas              |
|:----------------------|:-------------:|:----------------------------:|:------------------:|
| Árabe*               | `ar`          | 2019-10-01                   |                    |
| Chino simplificado    | `zh-hans`     | 15-01-2021                   | También se acepta `zh` |
| Chino (tradicional)*  | `zh-hant`     | 2019-10-01                   |                    |
| Checo*                | `cs`          | 2019-10-01                   |                    |
| Danés*               | `da`          | 2019-10-01                   |                    |
| Neerlandés*                | `nl`          | 2019-10-01                   |                    |
| Inglés               | `en`          | 2019-10-01                   |                    |
| Finés*              | `fi`          | 2019-10-01                   |                    |
| Francés                | `fr`          | 15-01-2021                   |                    |
| Alemán                | `de`          | 15-01-2021                   |                    |
| Hebreo*               | `he`          | 2019-10-01                   |                    |
| Húngaro*            | `hu`          | 2019-10-01                   |                    |
| Italiano               | `it`          | 15-01-2021                   |                    |
| Japonés              | `ja`          | 15-01-2021                   |                    |
| Coreano                | `ko`          | 15-01-2021                   |                    |
| Noruego (Bokmål)*  | `no`          | 2019-10-01                   | También se acepta `nb` |
| Polaco*               | `pl`          | 2019-10-01                   |                    |
| Portugués (Brasil)   | `pt-BR`       | 15-01-2021                   |                    |
| Portugués (Portugal) | `pt-PT`       | 15-01-2021                   | También se acepta `pt` |
| Ruso*              | `ru`          | 2019-10-01                   |                    |
| Español               | `es`          | 2020-04-01                   |                    |
| Sueco*              | `sv`          | 2019-10-01                   |                    |
| Turco*              | `tr`          | 2019-10-01                   |                    |

## <a name="next-steps"></a>Pasos siguientes

[Introducción a las características de DCP](overview.md)
