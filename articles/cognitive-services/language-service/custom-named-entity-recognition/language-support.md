---
title: Compatibilidad de idiomas y regiones para Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los idiomas y regiones que admite Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: references_regions, language-service-custom-ner, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 996a04ae69e8e511a8dd74b5cad82b76eb1c6d8d
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502585"
---
# <a name="language-support-for-custom-named-entity-recognition-ner"></a>Compatibilidad de idiomas para Reconocimiento de entidades con nombre (NER) personalizado

Use este artículo para obtener información sobre los idiomas y regiones admitidos actualmente por Reconocimiento de entidades con nombre (NER) personalizado.

## <a name="multiple-language-support"></a>Compatibilidad con varios idiomas

Con NER personalizado, puede entrenar un modelo en un idioma y probarlo en otro. Esta característica es muy eficaz porque ayuda a ahorrar tiempo y esfuerzo, en lugar de crear proyectos independientes para cada idioma, puede controlar el conjunto de datos multilingüe en un proyecto. El conjunto de datos no tiene que estar completamente en el mismo idioma, pero tiene que especificar esta opción en la creación del proyecto. Si observa que el modelo funciona de forma deficiente en determinados idiomas durante el proceso de evaluación, considere la posibilidad de agregar más datos en este idioma al conjunto de entrenamiento.

> [!NOTE]
> Para habilitar la compatibilidad con varios idiomas, debe habilitar esta opción al [crear el proyecto](how-to/create-project.md) o habilitarla más adelante en la página de configuración del proyecto.

## <a name="language-support"></a>Compatibilidad con idiomas

NER personalizado admite archivos `.txt` en los siguientes idiomas:

| Idioma | Configuración regional |  
|--|--|
| Spanish (Traditional Sort) - Spain |`en-US` |
| Francés (Francia) |`fr-FR` |
| Alemán |`de-DE` |
| Italiano |`it-IT` |
| Español (España) |`es-ES` |
| Portugués (Portugal) | `pt-PT` |
| Portugués (Brasil) | `pt-BR` |

## <a name="next-steps"></a>Pasos siguientes

[Introducción a NER personalizado](overview.md)
