---
title: 'Inicio rápido: SDK v3.0 de Form Recognizer para Java | Versión preliminar'
titleSuffix: Azure Applied AI Services
description: Procesamiento de formularios y documentos, extracción de datos y análisis mediante la versión v3.0 de los SDK de la biblioteca cliente de Form Recognizer para Java (versión preliminar)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 23d7ef3bf6eb29e81723cb102427ef32d8bc83b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017408"
---
# <a name="quickstart-java-client-library-sdk-v30--preview"></a>Inicio rápido: SDK v3.0 de la biblioteca cliente de Java | Versión preliminar

>[!NOTE]
> Form Recognizer v3.0 está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas.

[Documentación de referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Paquete (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Ejemplos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

Comience a usar Azure Form Recognizer mediante el lenguaje de programación Java. Azure Form Recognizer es un componente de Azure Applied AI Services en la nube que usa el aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Puede llamar fácilmente a los modelos de Form Recognizer mediante la integración de los SDK de biblioteca cliente en los flujos de trabajo y aplicaciones. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.

Para más información sobre las características y las opciones de desarrollo de Form Recognizer, visite nuestra página de [información general](../overview.md#form-recognizer-features-and-development-options).

En este inicio rápido, usará las siguientes características para analizar y extraer datos y valores de formularios y documentos:

* [🆕 **Documento general**](#try-it-general-document-model): análisis y extracción de texto, tablas, estructura, pares clave-valor y entidades con nombre.

* [**Diseño**](#try-it-layout-model): análisis y extracción de tablas, líneas, palabras y marcas de selección, como botones de radio y casillas en documentos de formularios, sin necesidad de entrenar un modelo.

* [**Factura precompilada**](#try-it-prebuilt-model): análisis y extracción de campos comunes de facturas mediante un modelo de facturas entrenado previamente.

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).
* Un [kit de desarrollo de Java (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), versión 8 o posterior.
* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

> [!TIP] 
> Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Una vez que se implemente el recurso, haga clic en **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

## <a name="set-up"></a>Configurar

### <a name="create-a-new-gradle-project"></a>Creación de un proyecto de Gradle

En una ventana de la consola (como cmd, PowerShell o Bash), cree un directorio para la aplicación llamado **form-recognizer-app** y vaya hasta él.

```console
mkdir form-recognizer-app && form-recognizer-app
```

Ejecute el comando `gradle init` desde el directorio de trabajo. Este comando creará archivos de compilación esenciales para Gradle, como *build.gradle.kts*, que se usa en tiempo de ejecución para crear y configurar la aplicación.

```console
gradle init --type basic
```

1. Cuando se le solicite que elija un **DSL**, seleccione **Kotlin**.

1. Acepte el nombre del proyecto predeterminado (form-recognizer-app).

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

En este inicio rápido se usa el administrador de dependencias Gradle. Puede encontrar la biblioteca de cliente y la información de otros administradores de dependencias en el [repositorio central de Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

En el archivo *build.gradle.kts* del proyecto, incluya la biblioteca cliente como una instrucción `implementation`, junto con los complementos y la configuración necesarios.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "4.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Creación de un archivo Java

En el directorio de trabajo, ejecute el siguiente comando:

```console
mkdir -p src/main/java
```

Va a crear la estructura de directorios siguiente:

:::image type="content" source="../media/quickstarts/java-directories.png" alt-text="Captura de pantalla: Estructura de directorios de Java":::

Vaya a la nueva carpeta y cree un archivo llamado *FormRecognizer.java* (se crea durante el comando `gradle init`). Ábralo en el editor o el IDE que prefiera y agregue las instrucciones `import` y la declaración del paquete siguientes:

```java
package com.azure.ai.formrecognizer;

import com.azure.ai.formrecognizer.models.AnalyzeDocumentOptions;
import com.azure.ai.formrecognizer.models.AnalyzedDocument;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.HttpPipeline;
import com.azure.core.http.HttpPipelineBuilder;
import com.azure.core.util.Context;

import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.util.Arrays;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>Seleccione un ejemplo de código para copiar y pegar en el método main de la aplicación:

* [**Documento general**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo sobre la [seguridad](../../../cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-general-document-model"></a>**Pruébelo**: modelo de documento general

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Para analizar un archivo determinado en un URI, usará el método `beginAnalyzeDocumentFromUrl` y pasará `prebuilt-document` como identificador del modelo. El valor devuelto es un objeto `AnalyzeResult` que contiene datos sobre el documento enviado.
> * Se ha agregado el valor del URI del archivo a la variable `documentUrl` en el método main.
> * Por motivos de simplicidad, aquí no se muestran todos los campos de entidad que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto del [documento general](../concept-general-document.md#named-entity-recognition-ner-categories).

Actualice la clase **FormRecognizer** de la aplicación con el código siguiente (asegúrese de actualizar las variables de clave y punto de conexión con valores de la instancia de Form Recognizer en Azure Portal):

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
        String modelId = "prebuilt-document";
        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeDocumentPoller =
            documentAnalysisClient.beginAnalyzeDocumentFromUrl(modelId, documentUrl);

        AnalyzeResult analyzeResult = analyzeDocumentPoller.getFinalResult();

        for (int i = 0; i < analyzeResult.getDocuments().size(); i++) {
            final AnalyzedDocument analyzedDocument = analyzeResult.getDocuments().get(i);
            System.out.printf("----------- Analyzing document %d -----------%n", i);
            System.out.printf("Analyzed document has doc type %s with confidence : %.2f%n",
                analyzedDocument.getDocType(), analyzedDocument.getConfidence());
        }

        analyzeResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // words
            documentPage.getWords().forEach(documentWord - >
                System.out.printf("Word %s has a confidence score of %.2f%n.",
                    documentWord.getContent(),
                    documentWord.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n",
                    documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }

        // Entities
        analyzeResult.getEntities().forEach(documentEntity - > {
            System.out.printf("Entity category : %s, sub-category %s%n: ",
                documentEntity.getCategory(), documentEntity.getSubCategory());
            System.out.printf("Entity content: %s%n: ", documentEntity.getContent());
            System.out.printf("Entity confidence: %.2f%n", documentEntity.getConfidence());
        });

        // Key-value
        analyzeResult.getKeyValuePairs().forEach(documentKeyValuePair - > {
            System.out.printf("Key content: %s%n", documentKeyValuePair.getKey().getContent());
            System.out.printf("Key content bounding region: %s%n",
                documentKeyValuePair.getKey().getBoundingRegions().toString());

            System.out.printf("Value content: %s%n", documentKeyValuePair.getValue().getContent());
            System.out.printf("Value content bounding region: %s%n", documentKeyValuePair.getValue().getBoundingRegions().toString());
        });
        Build a custom document analysis model
        In 3. x.x, creating a custom model required specifying useTrainingLabels to indicate whether to use labeled data when creating the custom model with the beginTraining method.
        In 4. x.x, we introduced the new general document model(prebuilt - document) to replace the train without labels functionality from 3. x.x which extracts entities, key - value pairs, and layout from a document with the beginBuildModel method.In 4. x.x the beginBuildModel always returns labeled data otherwise.
        Train a custom model using 3. x.x beginTraining:

            String trainingFilesUrl = "{SAS_URL_of_your_container_in_blob_storage}";
        SyncPoller < FormRecognizerOperationResult, CustomFormModel > trainingPoller =
            formTrainingClient.beginTraining(trainingFilesUrl,
                false,
                new TrainingOptions()
                .setModelName("my model trained without labels"),
                Context.NONE);

        CustomFormModel customFormModel = trainingPoller.getFinalResult();

        // Model Info
        System.out.printf("Model Id: %s%n", customFormModel.getModelId());
        System.out.printf("Model name given by user: %s%n", customFormModel.getModelName());
        System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());

        System.out.println("Recognized Fields:");
        // looping through the subModels, which contains the fields they were trained on
        // Since the given training documents are unlabeled we still group them but, they do not have a label.
        customFormModel.getSubmodels().forEach(customFormSubmodel - > {
            System.out.printf("Submodel Id: %s%n: ", customFormSubmodel.getModelId());
            // Since the training data is unlabeled, we are unable to return the accuracy of this model
            customFormSubmodel.getFields().forEach((field, customFormModelField) - >
                System.out.printf("Field: %s Field Label: %s%n",
                    field, customFormModelField.getLabel()));
        });

        System.out.println();
        customFormModel.getTrainingDocuments().forEach(trainingDocumentInfo - > {
            System.out.printf("Document name: %s%n", trainingDocumentInfo.getName());
            System.out.printf("Document status: %s%n", trainingDocumentInfo.getStatus());
            System.out.printf("Document page count: %d%n", trainingDocumentInfo.getPageCount());
            if (!trainingDocumentInfo.getErrors().isEmpty()) {
                System.out.println("Document Errors:");
                trainingDocumentInfo.getErrors().forEach(formRecognizerError - >
                    System.out.printf("Error code %s, Error message: %s%n", formRecognizerError.getErrorCode(),
                        formRecognizerError.getMessage()));
            }
        });
    }
```

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

Extraiga de los documentos texto, marcas de selección, estilos de texto y estructuras de tablas, junto con las coordenadas de su región delimitadora.

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Para analizar un archivo determinado en un URI, usará el método `beginAnalyzeDocumentFromUrl` y pasará `prebuilt-layout` como identificador del modelo. El valor devuelto es un objeto `AnalyzeResult` que contiene datos sobre el documento enviado.
> * Se ha agregado el valor del URI del archivo a la variable `documentUrl` en el método main.

Actualice la clase **FormRecognizer** de la aplicación con el código siguiente (asegúrese de actualizar las variables de clave y punto de conexión con valores de la instancia de Form Recognizer en Azure Portal):

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"
        String modelId = "prebuilt-layout";

        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeLayoutResultPoller =
            documentAnalysisClient.beginAnalyzeDocument(modelId, documentUrl);

        AnalyzeResult analyzeLayoutResult = analyzeLayoutResultPoller.getFinalResult();

        // pages
        analyzeLayoutResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // selection marks
            documentPage.getSelectionMarks().forEach(documentSelectionMark - >
                System.out.printf("Selection mark is %s and is within a bounding box %s with confidence %.2f.%n",
                    documentSelectionMark.getState().toString(),
                    documentSelectionMark.getBoundingBox().toString(),
                    documentSelectionMark.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeLayoutResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n", documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }
    }
```

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos de determinados tipos de documentos comunes con modelos entrenados previamente, utilizando una factura como ejemplo.

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.
> * Para analizar un archivo determinado en un URI, usará el método `beginAnalyzeDocumentFromUrl` y pasará `prebuilt-invoice` como identificador del modelo. El valor devuelto es un objeto `AnalyzeResult` que contiene datos sobre el documento enviado.
> * Se ha agregado el valor del URI del archivo a la variable `invoiceUrl` en el método main.
> * Por motivos de simplicidad, aquí no se muestran todos los pares clave-valor que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto de [factura](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Elección del identificador de modelo precompilado de factura

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los identificadores de modelo de los modelos precompilados admitidos actualmente por el servicio Form Recognizer:

* [**prebuilt-invoice**](../concept-invoice.md): extrae texto, marcas de selección, tablas, pares clave-valor e información clave de las facturas.
* [**prebuilt-receipt**](../concept-receipt.md): extrae texto e información clave de los recibos.
* [**prebuilt-idDocument**](../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**prebuilt-businessCard**](../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

Actualice la clase **FormRecognizer** de la aplicación con el código siguiente (asegúrese de actualizar las variables de clave y punto de conexión con valores de la instancia de Form Recognizer en Azure Portal):

```java

public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"
        String modelId = "prebuilt-invoice";

        PollerFlux < DocumentOperationResult, AnalyzeResult > analyzeInvoicePoller = client.beginAnalyzeDocumentFromUrl("prebuilt-invoice", invoiceUrl);

        Mono < AnalyzeResult > analyzeInvoiceResultMono = analyzeInvoicePoller
            .last()
            .flatMap(pollResponse - > {
                if (pollResponse.getStatus().isComplete()) {
                    System.out.println("Polling completed successfully");
                    return pollResponse.getFinalResult();
                } else {
                    return Mono.error(new RuntimeException("Polling completed unsuccessfully with status:" +
                        pollResponse.getStatus()));
                }
            });

        analyzeInvoiceResultMono.subscribe(analyzeInvoiceResult - > {
            for (int i = 0; i < analyzeInvoiceResult.getDocuments().size(); i++) {
                AnalyzedDocument analyzedInvoice = analyzeInvoiceResult.getDocuments().get(i);
                Map < String, DocumentField > invoiceFields = analyzedInvoice.getFields();
                System.out.printf("----------- Analyzing invoice  %d -----------%n", i);
                DocumentField vendorNameField = invoiceFields.get("VendorName");
                if (vendorNameField != null) {
                    if (DocumentFieldType.STRING == vendorNameField.getType()) {
                        String merchantName = vendorNameField.getValueString();
                        System.out.printf("Vendor Name: %s, confidence: %.2f%n",
                            merchantName, vendorNameField.getConfidence());
                    }
                }

                DocumentField vendorAddressField = invoiceFields.get("VendorAddress");
                if (vendorAddressField != null) {
                    if (DocumentFieldType.STRING == vendorAddressField.getType()) {
                        String merchantAddress = vendorAddressField.getValueString();
                        System.out.printf("Vendor address: %s, confidence: %.2f%n",
                            merchantAddress, vendorAddressField.getConfidence());
                    }
                }

                DocumentField customerNameField = invoiceFields.get("CustomerName");
                if (customerNameField != null) {
                    if (DocumentFieldType.STRING == customerNameField.getType()) {
                        String merchantAddress = customerNameField.getValueString();
                        System.out.printf("Customer Name: %s, confidence: %.2f%n",
                            merchantAddress, customerNameField.getConfidence());
                    }
                }

                DocumentField customerAddressRecipientField = invoiceFields.get("CustomerAddressRecipient");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.STRING == customerAddressRecipientField.getType()) {
                        String customerAddr = customerAddressRecipientField.getValueString();
                        System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n",
                            customerAddr, customerAddressRecipientField.getConfidence());
                    }
                }

                DocumentField invoiceIdField = invoiceFields.get("InvoiceId");
                if (invoiceIdField != null) {
                    if (DocumentFieldType.STRING == invoiceIdField.getType()) {
                        String invoiceId = invoiceIdField.getValueString();
                        System.out.printf("Invoice ID: %s, confidence: %.2f%n",
                            invoiceId, invoiceIdField.getConfidence());
                    }
                }

                DocumentField invoiceDateField = invoiceFields.get("InvoiceDate");
                if (customerNameField != null) {
                    if (DocumentFieldType.DATE == invoiceDateField.getType()) {
                        LocalDate invoiceDate = invoiceDateField.getValueDate();
                        System.out.printf("Invoice Date: %s, confidence: %.2f%n",
                            invoiceDate, invoiceDateField.getConfidence());
                    }
                }

                DocumentField invoiceTotalField = invoiceFields.get("InvoiceTotal");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.FLOAT == invoiceTotalField.getType()) {
                        Float invoiceTotal = invoiceTotalField.getValueFloat();
                        System.out.printf("Invoice Total: %.2f, confidence: %.2f%n",
                            invoiceTotal, invoiceTotalField.getConfidence());
                    }
                }

                DocumentField invoiceItemsField = invoiceFields.get("Items");
                if (invoiceItemsField != null) {
                    System.out.printf("Invoice Items: %n");
                    if (DocumentFieldType.LIST == invoiceItemsField.getType()) {
                        List < DocumentField > invoiceItems = invoiceItemsField.getValueList();
                        invoiceItems.stream()
                            .filter(invoiceItem - > DocumentFieldType.MAP == invoiceItem.getType())
                            .map(formField - > formField.getValueMap())
                            .forEach(formFieldMap - > formFieldMap.forEach((key, formField) - > {
                                // See a full list of fields found on an invoice here:
                                // https://aka.ms/formrecognizer/invoicefields
                                if ("Description".equals(key)) {
                                    if (DocumentFieldType.STRING == formField.getType()) {
                                        String name = formField.getValueString();
                                        System.out.printf("Description: %s, confidence: %.2fs%n",
                                            name, formField.getConfidence());
                                    }
                                }
                                if ("Quantity".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float quantity = formField.getValueFloat();
                                        System.out.printf("Quantity: %f, confidence: %.2f%n",
                                            quantity, formField.getConfidence());
                                    }
                                }
                                if ("UnitPrice".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float unitPrice = formField.getValueFloat();
                                        System.out.printf("Unit Price: %f, confidence: %.2f%n",
                                            unitPrice, formField.getConfidence());
                                    }
                                }
                                if ("ProductCode".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float productCode = formField.getValueFloat();
                                        System.out.printf("Product Code: %f, confidence: %.2f%n",
                                            productCode, formField.getConfidence());
                                    }
                                }
                            }));
                    }
                }
            }
        });
    }
}

```

## <a name="build-and-run-your-application"></a>compilar y ejecutar la aplicación

Vuelva al directorio principal del proyecto, **form-recognizer-app**.

1. Compile la aplicación con el comando `build`:

```console
gradle build
```

1. Ejecute la aplicación con el comando `run`:

```console
gradle run
```

¡Enhorabuena! En este inicio rápido, ha usado el SDK de Form Recognizer para Java para analizar varios formularios y documentos de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Documentación de referencia de la versión v3.0 de la API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Referencia de la biblioteca de Form Recognizer para Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html)
