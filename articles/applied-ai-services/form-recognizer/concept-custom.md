---
title: Modelos personalizados y compuestos de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Aprenda a crear, usar y administrar modelos personalizados y compuestos de Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 181d96d2e722d7f9b79c47285bab417bf0337133
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754679"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Modelos personalizados y compuestos de Form Recognizer

Form Recognizer emplea tecnología avanzada de aprendizaje automático para detectar y extraer información de imágenes de documento y devolver los datos extraídos en una salida JSON estructurada. Con Form Recognizer, puede entrenar modelos personalizados independientes o combinar modelos personalizados para crear modelos compuestos.

* **Modelos personalizados.** Los modelos personalizados de Form Recognizer permiten analizar y extraer datos de formularios y documentos específicos de su negocio. Los modelos personalizados se entrenan para sus distintos datos y casos de uso.

* **Modelos compuestos**. El modelo compuesto se crea tomando una colección de modelos personalizados y asignándolos a un único modelo que abarca sus tipos de formulario. Cuando se envía un documento a un modelo compuesto, el servicio realiza un paso de clasificación para decidir qué modelo personalizado representa con exactitud el formulario presentado para el análisis.

:::image type="content" source="media/analyze.png" alt-text="Captura de pantalla: ventana de análisis de un formulario personalizado de la herramienta Form Recognizer.":::


## <a name="what-is-a-custom-model"></a>¿Qué es un dominio personalizado?

Un modelo personalizado es un programa de aprendizaje automático entrenado para reconocer campos de formulario dentro de su contenido distinto y extraer pares clave-valor y datos de tabla. Solo necesita cinco ejemplos del mismo tipo de formulario para empezar y el modelo personalizado se puede entrenar con o sin conjuntos de datos etiquetados.

## <a name="what-is-a-composed-model"></a>¿Qué es un modelo compuesto?

Con los modelos compuestos, puede asignar varios modelos personalizados a un modelo compuesto llamado con un único identificador de modelo. Esto resulta útil cuando se han entrenado varios modelos y se quieren agrupar para analizar tipos de formulario parecidos. Por ejemplo, el modelo compuesto puede incluir modelos personalizados entrenados para analizar sus pedidos de compra de suministros, equipos y mobiliario. En lugar de intentar seleccionar manualmente el modelo adecuado, puede usar un modelo compuesto para determinar el modelo personalizado adecuado para cada análisis y extracción.

## <a name="try-form-recognizer-studio-preview"></a>Probar Form Recognizer Studio (versión preliminar)

* Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

* Analice formularios de un tipo específico o único con nuestra característica Form Recognizer Studio Custom Form:

> [!div class="nextstepaction"]
> [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Pruebe la herramienta de etiquetado de ejemplo de Form Recognizer

Para ver cómo se extraen datos de formularios personalizados, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

> [!div class="nextstepaction"]
> [Probarlo](https://fott-2-1.azurewebsites.net/projects/create)

En la interfaz de usuario de Form Recognizer:

1. Seleccione **Use Custom to train a model with labels and get key value pairs** (Use Personalizado para entrenar un modelo con etiquetas y obtener pares clave-valor).

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Captura de pantalla: selección de la herramienta FOTT de la opción personalizada.":::

1. En la ventana siguiente, seleccione **Nuevo proyecto**:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Captura de pantalla: selección de FOTTtools selecciona del nuevo proyecto.":::

## <a name="input-requirements"></a>Requisitos de entrada

* Para obtener unos resultados óptimos, proporcione una foto clara o una digitalización de alta calidad por documento.
* Formatos de archivo admitidos: JPEG, PNG, BMP, TIFF y PDF (texto insertado o digitalizado). Los PDF insertados de texto son mejores para eliminar la posibilidad de error en la extracción de caracteres y en la ubicación.
* En el caso de PDF y TIFF, se pueden procesar hasta 2000 páginas (con una suscripción de nivel gratuito, solo se procesan las dos primeras páginas).
* El tamaño del archivo debe ser inferior a 50 MB.
* Las imágenes deben tener unas dimensiones entre 50 x 50 píxeles y 10 000 x 10 000 píxeles.
* Los archivos PDF tienen unas dimensiones de hasta 17 x 17 pulgadas, lo que corresponde a los tamaños de papel Legal o A3, o más pequeños.
* El tamaño total de los datos de entrenamiento es de 500 páginas o menos.
* Si los archivos PDF están bloqueados con contraseña, debe desbloquearlos antes de enviarlos.
* Para un aprendizaje sin supervisión (sin datos etiquetados):
  * Los datos deben contener claves y valores.
  * Las claves deben aparecer por encima o a la izquierda de los valores; no pueden aparecer por debajo ni a la derecha.

  > [!TIP]
  > **Datos de aprendizaje**
  >
  >* Si es posible, use documentos PDF de texto en lugar de documentos basados en imágenes. Los archivos PDF digitalizados se tratan como imágenes.
  > * En el caso de los formularios rellenados, use ejemplos en los que estén todos los campos rellenados.
  > * Use formularios con valores distintos en cada campo.
  >* Si las imágenes de los formularios son de menor calidad, use un conjunto de datos más grande (con entre 10 y 15 imágenes, por ejemplo).

> [!NOTE]
> La [herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/) no admite el formato de archivo BMP. Se trata de una limitación de la herramienta, no del servicio Form Recognizer.

## <a name="supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles

 La versión preliminar de Form Recognizer presenta compatibilidad de idioma adicional para los modelos personalizados. *Consulte* nuestra [compatibilidad de idioma](language-support.md#layout-and-custom-model) para obtener una lista completa de textos manuscritos e impresos compatibles.

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

 Form Recognizer v3.0 (versión preliminar) presenta varias características y funcionalidades nuevas:

* La **API del modelo personalizado (v3.0)** admite la detección de firmas de formularios personalizados. Al entrenar modelos personalizados, puede especificar determinados campos como firmas.  Cuando se analiza un documento con el modelo personalizado, indicará si se ha detectado o no una firma.

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

### <a name="try-signature-detection"></a>Prueba de la detección de firmas

1. [**Creación del conjunto de datos de aprendizaje**](build-training-data-set.md#custom-model-input-requirements).

1. Vaya a la [**herramienta de etiquetado de ejemplo de Form Recognizer**](https://fott-preview-private.azurewebsites.net) y seleccione **Use Custom to train a model with labels and get key value pairs** (Use Personalizado para entrenar un modelo con etiquetas y obtener pares clave-valor):

    :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Captura de pantalla: selección de FOTTtools de la opción personalizada.":::

1. En la ventana siguiente, seleccione **Nuevo proyecto**:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Captura de pantalla: selección de FOTTtools selecciona del nuevo proyecto.":::

1. Siga los [**Requisitos de entrada del modelo personalizado**](build-training-data-set.md#custom-model-input-requirements).

1. Cree una etiqueta con el tipo **Firma**.

1. **Etiquete los documentos**.  Para los campos de firma, se recomienda usar el etiquetado de región para mejorar la precisión.

1. Una vez etiquetado el conjunto de entrenamiento, puede **entrenar el modelo personalizado** y usarlo para analizar documentos. Los campos de firma especificarán si se detectó o no una firma.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API de REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
