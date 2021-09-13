---
title: Novedades en Form Recognizer
titleSuffix: Azure Applied AI Services
description: Comprenda los últimos cambios en la API de Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: 2efc4d2c546cff172164b9416cbe5a0772167631
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326587"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-azure-form-recognizer"></a>Novedades en Azure Form Recognizer

El servicio Form Recognizer se actualiza de forma continuada. Marque esta página para mantenerse al día con las notas de la versión, las mejoras de características y las actualizaciones de documentación.

## <a name="july-2021"></a>Julio de 2021

### <a name="system-assigned-managed-identity-support"></a>Compatibilidad con la identidad administrada asignada por el sistema 

 Ahora puede habilitar una identidad administrada asignada por el sistema para conceder a Form Recognizer acceso limitado a cuentas de almacenamiento privadas, incluidas las protegidas por una red virtual (VNet) o un firewall, o bien tener habilitada la funcionalidad Bring Your Own Storage (BYOS). *Consulte* [Creación y uso de identidades administradas para el recurso de Form Recognizer](managed-identity-byos.md) para más información.

## <a name="june-2021"></a>Junio de 2021

### <a name="form-recognizer-containers-v21-released-in-gated-preview"></a>Publicación de contenedores de Form Recognizer v2.1 en versión preliminar validada

Las características de Form Recognizer son ahora compatibles con seis contenedores de características: **Diseño**, **Tarjeta de presentación**, **Documento de identificación**, **Recibo**, **Factura** y **Personalizado**. Para usarlos, debe enviar una [solicitud en línea](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) y recibir la aprobación.

*Consulte* [**Instalación y ejecución de contenedores de Docker para Form Recognizer**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout) y [**Configuración de contenedores de Form Recognizer**](containers/form-recognizer-container-configuration.md?branch=main).

### <a name="form-recognizer-connector-released-in-preview"></a>Publicación del conector de Form Recognizer en versión preliminar

  El [**conector de Form Recognizer**](/connectors/formrecognizer) se integra con [Azure Logic Apps](../../logic-apps/logic-apps-overview.md), [Microsoft Power Automate](/power-automate/getting-started) y [Microsoft Power Apps](/powerapps/powerapps-overview). El conector admite acciones y desencadenadores de flujo de trabajo para extraer y analizar los datos y la estructura de los documentos de formularios, facturas, recibos, tarjetas de presentación y documentos de identificación personalizados y creados previamente.

### <a name="form-recognizer-sdk-v310-patched-to-v311-for-c-java-and-python"></a>Revisión del SDK de Form Recognizer v3.1.0 a v3.1.1 para C#, Java y Python

La revisión aborda las facturas sin campos de elementos de sublínea detectados, como un elemento `FormField` con `Text`, pero sin información de `BoundingBox` o `Page`.

### <a name="c"></a>[**C#**](#tab/csharp)

| [Documentación de referencia](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [Paquete NuGet, versión 3.1.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

### <a name="java"></a>[**Java**](#tab/java)

 | [Documentación de referencia](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Versión de dependencia de paquetes de artefactos de Maven, versión 3.1.1](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer/3.1.1) |

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

> [!NOTE]
> No hay actualizaciones del SDK de JavaScript v3.1.0.

| [Documentación de referencia](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm package dependency form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer) |

### <a name="python"></a>[**Python**](#tab/python)

| [Documentación de referencia](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.1](https://pypi.org/project/azure-ai-formrecognizer/) |

---

## <a name="may-2021"></a>Mayo de 2021

### <a name="form-recognizer-21-api-generally-available-ga-release"></a>API de Form Recognizer 2.1, versión de disponibilidad general (GA)

* Form Recognizer 2.1 está disponible con carácter general. Esta versión de disponibilidad general (GA) marca la estabilidad de los cambios introducidos en versiones anteriores del paquete de versión preliminar 2.1. Esta versión le permite detectar y extraer información y datos de lo siguiente:

* [Documentos](concept-layout.md)
* [Receipts](concept-receipts.md)
* [Tarjetas de presentación](concept-business-cards.md)
* [Facturas](concept-invoices.md)
* [Documentos de identidad](concept-identification-cards.md)
* [Formularios personalizados](concept-custom.md)

#### <a name="get-started"></a>Primeros pasos 

Vaya a la [herramienta de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/) y siga el [inicio rápido](quickstarts/get-started-with-form-recognizer.md). 

### <a name="layout-adds-table-headers"></a>Adición de encabezados de tabla por diseño

La característica de tabla de la API de diseño actualizada agrega el reconocimiento de encabezados con encabezados de columna que pueden abarcar varias filas. Cada celda de tabla tiene un atributo que indica si forma parte de un encabezado o no. Esto se puede usar para identificar qué filas son el encabezado de tabla.

#### <a name="sdk-updates"></a>Actualizaciones del SDK

### <a name="c"></a>[**C#**](#tab/csharp)

| [Documentación de referencia](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [Paquete NuGet, versión 3.0.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

#### <a name="non-breaking-changes"></a>**Cambios secundarios**

* La clase **FormRecognizerModelFactory** admite ahora actualizaciones de **TextAppearance** y **ReadingOrder** y la eliminación de modelos **TextStyle**. Consulte [Cambios importantes](#breaking-changes-may).

#### <a name="breaking-changes-may"></a>**Cambios importantes (mayo)**

* El cliente tiene como valor predeterminado la versión más reciente del servicio compatible, actualmente la v2.1. Puede especificar la versión 2.0 en la propiedad **Version** del objeto **FormRecognizerClientOptions**.

* **StartRecognizeIdentityDocuments**. Se ha cambiado el nombre de los métodos y los parámetros de método que usaban **Identity** para reemplazar la palabra clave _Id_ en el caso de todas las funcionalidades de API de reconocimiento de documentos de identidad relacionadas.

* **FormReadingOrder**. *ReadingOrder* ha pasado a denominarse **FormReadingOrder**.

* **AsCountryRegion**. *AsCountryCode* ha pasado a denominarse **AsCountryRegion**.

* **TextAppearance** incluye ahora las propiedades **StyleName** y **StyleConfidence** (anteriormente parte del objeto **TextStyle**).

* **FieldValueType**.  Se ha eliminado el valor **Gender** del modelo.

* Se ha eliminado el modelo **TextStyle**.

* Se ha eliminado el tipo **FieldValueGender**.

### <a name="java"></a>[**Java**](#tab/java)

  | [Documentación de referencia](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Dependencia del paquete de artefactos de Maven, versión 3.1.0](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) |

#### <a name="non-breaking-changes"></a>**Cambios secundarios**

* **FormRecognizerClientBuilder** y **FormTrainingClientBuilder**. Se han agregado los métodos **clientOptions** y **getDefaultLogOptions**.

* **FormRecognizerLanguage**.  Se han agregado más campos de idioma.

#### <a name="breaking-changes-may"></a>**Cambios importantes (mayo)**

* El cliente tiene como valor predeterminado la versión más reciente del servicio compatible, actualmente la v2.1. Puede especificar la versión 2.0 del método **serviceVersion** del objeto **FormRecognizerClientBuilder**.

* Se ha quitado la compatibilidad con v2.1-preview.1 y v2.1-preview.2.

* **beginRecognizeIdentityDocuments**.  Se ha cambiado el nombre de los métodos y los parámetros de método que usaban **Identity** para reemplazar la palabra clave _Id_ en el caso de todas las funcionalidades de API de reconocimiento de documentos de identidad relacionadas.

* **FormReadingOrder**. *ReadingOrder* ha pasado a denominarse **FormReadingOrder** y refactoriza la clase para que sea una clase de cadena expandible.

* **asCountryRegion**. El método *AsCountryCode* ha pasado a denominarse **AsCountryRegion**.

* **FieldValueType**. El valor de campo *COUNTRY* ha pasado a denominarse **COUNTRY_REGION**.

* La clase **TextAppearance** incluye ahora las propiedades **styleName** y **styleConfidence** (anteriormente parte del objeto **TextStyle**).

* **FieldValueType**. Se ha eliminado el valor *Gender* del modelo.

* Se ha eliminado el modelo **TextStyle**.

* Se ha eliminado el tipo de clase **FieldValueGender**.

* **pollInterval**. Se han eliminado los métodos pollInterval de las clases **RecognizeBusinessCardsOptions**, **RecognizeContentOptions**, **RecognizeCustomFormsOptions**, **RecognizeIdentityDocumentOptions**, **RecognizeInvoicesOptions** y **RecognizeReceiptsOptions**. El intervalo de sondeo se puede actualizar mediante los métodos [**SyncPoller setPollInterval**](/java/api/com.azure.core.util.polling.syncpoller.setpollinterval?view=azure-java-stable&preserve-view=true) o [**PollerFlux setPollInterval**](/java/api/com.azure.core.util.polling.pollerflux.setpollinterval?view=azure-java-stable&preserve-view=true) de Azure Core de forma sincrónica o asincrónica, respectivamente.

* **FormLine**, **FormPage**, **FormTable**, **FormSelectionMark**, **TextAppearance**, **CustomFormModel**, **CustomFormModelInfo**, **CustomFormModelProperties**, **CustomFormSubmodel** y **TrainingDocumentInfo** ahora son clases de modelo inmutables.

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

| [Documentación de referencia](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm package dependency form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |

#### <a name="non-breaking-changes"></a>**Cambios secundarios**

* Todas las llamadas API REST se migran al punto de conexión v2.1.

* Se ha agregado la enumeración **KnownFormLocale** para tener acceso a los valores posibles de las configuraciones regionales de formulario.

* **beginRecognizeIdDocuments...** . Se ha cambiado el nombre de los métodos y los parámetros de método que usaban **Identity** para reemplazar la palabra clave _Id_ en el caso de todas las funcionalidades de API de reconocimiento de documentos de identidad relacionadas.

* **FormReadingOrder** y **FormLanguage**. *ReadingOrder* ha pasado a denominarse *FormReadingOrder*. *Language* ha pasado a denominarse **FormLanguage**.

* **FormCountryRegionField** y **countryRegion**. El tipo *FormCountryField* ha pasado a denominarse **FormCountryRegionField**, mientras que el tipo de valor *country* ha pasado a denominarse **countryRegion**.

* La interfaz **TextAppearance** ahora incluye las propiedades **styleName** y **styleConfidence** (anteriormente propiedades "name" y "confidence" en la interfaz **TextStyle**).

* Se han eliminado las enumeraciones **KnownStyleName**, **KnownSelectionMarkState** y **KnownKeyValueType**.

* Se ha eliminado el tipo **FormGenderField**. Cualquier valor reconocido que se produjera anteriormente como _FormGenderField_ ahora se devolverá como un tipo FormStringField. El valor seguirá siendo el mismo.

* Se ha eliminado el tipo **TextStyle**.

#### <a name="breaking-changes-may"></a>**Cambios importantes (mayo)**

**No hay cambios importantes.**

### <a name="python"></a>[**Python**](#tab/python)

| [Documentación de referencia](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.0](https://pypi.org/project/azure-ai-formrecognizer/) |

#### <a name="non-breaking-changes"></a>**Cambios secundarios**

* Se han agregado los métodos **to_dict** y **from_dict** a todos los modelos.

#### <a name="breaking-changes-may"></a>**Cambios importantes (mayo)**

* **begin_recognize_identity_documents** y **begin_recognize_identity_documents_from_url**. Se ha cambiado el nombre de los métodos y los parámetros de método que usaban **Identity** para reemplazar la palabra clave _Id_.

* **FieldValueType**. El tipo de valor *country* ha pasado a denominarse **countryRegion**.  Se ha eliminado el tipo de valor *gender*.

* El modelo **TextAppearance** ahora incluye las propiedades **style_name** y **style_confidence** (anteriormente propiedades "name" y "confidence" en el objeto **TextStyle**).

* Se ha eliminado el modelo **TextStyle**.

---

## <a name="april-2021"></a>Abril de 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-preview-updates-for-api--version-21-preview3"></a>Actualizaciones de la versión preliminar del SDK para la API versión 2.1-preview.3

### <a name="c"></a>[**C#**](#tab/csharp)

Paquete NuGet, versión 3.1.0-beta.4

* **Nuevos métodos para analizar datos de documentos de identidad**:

   **StartRecognizeIdDocumentsFromUriAsync**

   **StartRecognizeIdDocumentsAsync**

   Para obtener una lista de los valores de campos, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) en la documentación de Form Recognizer.

* Se ha ampliado el conjunto de idiomas de documento que se pueden proporcionar al método **[StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** .

* **Nueva propiedad `Pages` compatible con las clases siguientes**:

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   La propiedad `Pages` le permite seleccionar páginas individuales o un intervalo de ellas para los documentos PDF y TIFF de varias páginas. Si quiere elegir páginas individuales, escriba el número de página: por ejemplo, `3`. Si quiere elegir un intervalo de páginas (como las páginas 2 y 5-7), escriba los números de página y los intervalos separados por comas: `2, 5-7`.

* **Nueva propiedad `ReadingOrder` compatible con las clases siguientes**:

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  La propiedad `ReadingOrder` es un parámetro opcional que permite especificar cuál algoritmo de orden de lectura, `basic` o `natural`, se debe aplicar para ordenar la extracción de los elementos de texto. Si no se especifica, el valor predeterminado es `basic`.

#### <a name="breaking-changes-april"></a>Cambios importantes (abril)

* El cliente tiene como valor predeterminado la versión del servicio más reciente que sea compatible. Actualmente, es **2.1-preview.3**.

* **El método [StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** ahora genera una excepción `RequestFailedException()` cuando se pasa un archivo no válido.

### <a name="java"></a>[**Java**](#tab/java)

Dependencia del paquete de artefacto de Maven, versión 3.1.0-beta.3

* **Nuevos métodos para analizar datos de documentos de identidad**:

  **[beginRecognizeIdDocumentsFromUrl]**

  **[beginRecognizeIdDocuments]**

   Para obtener una lista de los valores de campos, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) en la documentación de Form Recognizer.

* **Compatibilidad con los archivos de imagen de mapa de bits (.bmp) para los formularios personalizados y los métodos de entrenamiento en la enumeración `FormContentType`** :

* `image/bmp`

* **Nueva propiedad `Pages` compatible con las clases siguientes**:

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  La propiedad `Pages` le permite seleccionar páginas individuales o un intervalo de ellas para los documentos PDF y TIFF de varias páginas. Si quiere elegir páginas individuales, escriba el número de página: por ejemplo, `3`. Si quiere elegir un intervalo de páginas (como las páginas 2 y 5-7), escriba los números de página y los intervalos separados por comas: `2, 5-7`.

* **Compatibilidad con los archivos de imagen de mapa de bits (.bmp) para los formularios personalizados y los métodos de entrenamiento en los campos [FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)** :

  `image/bmp`

* **Nuevo argumento de palabra clave `ReadingOrder` compatible con los métodos siguientes**:

* **[beginRecognizeContent](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?preserve-view=true&view=azure-java-preview)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   El argumento de palabra clave `ReadingOrder` es un parámetro opcional que permite especificar cuál algoritmo de orden de lectura, `basic` o `natural`, se debe aplicar para ordenar la extracción de los elementos de texto. Si no se especifica, el valor predeterminado es `basic`.

* El cliente tiene como valor predeterminado la versión del servicio más reciente que sea compatible. Actualmente, esta versión es **2.1-preview.3**.

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

Paquete npm, versión 3.1.0-beta.3

* **Nuevos métodos para analizar datos de documentos de identidad**:

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Para obtener una lista de los valores de campos, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) en la documentación de Form Recognizer.

* **Se han agregado nuevos valores de campo a la interfaz FieldValue**:

    `gender`: los valores posibles son `M`, `F` o `X`.</br>
   `country`: los valores posibles siguen la cadena de código de país [ISO alfa-3](https://www.iso.org/obp/ui/#search) de tres letras.

* Nueva opción `pages` compatible con todos los métodos de reconocimiento de formularios (formularios personalizados y todos los modelos precompilados). El argumento le permite seleccionar páginas individuales o un intervalo de ellas para los documentos PDF y TIFF de varias páginas. Si quiere elegir páginas individuales, escriba el número de página: por ejemplo, `3`. Si quiere elegir un intervalo de páginas (como las páginas 2 y 5-7), escriba los números de página y los intervalos separados por comas: `2, 5-7`.

* Se ha agregado compatibilidad con un tipo **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/formreadingorder?view=azure-node-latest&preserve-view=true to the URL)** a los métodos de reconocimiento de contenido. Esta opción le permite controlar el algoritmo que usa el servicio para determinar cómo se deben ordenar las líneas de texto reconocidas. Puede especificar qué algoritmo de orden de lectura, `basic` o `natural`, se debe aplicar para ordenar la extracción de elementos de texto. Si no se especifica, el valor predeterminado es `basic`.

* Se ha dividido el tipo **[FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** en varias interfaces diferentes. Esta actualización no debería provocar ningún problema de compatibilidad con la API, excepto en determinados casos extremos (valueType sin definir).

* Se ha migrado al punto de conexión de servicio de Form Recognizer versión **2.1-preview.3** para todas las llamadas a la API REST.

### <a name="python"></a>[**Python**](#tab/python)

Paquete pip, versión 3.1.0b4

* **Nuevos métodos para analizar datos de documentos de identidad**:

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Para obtener una lista de los valores de campos, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) en la documentación de Form Recognizer.

* **Se han agregado nuevos valores de campo a la enumeración [FieldValue](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)** :

   gender: los valores posibles son `M` `F` o `X`.

  country: los valores posibles siguen los [códigos de país ISO alfa-3](https://www.iso.org/obp/ui/#search).

* **Compatibilidad con los archivos de imagen de mapa de bits (.bmp) para los formularios personalizados y los métodos de entrenamiento en la enumeración [FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true)** :

    image/bmp

* **Nuevo argumento de palabra clave `pages` compatible con los métodos siguientes**:

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   El argumento de palabra clave `pages` le permite seleccionar páginas individuales o un intervalo de ellas para los documentos PDF y TIFF de varias páginas. Si quiere elegir páginas individuales, escriba el número de página: por ejemplo, `3`. Si quiere elegir un intervalo de páginas (como las páginas 2 y 5-7), escriba los números de página y los intervalos separados por comas: `2, 5-7`.

* **Nuevo argumento de palabra clave `readingOrder` compatible con los métodos siguientes**:

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   El argumento de palabra clave `readingOrder` es un parámetro opcional que permite especificar cuál algoritmo de orden de lectura, `basic` o `natural`, se debe aplicar para ordenar la extracción de los elementos de texto. Si no se especifica, el valor predeterminado es `basic`.

---

## <a name="march-2021"></a>Marzo de 2021

**Ya está disponible la versión preliminar pública 3 de Form Recognizer v2.1.** Se ha publicado la versión v2.1-preview.3, que incluye las siguientes características:

* **Nuevo modelo de identificación precompilado** El nuevo modelo de identificación precompilado permite a los clientes tomar identificaciones y devolver datos estructurados para automatizar el procesamiento. Combina nuestras potentes capacidades de reconocimiento óptico de caracteres (OCR) con modelos de reconocimiento de identificación para extraer información clave de los pasaportes y los carnés de conducir de EE. UU., como el nombre, la fecha de nacimiento, la fecha de la fecha de caducidad, etc.

  [Más información sobre el modelo de identificación precompilado](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="ejemplo de pasaporte" lightbox="./media/id-canada-passport-example.png":::

* **Extracción de elemento de línea para modelo de factura precompilado**: el modelo de factura precompilado ahora admite la extracción de elementos de línea; ahora se extraen los elementos completos y sus partes: descripción, importe, cantidad, id. de producto, fecha y mucho más. Con una sencilla llamada de API/SDK, puede extraer datos útiles de las facturas (texto, tabla, pares clave-valor y elementos de línea).

   [Más información sobre el modelo de factura precompilado](concept-invoices.md)

* **Etiquetado y entrenamiento de tablas supervisados, etiquetado de valores vacíos**: además de [las funciones de aprendizaje profundo para la extracción de tablas automáticamente](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)de Form Recognizer, ahora permite que los clientes etiqueten y entrenen tablas. Esta nueva versión incluye la posibilidad de etiquetar y entrenar en elementos de línea y tablas (dinámicas y fijas) y entrenar un modelo personalizado para extraer pares clave-valor y elementos de línea. Una vez que se entrena un modelo, este extrae los elementos de línea como parte de la salida JSON en la sección documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Etiquetado de tabla" lightbox="./media/table-labeling.png":::

    Además de etiquetar tablas, ahora puede etiquetar regiones y valores vacíos. Si algunos documentos del conjunto de entrenamiento no tienen valores para determinados campos, puede etiquetarlos para que el modelo sepa que se van a extraer los valores correctamente de los documentos analizados.

* **Compatibilidad con 66 nuevos idiomas**: la API de diseño de Form Recognizer y los modelos personalizados ahora admiten 73 idiomas.

  [Más información sobre la compatibilidad con idiomas de Form Recognizer](language-support.md)

* **Orden de lectura natural, clasificación de escritura a mano y selección de página**: con esta actualización, puede optar por obtener los resultados de la línea de texto en el orden de lectura natural, en lugar de la ordenación predeterminada de izquierda a derecha y de arriba hacia abajo. Use el nuevo parámetro de consulta readingOrder y establézcalo en el valor "natural" para obtener una salida de orden de lectura más fácil de usar. Además, en el caso de los idiomas latinos, Form Recognizer clasificará las líneas de texto como estilo manuscrito o no y dará una puntuación de confianza.

* **Mejoras en la calidad del modelo de recepción precompilado**: esta actualización incluye varias mejoras de calidad para el modelo de recepción precompilado, especialmente en lo que respecta a la extracción de elementos de línea.

## <a name="november-2020"></a>Noviembre de 2020

### <a name="new-features"></a>Nuevas características

**Ya está disponible la versión preliminar pública 2 de Form Recognizer v2.1.** Se ha publicado la versión V2.1-preview.2, que incluye las siguientes características:

* **Nuevo modelo de factura precompilado**: el nuevo modelo de factura precompilado permite a los clientes tomar facturas en muchos formatos y devolver datos estructurados para automatizar el procesamiento de facturas. Combina nuestras eficaces funcionalidades de reconocimiento óptico de caracteres (OCR) con modelos de aprendizaje profundo de reconocimiento de facturas para extraer información clave de facturas en inglés. Extrae el texto clave, las tablas y la información como el cliente, el proveedor, el identificador de la factura, la fecha de vencimiento de la factura, el total, el importe debido, el importe de los impuestos, la dirección de envío y la dirección de facturación.

  > [Más información sobre el modelo de factura precompilado](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="Ejemplo de factura" lightbox="./media/invoice-example.jpg":::

* **Extracción de tablas mejorada**: Form Recognizer ahora proporciona una extracción de tablas mejorada, que combina nuestras eficaces funcionalidades de reconocimiento óptico de caracteres (OCR) con un modelo de extracción de tablas de aprendizaje profundo. Form Recognizer puede extraer datos de tablas, lo que incluye tablas complejas con columnas combinadas, filas, sin bordes, etc.

  :::image type="content" source="./media/tables-example.jpg" alt-text="Tablas de ejemplo" lightbox="./media/tables-example.jpg":::

  > [Más información sobre la extracción de Layout](concept-layout.md)

* **Actualización de la biblioteca cliente**: las versiones más recientes de las [bibliotecas cliente](quickstarts/client-library.md) para .NET, Python, Java y JavaScript admiten la API de Form Recognizer 2.1.
* **Nuevo idioma admitido (** japonés): ahora se admite el siguiente idiomas nuevos para `AnalyzeLayout` y `AnalyzeCustomForm`: japonés (`ja`). [Compatibilidad con idiomas](language-support.md)
* **Indicación de estilo de línea de texto (manuscrito/otros) (solo idiomas romance)** : Form Recognizer ahora genera un objeto `appearance` que clasifica si cada línea de texto tiene estilo manuscrito o no, junto con una puntuación de confianza. Esta característica solo es compatible con los idiomas derivados del latín.
* **Mejoras en la calidad**: mejoras en la extracción, lo que incluye mejoras en la extracción de dígitos individuales.
* **Nueva característica para realizar pruebas en las herramientas de etiquetado y de ejemplo de Form Recognizer**: mediante la herramienta de etiquetado de ejemplo de Form Recognizer se pueden probar los modelos de factura, recibo y tarjeta de presentación precompilados y la API Layout. Vea cómo se extraen los datos sin escribir código.

  [**Pruebe la herramienta de etiquetado de ejemplo de Form Recognizer.** ](https://fott-2-1.azurewebsites.net)

  ![Captura de pantalla: herramienta de etiquetado de ejemplo](./media/ui-preview.jpg)

* **Bucle de comentarios**: si se usa la herramienta de etiquetado de ejemplo para analizar archivos, ahora también se puede agregar al conjunto de entrenamiento, ajustar las tablas si fuera necesario y entrenar para mejorar el modelo.
* **Etiquetado automático de documentos**: etiqueta automáticamente documentos adicionales en función de documentos ya etiquetados existentes en el proyecto.

## <a name="august-2020"></a>Agosto de 2020

### <a name="new-features"></a>Nuevas características

**Ya está disponible la versión preliminar pública de Form Recognizer v2.1.** Se ha publicado V2.1-preview.1, incluidas las siguientes características:

* **Está disponible la referencia de la API de REST**: vea la [referencia de v2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
* **Se admiten nuevos idiomas además del inglés**: ahora se admiten los siguientes [idiomas](language-support.md) para `Layout` y `Train Custom Model`: inglés (`en`), chino (simplificado) (`zh-Hans`), neerlandés (`nl`), francés (`fr`), alemán (`de`), italiano (`it`), portugués (`pt`) y español (`es`).
* **Detección de marcas de selección o casillas**: Form Recognizer admite la detección y extracción de marcas de selección, como casillas y botones de radio. Las marcas de selección se extraen de `Layout` y ahora también es posible etiquetar y entrenar en `Train Custom Model` - _Entrenamiento con etiquetas_ para extraer pares clave-valor de marcas de selección.
* **Composición de modelos**: permite que se compongan varios modelos y que se llamen con un único identificador de modelo. Cuando envía un documento para que se analice con un identificador de modelo compuesto, primero se realiza un paso de clasificación para enrutarlo al modelo personalizado correcto. La composición de modelos está disponible para `Train Custom Model` - _Entrenamiento con etiquetas_.
* **Nombre de modelo**: agregue un nombre descriptivo a los modelos personalizados para facilitar su administración y seguimiento.
* **[Nuevo modelo precompilado para tarjetas de presentación](concept-business-cards.md)** para extraer campos comunes de tarjetas de presentación (en inglés).
* **[Nuevas configuraciones regionales para recibos precompilados](concept-receipts.md)** : además de EN-US, ahora se admiten EN-AU, EN-CA, EN-GB y EN-IN.
* **Mejoras de calidad** para `Layout`, `Train Custom Model` - _Entrenamiento sin etiquetas_ y _Entrenamiento con etiquetas_.

La **versión 2.0** incluye la siguiente actualización:

* Las [bibliotecas cliente](quickstarts/client-library.md) para NET, Python, Java y JavaScript ahora tienen disponibilidad general.

Hay **nuevos ejemplos** disponibles en GitHub.

* En el [cuaderno de estrategias de Forms: recetas de extracción de conocimientos](https://github.com/microsoft/knowledge-extraction-recipes-forms) se recopilan procedimientos recomendados de interacciones de clientes reales de Form Recognizer y se proporcionan ejemplos de código útiles, listas de comprobación y canalizaciones de ejemplo que se usan en el desarrollo de estos proyectos.
* La [herramienta de etiquetado de ejemplo](https://github.com/microsoft/OCR-Form-Tools) se ha actualizado para admitir la nueva funcionalidad de v2.1. Vea este [inicio rápido](label-tool.md) para empezar a trabajar con la herramienta.
* El ejemplo de Form Recognizer [Intelligent Kiosk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) muestra cómo integrar `Analyze Receipt` y `Train Custom Model` - _Entrenamiento sin etiquetas_.

## <a name="july-2020"></a>Julio de 2020

### <a name="new-features"></a>Nuevas características
<!-- markdownlint-disable MD004 -->
* **Referencia de v2.0 disponible**: consulte la [referencia de la API v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) y los SDK actualizados para [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme) y [JavaScript](/javascript/api/overview/azure/).
* **Mejoras en tablas y extracciones**: incluye mejoras tanto en la precisión como en las extracciones de tablas, en concreto, la funcionalidad para aprender encabezados y estructuras de tablas en el _entrenamiento personalizado sin etiquetas_.

* **Compatibilidad de divisas**: detección y extracción de símbolos de moneda globales.
* **Azure Gov**: Form Recognizer ya está disponible también en Azure Gov.
* **Características de seguridad mejoradas**:
  * **Bring Your Own Key**: Form Recognizer cifra automáticamente los datos cuando se guardan en la nube para protegerlos y para ayudarle a satisfacer los compromisos de cumplimiento y de seguridad de la organización. De forma predeterminada, su suscripción usa claves de cifrado administradas por Microsoft. Ahora también puede administrar la suscripción con sus propias claves de cifrado. Las [claves administradas por el cliente (CMK), que también se conocen como Bring Your Own Key](./encrypt-data-at-rest.md), ofrecen más flexibilidad para crear, rotar, deshabilitar y revocar controles de acceso. También permite auditar las claves de cifrado que se usan para proteger los datos.
  * **Puntos de conexión privados**: le permiten [acceder de forma segura a los datos a través de Private Link](../../private-link/private-link-overview.md) en una red virtual.

## <a name="june-2020"></a>Junio de 2020

### <a name="new-features"></a>Nuevas características

* **CopyModel API se agregó a los SDK de cliente**: ya se pueden usar los SDK de cliente para copiar modelos de una suscripción a otra. Consulte [Copia de seguridad y recuperación de modelos](./disaster-recovery.md) para obtener información general sobre esta característica.
* **Integración de Azure Active Directory**: ya puede usar sus credenciales de Azure AD para autenticar sus objetos cliente de Form Recognizer en los SDK.
* **Cambios específicos en los SDK**: este cambio incluye tanto la adición de características secundarias como los cambios importantes. Consulte el registro de cambios del SDK para obtener más información.
  * [Registro de cambios del SDK para C# versión preliminar 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Registro de cambios del SDK para Python versión preliminar 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Registro de cambios del SDK para Java versión preliminar 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Registro de cambios del SDK para JavaScript versión preliminar 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Abril de 2020

### <a name="new-features"></a>Nuevas características

* **Compatibilidad del SDK con la versión preliminar pública de la API Form Recognizer v2.0**: este mes hemos ampliado nuestro servicio de soporte técnico para incluir un SDK de versión preliminar para la versión 2.0 (versión preliminar) de Form Recognizer. Use los vínculos siguientes para empezar a trabajar con el lenguaje que prefiera:
  * [SDK de .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [SDK de Java](/java/api/overview/azure/ai-formrecognizer-readme)
  * [SDK de Python](/python/api/overview/azure/ai-formrecognizer-readme)
  * [SDK de JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme)

  El nuevo SDK es compatible con todas las características de la API REST v 2.0 de Form Recognizer. Por ejemplo, puede entrenar un modelo con o sin etiquetas y extraer texto, pares clave-valor y tablas de los formularios; extraer datos de las confirmaciones con el servicio de confirmaciones pregeneradas; y extraer texto y tablas con el servicio de diseño de los documentos. Puede compartir sus comentarios sobre los SDK mediante el [formulario de comentarios de SDK](https://aka.ms/FR_SDK_v1_feedback).

* **Copia del modelo personalizado** Ahora puede copiar modelos entre regiones y suscripciones mediante la nueva característica Copiar modelo personalizado. Antes de invocar la API Copy Custom Model, primero debe obtener la autorización para realizar copias en el recurso de destino mediante la llamada a la operación de autorización de copia en el punto de conexión del recurso de destino.

  * API REST [Generate a copy authorization](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization)
  * API REST [Copy a custom model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel)

### <a name="security-improvements"></a>Mejoras de seguridad

* Las claves administradas por el cliente Customer-Managed Keys ya están disponibles para Form Recognizer. Para obtener más información, consulte [Cifrado de datos en reposo para Form Recognizer](./encrypt-data-at-rest.md).
* Use Identidades administradas para acceder a los recursos de Azure con Azure Active Directory. Para obtener más información, consulte [Autorización para obtener acceso a identidades administradas](../../cognitive-services/authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Marzo de 2020

### <a name="new-features"></a>Nuevas características

* **Tipos de valor para etiquetar**: ahora puede especificar los tipos de valores que va a etiquetar con la herramienta de etiquetado de ejemplo de Form Recognizer. Actualmente se admiten los siguientes tipos de valor y variaciones:
  * `string`
    * predeterminado, `no-whitespaces`, `alphanumeric`
  * `number`
    * predeterminado, `currency`
  * `date`
    * predeterminado, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Para aprender a usar esta característica, consulte la [herramienta de etiquetado de ejemplo](label-tool.md#specify-tag-value-types).

* **Visualización de tablas**: la herramienta de etiquetado de ejemplo ahora muestra las tablas que se reconocieron en el documento. Esta característica permite ver las tablas que se han reconocido y extraído del documento antes de etiquetarlas y analizarlas. Esta característica se puede activar o desactivar mediante la opción de capas.

  La siguiente imagen es un ejemplo de cómo se reconocen y extraen las tablas:

  > [!div class="mx-imgBorder"]
  > ![Visualización de tablas mediante la herramienta de etiquetado de ejemplo](./media/whats-new/table-viz.png)

    Las tablas extraídas están disponible en la salida JSON en `"pageResults"`.

  > [!IMPORTANT]
  > No se admite el etiquetado de tablas. Si las tablas no se reconocen ni se extraen automáticamente, solo se pueden etiquetar como pares clave-valor. Al etiquetar tablas como pares clave-valor, etiquete cada celda como un valor único.

### <a name="extraction-enhancements"></a>Mejoras de extracción

Esta versión incluye mejoras de extracción y mejoras de precisión, en concreto, la capacidad de etiquetar y extraer varios pares clave-valor en la misma línea de texto.

### <a name="sample-labeling-tool-is-now-open-source"></a>La herramienta de etiquetado de ejemplo ahora es de código abierto

La herramienta de etiquetado de ejemplo de Form Recognizer ahora está disponible como proyecto de código abierto. Puede integrarla en sus soluciones y realizar cambios específicos del cliente para satisfacer sus necesidades.

Para más información sobre la herramienta de etiquetado de ejemplo de Form Recognizer, revise la documentación disponible en [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Cumplimiento de TLS 1.2

TLS 1.2 ya se exige en todas las solicitudes HTTP para este servicio. Para más información, consulte [Seguridad de Azure Cognitive Services](../../cognitive-services/cognitive-services-security.md).

## <a name="january-2020"></a>Enero de 2020

En esta versión se introduce Form Recognizer 2.0 (versión preliminar). En las secciones siguientes encontrará más información sobre nuevas características, mejoras y cambios.

### <a name="new-features"></a>Nuevas características

* **Modelo personalizado**
  * **Entrenar con etiquetas** Ahora puede entrenar un modelo personalizado con datos etiquetados manualmente. Este método genera modelos con un mejor rendimiento y puede generar modelos que funcionen con formularios complejos o formularios que contengan valores sin claves.
  * **API asincrónica** Puede usar llamadas API asincrónicas para entrenar y analizar grandes conjuntos de datos y archivos.
  * **Compatibilidad con archivos TIFF** Ahora puede entrenar y extraer datos de documentos TIFF.
  * **Mejoras en la precisión de la extracción**

* **Modelo de recibo pregenerado**
  * **Importes de propinas** Ahora puede extraer importes de propina y otros valores escritos a mano.
  * **Extracción de elementos de línea** Puede extraer valores de elementos de línea de recibos.
  * **Valores de confianza** Puede ver la confianza del modelo para cada valor extraído.
  * **Mejoras en la precisión de la extracción**

* **Extracción de diseño** Ahora puede usar la API de diseño para extraer datos de texto y datos de tabla de los formularios.

### <a name="custom-model-api-changes"></a>Cambios en la API del modelo personalizado

Se ha cambiado el nombre de todas las API de entrenamiento y uso de modelos personalizados y algunos métodos sincrónicos son asincrónicos ahora. A continuación se indican los cambios principales:

* El proceso de entrenamiento de un modelo es ahora asincrónico. Inicie el entrenamiento mediante la llamada API **/custom/models**. Esta llamada devuelve un identificador de operación, que se puede pasar a **custom/models/{modelID}** para devolver los resultados del entrenamiento.
* La extracción de clave y valor se inicia ahora mediante la llamada API **/custom/models/{modelID}/analyze**. Esta llamada devuelve un identificador de operación, que se puede pasar a **custom/models/{modelID}/analyzeResults/{resultID}** para devolver los resultados de la extracción.
* Los identificadores de operación de la operación de entrenamiento se encuentran ahora en el encabezado **Location** de las respuestas HTTP, no en el encabezado **Operation-Location**.

### <a name="receipt-api-changes"></a>Cambios en la API del recibo

Se ha cambiado el nombre de las API para leer recibos de venta.

* La extracción de datos de recibo ahora se inicia mediante la llamada API **/prebuilt/receipt/analyze**. Esta llamada devuelve un identificador de operación, que se puede pasar a **/prebuilt/receipt/analyzeResults/{resultID}** para devolver los resultados de la extracción.

### <a name="output-format-changes"></a>Cambios del formato de salida

Las respuestas JSON para todas las llamadas API tienen nuevos formatos. Algunas claves y valores se han agregado, quitado o cambiado de nombre. Consulte las guías de inicio rápido para obtener ejemplos de los formatos JSON actuales.

## <a name="next-steps"></a>Pasos siguientes

Complete un [inicio rápido](quickstarts/client-library.md) para empezar a escribir una aplicación de procesamiento de formularios con Form Recognizer en el lenguaje de desarrollo que prefiera.

## <a name="see-also"></a>Consulte también

* [¿Qué es Form Recognizer?](./overview.md)