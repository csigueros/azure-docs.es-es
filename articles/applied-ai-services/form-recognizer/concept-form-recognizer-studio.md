---
title: Form Recognizer Studio | Versión preliminar
titleSuffix: Azure Applied AI Services
description: 'Concepto: procesamiento de formularios y documentos, extracción de datos y análisis mediante Form Recognizer Studio (versión preliminar)'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021
ms.openlocfilehash: 938f1cc6058794deba4d6dc5182dbd2cea2a31ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027576"
---
# <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (versión preliminar)

>[!NOTE]
> Form Recognizer Studio está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas.

[Form Recognizer Studio en versión preliminar](https://formrecognizer.appliedai.azure.com/) es una herramienta en línea para explorar, comprender e integrar visualmente características del servicio Form Recognizer en las aplicaciones. Use el [inicio rápido de Form Recognizer Studio](quickstarts/try-v3-form-recognizer-studio.md) para empezar a analizar los documentos con modelos previamente entrenados. Cree modelos de formulario personalizados y haga referencia a los modelos en las aplicaciones mediante la [versión preliminar del SDK de Python](quickstarts/try-v3-python-sdk.md) y otros inicios rápidos.

En la imagen siguiente se muestra la característica del modelo precompilado de facturación en el trabajo.

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Ejemplo precompilado de Form Recognizer":::

## <a name="form-recognizer-studio-features"></a>Características de Form Recognizer Studio

Las siguientes características del servicio Form Recognizer están disponibles en el estudio.

* **Layout**: pruebe esta característica de Azure Form Recognizer para extraer texto, tablas, marcas de selección e información de estructura de documentos (PDF, TIFF) e imágenes (JPG, PNG, BMP). Comience con el [inicio rápido de Layout de Studio](quickstarts/try-v3-form-recognizer-studio.md#layout). Explore con documentos de ejemplo y los suyos propios. Use la visualización interactiva y la salida JSON para comprender cómo funciona la característica. Consulte la [introducción a Layout](concept-layout.md) para más información y empezar a trabajar con el [inicio rápido del SDK de Python para Layout](quickstarts/try-v3-python-sdk.md#try-it-layout-model).

* **Modelos precompilados**: los modelos precompilados de Form Recognizer permiten agregar procesamiento de formularios inteligente a las aplicaciones y los flujos sin necesidad de entrenar ni compilar modelos propios. Comience con el [inicio rápido de ejemplos precompilados de Studio](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models). Explore con documentos de ejemplo y los suyos propios. Use la visualización interactiva, la lista de campos extraídos y la salida JSON para comprender cómo funciona la característica. Consulte la [introducción a los modelos](concept-model-overview.md) para más información y empezar a trabajar con el [inicio rápido del SDK de Python para facturación precompilado](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model).

* **Modelos personalizados**: los modelos personalizados de Form Recognizer permiten extraer campos y valores de modelos entrenados con sus datos, adaptados a sus formularios y documentos. Cree modelos personalizados independientes o combine dos o más modelos personalizados para crear un modelo compuesto para extraer datos de varios tipos de formulario. Comience con el [inicio rápido de modelos precompilados de Studio](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics).  Use el asistente en línea, la interfaz de etiquetado, el paso de entrenamiento y las visualizaciones para comprender cómo funciona la característica. Pruebe el modelo personalizado con los documentos de ejemplo y realice la iteración para mejorar el modelo. Consulte la [introducción a los modelos personalizados](concept-custom.md) para más información y use la [guía de migración de la versión preliminar de Form Recognizer v3.0](v3-migration-guide.md) para empezar a integrar los nuevos modelos con las aplicaciones.

* **Modelos personalizados**: la creación de modelos personalizados de Form Recognizer requiere identificar los campos que se extraerán y etiquetar esos campos antes de entrenar los modelos personalizados. El etiquetado de texto, marcas de selección, datos tabulares y otros tipos de contenido se suele ayudar con una interfaz de usuario para facilitar el flujo de trabajo de entrenamiento. Por ejemplo, use los inicios rápidos [Etiquetado como tablas](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables) y [Etiquetado para detección de firma](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection) para comprender la experiencia de etiquetado en Form Recognizer Studio.

## <a name="next-steps"></a>Pasos siguientes

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para ver las diferencias con respecto a la versión anterior de la API REST.
* Explore nuestros [**inicios rápidos del SDK de versión preliminar**](quickstarts/try-v3-python-sdk.md) para ver las características en vista previa de las aplicaciones con los nuevos SDK.
* Consulte nuestros [**inicios rápidos de la API REST en versión preliminar**](quickstarts/try-v3-rest-api.md) para ver las características en vista previa con la nueva API REST.

> [!div class="nextstepaction"]
> [Inicio rápido de Form Recognizer Studio (versión preliminar)](quickstarts/try-v3-form-recognizer-studio.md)
