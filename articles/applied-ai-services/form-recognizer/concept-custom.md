---
title: 'Modelos personalizados y compuestos: Form Recognizer'
titleSuffix: Azure Applied AI Services
description: Aprenda a crear, usar y administrar modelos personalizados y compuestos de Form Recognizer, su uso y límites.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: de050c6c8f2b0b8b9e108f5bcc677dadf62aad2f
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387475"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Modelos personalizados y compuestos de Form Recognizer

Form Recognizer emplea tecnología avanzada de aprendizaje automático para detectar y extraer información de imágenes de documento y devolver los datos extraídos en una salida JSON estructurada. Con Form Recognizer, puede entrenar modelos personalizados independientes, crear modelos compuestos o empezar a trabajar con nuestros modelos precompilados:

* **Modelos personalizados.** Los modelos personalizados de Form Recognizer permiten analizar y extraer datos de formularios y documentos específicos de su negocio. Los modelos personalizados se entrenan para sus distintos datos y casos de uso.

* **Modelos compuestos**. El modelo compuesto se crea tomando una colección de modelos personalizados y asignándolos a un único modelo que abarca sus tipos de formulario. Cuando se envía un documento a un modelo compuesto, el servicio realiza un paso de clasificación para decidir qué modelo personalizado representa con exactitud el formulario presentado para el análisis.

* **Modelos precompilados**. Form Recognizer admite actualmente modelos precompilados para [tarjetas de presentación](concept-business-cards.md), [diseño](concept-layout.md), [documentos de identidad](concept-identification-cards.md), [facturas](concept-invoices.md) y [recibos](concept-receipts.md).

En este artículo, examinaremos el proceso de creación de modelos personalizados y compuestos de Form Recognizer mediante nuestra [herramienta de etiquetado de ejemplo de Form Recognizer](label-tool.md), las [API REST](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) o los [SDK de la biblioteca cliente](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model).

## <a name="what-is-a-custom-model"></a>¿Qué es un dominio personalizado?

Un modelo personalizado es un programa de aprendizaje automático entrenado para reconocer campos de formulario dentro de su contenido distinto y extraer pares clave-valor y datos de tabla. Solo necesita cinco ejemplos del mismo tipo de formulario para empezar y el modelo personalizado se puede entrenar con o sin conjuntos de datos etiquetados.

## <a name="what-is-a-composed-model"></a>¿Qué es un modelo compuesto?

Con los modelos compuestos, puede asignar varios modelos personalizados a un modelo compuesto llamado con un único identificador de modelo. Esto resulta útil cuando se han entrenado varios modelos y se quieren agrupar para analizar tipos de formulario parecidos. Por ejemplo, el modelo compuesto puede estar formado por modelos personalizados entrenados para analizar sus pedidos de compra de suministros, equipos y mobiliario. En lugar de intentar seleccionar manualmente el modelo adecuado, puede usar un modelo compuesto para determinar el modelo personalizado adecuado para cada análisis y extracción.

## <a name="try-it"></a>Pruébelo

Comience a trabajar con nuestra herramienta de etiquetado de ejemplo de Form Recognizer:

> [!div class="nextstepaction"]
> [Probar un modelo personalizado](https://aka.ms/fott-2.1-ga "Comience con un modelo personalizado para entrenar un modelo con etiquetas y encontrar los pares clave-valor.")

## <a name="create-your-models"></a>Creación de los modelos

Estos son los pasos para compilar, entrenar y usar modelos personalizados y compuestos:

* [**Ensamblado del conjunto de datos de entrenamiento**](#assemble-your-training-dataset)
* [**Carga del conjunto de entrenamiento en Azure Blob Storage**](#upload-your-training-dataset)
* [**Entrenamiento del modelo personalizado**](#train-your-custom-model)
* [**Composición de modelos personalizados**](#create-a-composed-model)
* [**Análisis de documentos**](#analyze-documents-with-your-custom-or-composed-model)
* [**Administrar modelos personalizados**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>Ensamblado del conjunto de datos de entrenamiento

La construcción de un modelo personalizado comienza con el establecimiento de su conjunto de datos de entrenamiento. Necesitará un mínimo de cinco formularios completados del mismo tipo para su conjunto de datos de ejemplo. Pueden ser de distintos tipos de archivo (jpg, png, pdf, tiff) y contener texto y escritura a mano. Los formularios deben seguir los [requisitos de entrada](build-training-data-set.md#custom-model-input-requirements) para Form Recognizer.

## <a name="upload-your-training-dataset"></a>Carga del conjunto de datos de entrenamiento

Tendrá que [cargar los datos de entrenamiento](build-training-data-set.md#upload-your-training-data) en un contenedor de Azure Blob Storage. Si no sabe cómo crear una cuenta de almacenamiento de Azure con un contenedor, *vea el* [inicio rápido de Azure Storage para Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Puede usar el plan de tarifa gratis (F0) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

## <a name="train-your-custom-model"></a>Entrenamiento del modelo personalizado

Puede [entrenar el modelo](quickstarts/client-library.md#train-a-custom-model) con o sin conjuntos de datos con etiqueta. Los conjuntos de datos no etiquetados se basan únicamente en la [API de diseño](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) para detectar e identificar la información clave sin necesidad de intervención humana. Los conjuntos de datos etiquetados también se basan en la API de diseño, pero se incluye información humana complementaria, como sus etiquetas específicas y las ubicaciones de los campos. Para utilizar datos etiquetados y no etiquetados, comience con al menos cinco formularios completados del mismo tipo para los datos de entrenamiento etiquetados y luego añada datos no etiquetados al conjunto de datos requerido.

## <a name="create-a-composed-model"></a>Creación de un modelo compuesto

> [!NOTE]
> **El modelo compuesto solo está disponible para los modelos personalizados entrenados _con_ etiquetas.** Al intentar crear modelos compuestos sin etiquetar se producirá un error.

Con la operación de modelo compuesto, puede crear hasta 100 modelos personalizados entrenados con un único identificador de modelo. Cuando llame a la función de análisis con el identificador de modelo compuesto, Form Recognizer clasificará primero el formulario que envió, elegirá el mejor modelo coincidente y después devolverá los resultados de ese modelo. Esta operación resulta útil cuando los formularios de entrada pueden pertenecer a una de varias plantillas.

Con la herramienta de etiquetado de ejemplo de Form Recognizer, la API REST o los SDK de biblioteca cliente, siga los pasos que se indican a continuación para configurar un modelo compuesto:

1. [**Recopilación de los identificadores de modelo personalizado**](#gather-your-custom-model-ids)
1. [**Composición de los modelos personalizados**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>Recopilación de los identificadores de modelo personalizado

Una vez que el proceso de entrenamiento se haya completado correctamente, se le asignará un identificador de modelo al modelo personalizado. Puede recuperar un identificador de modelo de la manera siguiente:

### <a name="form-recognizer-sample-labeling-tool"></a>[**Herramienta de etiquetado de ejemplo de Form Recognizer**](#tab/fott)

Al entrenar modelos mediante la [**herramienta de etiquetado de ejemplo de Form Recognizer**](https://fott-2-1.azurewebsites.net/), el identificador de modelo se encuentra en la ventana Train Result (Resultado de entrenamiento):

:::image type="content" source="media/fott-training-results.png" alt-text="Captura de pantalla: ventana de resultados del entrenamiento.":::

### <a name="rest-api"></a>[**API DE REST**](#tab/rest-api)

La [**API REST**](quickstarts/client-library.md?pivots=programming-language-rest-api#train-a-custom-model), devolverá una respuesta `201 (Success)` con un encabezado **Location**. El valor del último parámetro de este encabezado es el identificador del modelo recién entrenado:

:::image type="content" source="media/model-id.png" alt-text="Captura de pantalla: encabezado de ubicación devuelto que contiene el identificador del modelo.":::

### <a name="client-library-sdks"></a>[**SDK de biblioteca cliente**](#tab/sdks)

 Los [**SDK de biblioteca cliente**](quickstarts/client-library.md?pivots=programming-language-csharp#train-a-custom-model) devuelven un objeto de modelo que se puede consultar para devolver el identificador de modelo entrenado:

* C\#  | [Clase CustomFormModel](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "SDK de Azure para .NET")

* Java | [Clase CustomFormModelInfo](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "SDK de Azure para Java")

* JavaScript | [Interfaz CustomFormModelInfo](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "SDK de Azure para JavaScript")

* Python | [Clase CustomFormModelInfo](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "SDK de Azure para Python")

---

#### <a name="compose-your-custom-models"></a>Composición de los modelos personalizados

Después de haber recopilado los modelos personalizados correspondientes a un único tipo de formulario, puede componerlos en un solo modelo.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Herramienta de etiquetado de ejemplo de Form Recognizer**](#tab/fott)

La **herramienta de etiquetado de ejemplo** le permite empezar a entrenar modelos rápidamente y a componerlos con un único identificador de modelo.

Una vez completado el entrenamiento, componga los modelos de la siguiente manera:

1. En el menú del raíl izquierdo, seleccione el **icono Model Compose** (Componer modelo) (flecha de combinación).

1. En la ventana principal, seleccione los modelos a los que quiere asignar un único identificador de modelo. Los modelos con el icono de flechas ya son modelos compuestos.

1. Elija el botón **Compose** (Componer) en la esquina superior izquierda.

1. En la ventana emergente, asígnele un nombre al nuevo modelo compuesto y seleccione **Compose** (Componer).

Una vez finalizada la operación, el modelo recién compuesto aparecerá en la lista.

  :::image type="content" source="media/custom-model-compose.png" alt-text="Captura de pantalla: ventana de composición de modelos." lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**API DE REST**](#tab/rest-api)

Con la **API REST**, puede realizar una solicitud [**Compose Custom Model**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose) (Componer modelo personalizado) para crear un único modelo compuestos a partir de modelos existentes. El cuerpo de la solicitud requiere una matriz de cadenas de `modelIds` para realizar la composición y, opcionalmente, puede definir `modelName`.  *Consulte* [Modelos compuestos asincrónicos](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async).

### <a name="client-library-sdks"></a>[**SDK de biblioteca cliente**](#tab/sdks)

Use el código del lenguaje de programación que prefiera para crear un modelo compuesto al que se llamará con un único identificador de modelo. Estos son vínculos a ejemplos de código que muestran cómo crear un modelo compuesto a partir de modelos personalizados existentes:

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md).

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/CreateComposedModel.java).

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js).

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.2-beta/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>Análisis de documentos con el modelo personalizado o compuesto

 La operación **Analizar** del formulario personalizado requiere que proporcione el parámetro `modelID` en la llamada a Form Recognizer. Puede proporcionar un único identificador de modelo personalizado o un identificador de modelo compuesto para el parámetro `modelID`.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Herramienta de etiquetado de ejemplo de Form Recognizer**](#tab/fott)

1. En el menú del panel izquierdo de la herramienta, seleccione el **icono Analizar** (bombilla).

1. Elija un archivo local o una dirección URL de imagen para analizar.

1. Seleccione el botón **Run Analysis** (Ejecutar análisis).

1. La herramienta aplicará etiquetas en los cuadros de límite e informará del porcentaje de confianza de cada etiqueta.

:::image type="content" source="media/analyze.png" alt-text="Captura de pantalla: ventana de análisis de un formulario personalizado de la herramienta Form Recognizer.":::

### <a name="rest-api"></a>[**API DE REST**](#tab/rest-api)

Con la API REST, puede realizar una solicitud [Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) (Analizar formulario) para analizar un documento y extraer los pares clave-valor y los datos de la tabla.

### <a name="client-library-sdks"></a>[**SDK de biblioteca cliente**](#tab/sdks)

Use el lenguaje de programación que prefiera para analizar un formulario o documento con un modelo personalizado o compuesto. Necesitará el punto de conexión de Form Recognizer, la clave de API y el identificador de modelo.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md#recognize-a-custom-form-using-a-composed-model)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/RecognizeCustomFormsFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.1/sample_recognize_custom_forms.py)

---

Para probar los modelos recién entrenados, [analice los formularios](quickstarts/client-library.md#analyze-forms-with-a-custom-model) que no formaron parte del conjunto de datos de entrenamiento. Según la precisión notificada, puede que desee realizar un entrenamiento adicional para mejorar el modelo. Puede continuar con entrenamiento adicional para [mejorar los resultados](label-tool.md#improve-results).

## <a name="manage-your-custom-models"></a>Administración de modelos personalizados

Puede [administrar los modelos personalizados](quickstarts/client-library.md#manage-custom-models) a lo largo de su ciclo de vida. Así, puede ver una [lista de todos los modelos personalizados](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels) de su suscripción, recuperar información sobre [un modelo personalizado concreto](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel), y [eliminar modelos personalizados](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel) de su cuenta.

Estupendo. Ha aprendido los pasos para crear modelos personalizados y compuestos y usarlos en proyectos y aplicaciones de Form Recognizer.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la biblioteca cliente de Form Recognizer, consulte nuestra documentación de referencia de la API.

> [!div class="nextstepaction"]
> [Referencia de la API Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
