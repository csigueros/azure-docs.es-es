---
title: 'Instrucciones: modelos personalizados y compuestos'
titleSuffix: Azure Applied AI Services
description: Aprenda a crear, usar y administrar modelos personalizados y compuestos de Form Recognizer
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 55e3d529b0f0e713be1c19e9f2ae4736d9b6a9ca
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177524"
---
# <a name="how-to-use-custom-and-composed-models"></a>Uso de modelos personalizados y compuestos

Form Recognizer emplea tecnología avanzada de aprendizaje automático para detectar y extraer información de imágenes de documento y devolver los datos extraídos en una salida JSON estructurada. Con Form Recognizer, puede entrenar modelos personalizados independientes o combinar modelos personalizados para crear modelos compuestos.

* **Modelos personalizados.** Los modelos personalizados de Form Recognizer permiten analizar y extraer datos de formularios y documentos específicos de su negocio. Los modelos personalizados se entrenan para sus distintos datos y casos de uso.

* **Modelos compuestos**. El modelo compuesto se crea tomando una colección de modelos personalizados y asignándolos a un único modelo que abarca sus tipos de formulario. Cuando se envía un documento a un modelo compuesto, el servicio realiza un paso de clasificación para decidir qué modelo personalizado representa con exactitud el formulario presentado para el análisis.

En este artículo, examinaremos el proceso de creación de modelos personalizados y compuestos de Form Recognizer mediante nuestra [herramienta de etiquetado de ejemplo de Form Recognizer](label-tool.md), las [API REST](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) o los [SDK de la biblioteca cliente](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model).

## <a name="try-it-sample-labeling-tool"></a>Pruébelo: herramienta de etiquetado de ejemplo

Para ver cómo se extraen datos de formularios personalizados, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

> [!div class="nextstepaction"]
> [Probarlo](https://fott-2-1.azurewebsites.net/projects/create)

En la interfaz de usuario de Form Recognizer:

1. Seleccione **Use Personalizado para entrenar un modelo con etiquetas y obtener pares clave-valor**.
  
      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Captura de pantalla: selección de la herramienta fott de la opción personalizada.":::

1. En la ventana siguiente, seleccione **Nuevo proyecto**:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Captura de pantalla: selección de la herramienta fott del nuevo proyecto."::: 

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

### <a name="train-without-labels"></a>Entrenamiento sin etiquetas

Form Recognizer usa el aprendizaje sin supervisión para comprender el diseño y las relaciones entre los campos y las entradas de los formularios. Cuando se envían los formularios de entrada, el algoritmo agrupa los formularios por tipos, descubre qué claves y tablas están presentes y asocia los valores a las claves y entradas a las tablas. El entrenamiento sin etiquetas no requiere el etiquetado de datos manual, codificación intensiva ni mantenimiento; se recomienda probar este método primero.

Consulte [Creación de un conjunto de datos de entrenamiento](./build-training-data-set.md) para ver sugerencias sobre cómo recopilar los documentos de entrenamiento.

### <a name="train-with-labels"></a>Entrenamiento con etiquetas

Al entrenar con datos etiquetados, el modelo realiza un aprendizaje supervisado para extraer los valores de interés mediante los formularios etiquetados que se proporcionan. Los datos etiquetados derivan en modelos con mejor rendimiento y pueden generar modelos que funcionen con formularios complejos o formularios que contengan valores sin claves.

Form Recognizer usa la API [Diseño](concept-layout.md) para aprender los tamaños y las posiciones esperados de los elementos de texto impresos y manuscritos, y para extraer tablas. A continuación, usa etiquetas especificadas por el usuario para aprender las asociaciones clave-valor y las tablas de los documentos. Se recomienda usar cinco formularios etiquetados manualmente del mismo tipo (la misma estructura) para empezar a entrenar un nuevo modelo y agregar más datos etiquetados según sea necesario para mejorar la precisión del modelo. Form Recognizer permite entrenar un modelo para extraer pares de valores clave y tablas mediante funcionalidades de aprendizaje supervisado.

[Introducción al entrenamiento con etiquetas](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

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

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md).

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/administration/CreateComposedModel.java).

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

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/AnalyzeCustomDocumentFromUrl.java)

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

