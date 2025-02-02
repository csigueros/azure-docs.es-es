---
title: Idiomas admitidos en la clasificación personalizada de texto
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los idiomas admitidos en la extracción de entidades personalizadas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 13e031c502538fa2126e6d4ab4e9e4eb49616142
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509088"
---
# <a name="language-support"></a>Compatibilidad con idiomas

Utilice este artículo para conocer los idiomas admitidos en la clasificación personalizada de texto.

## <a name="multiple-language-support"></a>Compatibilidad con varios idiomas

Con la clasificación personalizada de texto, puede entrenar un modelo en un idioma y probarlo en otro idioma. Esta característica es muy eficaz porque ayuda a ahorrar tiempo y esfuerzo, en lugar de crear proyectos independientes para cada idioma, puede controlar el conjunto de datos multilingüe en un proyecto.  El conjunto de datos no tiene que estar completamente en el mismo idioma, pero tiene que especificar esta opción en la creación del proyecto. Si observa que el modelo funciona de forma deficiente en determinados idiomas durante el proceso de evaluación, considere la posibilidad de agregar más datos en este idioma al conjunto de entrenamiento.

> [!NOTE]
> Para habilitar la compatibilidad con varios idiomas, debe habilitar esta opción al [crear el proyecto](how-to/create-project.md) o habilitarla más adelante en la página de configuración del proyecto.

## <a name="languages-supported-by-custom-text-classification"></a>Idiomas admitidos en la clasificación personalizada de texto

La clasificación personalizada de texto admite archivos `.txt` en los siguientes idiomas:

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

* [Introducción a la clasificación personalizada de texto](overview.md)
* [Límites de datos](service-limits.md)
