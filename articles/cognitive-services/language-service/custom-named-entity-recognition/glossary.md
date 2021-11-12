---
title: Definiciones y términos usados en el Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre algunas de las definiciones y los términos que se pueden encontrar al usar el Reconocimiento de entidades con nombre (NER) personalizado
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c57d4efffc207759b8d3da343bda397ee885dc0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439089"
---
# <a name="custom-named-entity-recognition-ner-definitions-and-terms"></a>Definiciones y términos del Reconocimiento de entidades con nombre (NER) personalizado

Use este artículo para obtener información sobre algunas de las definiciones y los términos que se pueden encontrar al usar NER personalizado.

## <a name="project"></a>Proyecto

Un proyecto es un área de trabajo para compilar modelos de inteligencia artificial personalizados basados en los datos. A su proyecto solo puede acceder usted y otros usuarios que tengan acceso de colaborador al recurso de Azure que está usando.
Como requisito previo para crear un proyecto de extracción de entidades personalizado debe conectar el recurso a una cuenta de almacenamiento con el conjunto de datos al [crear un nuevo proyecto](how-to/create-project.md). El proyecto incluye automáticamente todos los archivos `.txt` disponibles en el contenedor.

Dentro del proyecto se puede hacer lo siguiente:

* **Etiquetar los datos**: proceso de etiquetado de los datos para que, al entrenar el modelo, aprenda lo que quiere extraer.
* **Compilar y entrenar el modelo**: paso principal del proyecto, donde el modelo comienza a aprender de los datos etiquetados. 
* **Ver los detalles de evaluación del modelo**: revise el rendimiento del modelo para decidir si es posible mejorarlo o si está satisfecho con los resultados.
* **Mejorar el modelo**: cuando se sabe qué ha ido mal con el modelo y cómo se puede mejorar el rendimiento. 
* **Implementar el modelo**: ponga el modelo a disposición de todos para su uso. 
* **Probar el modelo**: pruebe el modelo en un conjunto de datos.

## <a name="model"></a>Modelo

Un modelo es un objeto que se ha entrenado para realizar una tarea determinada, en este caso el reconocimiento de entidades con nombre personalizadas.

* El **entrenamiento del modelo** es el proceso de enseñar al modelo qué extraer en función de los datos etiquetados.
* La **evaluación del modelo** es el proceso que se produce justo después del entrenamiento para saber cómo funciona el modelo.
* La **implementación del modelo** es el proceso de hacer que esté disponible para su uso.

## <a name="entity"></a>Entidad

Una entidad es un fragmento de texto que indica un determinado tipo de información. El fragmento de texto puede constar de una o varias palabras (o tokens). En el Reconocimiento de entidades con nombre (NER) personalizado, las entidades representan la información que se quiere extraer del texto. 

Por ejemplo, en la frase "*John pidió prestados 25 000 USD a Fred.* " las entidades podrían ser: 

| Nombre o tipo de entidad | Entidad |
| -- | -- |
| Nombre del prestatario | *John* |
| Nombre del prestador | *Fred* |
| Importe del préstamo | *25 000 USD* |

## <a name="next-steps"></a>Pasos siguientes

* [Límites de servicio y datos](service-limits.md).
* [Introducción a NER personalizado](../overview.md).
