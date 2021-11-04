---
title: Etiquetado de expresiones en el reconocimiento del lenguaje conversacional
titleSuffix: Azure Cognitive Services
description: Use este artículo para etiquetar las expresiones en proyectos de reconocimiento del lenguaje conversacional
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 697a42d32fd0af80737ad65a8350b719ffc5c7a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092838"
---
# <a name="how-to-tag-utterances"></a>Etiquetado de expresiones

Una vez que [haya creado un esquema](build-schema.md) para el proyecto, debe agregar expresiones de entrenamiento al proyecto. Las expresiones deben ser similares a las que usarán los usuarios al interactuar con el proyecto. Al agregar una expresión, debe asignar a qué intención pertenece. Una vez agregada la expresión, etiquete las palabras que contiene con las entidades del proyecto. Las etiquetas de las entidades deben ser coherentes entre las distintas expresiones. 

El etiquetado es el proceso de asignar las expresiones a las intenciones y etiquetarlas con entidades. Es conveniente dedicar un tiempo a etiquetar las expresiones: introducir y refinar los datos que entrenarán los modelos de aprendizaje automático subyacentes para el proyecto. Los modelos de aprendizaje automático realizan generalizaciones basadas en los ejemplos que les proporcione. Cuantos más ejemplos proporcione, más puntos de datos tendrá un modelo para realizar mejores generalizaciones.

> [!NOTE]
>  Los componentes aprendidos de una entidad solo se definen cuando se etiquetan expresiones para esa entidad. También puede tener entidades que incluyan _solo_ componentes de una lista o predefinidos sin etiquetar componentes aprendidos. Consulte el artículo sobre los [componentes de entidad](../concepts/entity-components.md) para más información.

Al habilitar varios idiomas en el proyecto, también debe especificar el idioma de la expresión que va a agregar. Como parte de las funcionalidades multilingües del reconocimiento del lenguaje conversacional, puede entrenar el proyecto en un idioma dominante y, a continuación, predecirlo en los otros idiomas disponibles. La adición de ejemplos de otros idiomas aumenta el rendimiento del modelo en dichos idiomas si determina que no está funcionando bien, pero evite duplicar los datos entre los idiomas que desea admitir. 

Por ejemplo, para mejorar el rendimiento de un bot de calendario con los usuarios, un desarrollador podría agregar ejemplos principalmente en inglés y algunos cuantos en español o francés. Podría agregar expresiones como las siguientes:

* "_Set a meeting with **Matt** and **Kevin** **tomorrow** at **12 PM**_". (inglés)
* "_Reply as **tentative** to the **weekly update** meeting_". (inglés)
* "_Cancelar mi **próxima** reunión_". (español)

En los proyectos de flujo de trabajo de orquestación, los datos usados para entrenar intenciones conectadas no se proporcionan dentro del proyecto. En su lugar, el proyecto extrae los datos del servicio conectado (como aplicaciones de LUIS conectadas, proyectos de reconocimiento del lenguaje conversacional o knowledge bases de respuesta a preguntas personalizadas) durante el entrenamiento. No obstante, si crea intenciones que no están conectadas a ningún servicio, aún tendrá que agregar expresiones a esas intenciones.

Por ejemplo, un desarrollador podría crear una intención para cada una de sus aptitudes y conectarla al proyecto de calendario, el proyecto de correo electrónico y la knowledge base de preguntas frecuentes de la empresa correspondientes. 

## <a name="tag-utterances"></a>Etiquetado de expresiones

:::image type="content" source="../media/tag-utterances.png" alt-text="Captura de pantalla de la página para etiquetar expresiones en Language Studio." lightbox="../media/tag-utterances.png":::

En la *sección 1* es donde se agregan las expresiones. Debe seleccionar una de las intenciones de la lista desplegable, el idioma de la expresión (si procede) y la propia expresión. Presione la tecla *Entrar* en el cuadro de texto de la expresión para agregarla.

La *sección 2* incluye las entidades del proyecto. Puede seleccionar cualquiera de las entidades que ha agregado y, a continuación, mantener el puntero sobre el texto para etiquetar las entidades de las expresiones, que se muestran en la *sección 3*. También puede agregar entidades nuevas aquí si presiona el botón **+ Agregar entidad**. También puede ocultar las etiquetas de la entidad dentro de las expresiones. 

La *sección 3* incluye las expresiones agregadas. Si arrastra sobre el texto que desea etiquetar, aparecerá un menú contextual de las entidades.

:::image type="content" source="../media/label-utterance-menu.png" alt-text="Ejemplo de selección de texto para agregar una etiqueta." lightbox="../media/label-utterance-menu.png":::

> [!NOTE]
> A diferencia de LUIS, no se pueden etiquetar entidades superpuestas. Varias entidades no pueden etiquetar los mismos caracteres.

## <a name="filter-utterances"></a>Filtrado de expresiones

Al hacer clic en **Filtro**, solo podrá ver las expresiones asociadas a las intenciones y entidades que seleccione en el panel de filtro.
Al hacer clic en una intención en la página del [esquema de compilación](./build-schema.md), se le dirigirá a la página de **etiquetado de expresiones** con esa intención filtrada automáticamente. 

## <a name="next-steps"></a>Pasos siguientes
* [Entrenamiento y evaluación de modelos](./train-model.md)
