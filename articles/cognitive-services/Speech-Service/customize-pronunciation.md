---
title: Personalización de la pronunciación
description: Use fonemas para personalizar la pronunciación de palabras en la conversión de voz en texto.
author: ut-karsh
ms.author: umaheshwari
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/19/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 6b8f2339668656ec067bae234d21ec4bf2d5741c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017639"
---
# <a name="customize-pronunciation"></a>Personalización de la pronunciación

Habla personalizada le permite proporcionar diferentes pronunciaciones para palabras específicas mediante el conjunto de segmentos acústicos universal. El conjunto de segmentos acústicos universal (UPS, por sus siglas en inglés) es un conjunto de fonos legibles por máquina que se basa en el alfabeto fonético internacional (AFI). Los lingüistas de todo el mundo usan el AFI y se acepta como estándar.

Las pronunciaciones del UPS consisten en una cadena de fonos de UPS, cada uno separado por un espacio en blanco. El conjunto de fonos distingue mayúsculas de minúsculas. Todas las etiquetas de fonos del UPS se definen mediante cadenas de caracteres ASCII.

Para conocer los pasos para implementar UPS, consulte [Datos de texto estructurados para conjuntos de fonos de entrenamiento](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview).

Estos datos de texto estructurados no son lo mismo que los [archivos de pronunciación](how-to-custom-speech-test-and-train.md#pronunciation-data-for-training), y no se pueden usar juntos.

## <a name="languages-supported"></a>Idiomas admitidos

Use la siguiente tabla para ir al UPS del idioma correspondiente.

| Idioma                | Configuración regional  |
|-------------------------|---------|
| [Inglés (Estados Unidos)](phone-sets.md) | `en-US` |


## <a name="next-steps"></a>Pasos siguientes

* [Proporcionar pronunciación de UPS para Habla personalizada](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)
