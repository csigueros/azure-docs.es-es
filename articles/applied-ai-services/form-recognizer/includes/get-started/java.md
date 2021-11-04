---
title: 'Introducción: Biblioteca cliente de Form Recognizer para Java v2.1'
description: Use el SDK de Form Recognizer para Java crear una aplicación de procesamiento de formularios que extraiga pares clave-valor y datos de tabla de los documentos personalizados.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e93e5b157cc7bb17eb2e66a97770f4af0d915c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030892"
---
> [!IMPORTANT]
>
> Este inicio rápido tiene como destino la versión **2.1** de Azure Form Recognizer.

[Documentación de referencia](/java/api/overview/azure/ai-formrecognizer-readme) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Paquete (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Ejemplos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

En este inicio rápido, usará las siguientes API para extraer datos estructurados de formularios y documentos:

* [Diseño](#try-it-layout-model)

* [Factura](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).
* Un [kit de desarrollo de Java (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), versión 8 o posterior.
* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

> [!TIP]
> Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Una vez que se implemente el recurso, haga clic en **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

## <a name="set-up"></a>Configurar

### <a name="create-a-new-gradle-project"></a>Creación de un proyecto de Gradle

En una ventana de la consola (como cmd, PowerShell o Bash), cree un directorio para la aplicación llamado **form-recognizer-app** y vaya hasta él.

```console
mkdir form-recognizer-app && form-recognizer-app
```

1. Ejecute el comando `gradle init` desde el directorio de trabajo. Este comando creará archivos de compilación esenciales para Gradle, como *build.gradle.kts*, que se usa en tiempo de ejecución para crear y configurar la aplicación.

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.1")
}
```

### <a name="create-a-java-file"></a>Creación de un archivo Java

En el directorio de trabajo, ejecute el siguiente comando:

```console
mkdir -p src/main/java
```

Va a crear la estructura de directorios siguiente:

:::image type="content" source="../../media/quickstarts/java-directories.png" alt-text="Captura de pantalla: Estructura de directorios de Java":::

Vaya al directorio java y cree un archivo llamado *FormRecognizer.java*.  Ábralo en el editor o el IDE que prefiera y agregue las instrucciones `import` y la declaración del paquete siguientes:

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>Seleccione un ejemplo de código para copiar y pegar en el método main de la aplicación:

* [**Layout**](#try-it-layout-model)

* [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo sobre la [seguridad](/azure/cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

Extraiga de los documentos texto, marcas de selección, estilos de texto y estructuras de tablas, junto con las coordenadas de su región delimitadora.

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Para analizar un archivo determinado en un identificador URI, utilizará el método `beginRecognizeContentFromUrl`.
> * Se ha agregado el valor del URI del archivo a la variable `formUrl` en el método main.

Actualice la clase **FormRecognizer** de la aplicación con el código siguiente (asegúrese de actualizar las variables de clave y punto de conexión con valores de la instancia de Form Recognizer en Azure Portal):

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
                .credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

    System.out.println("Get form content...");
        GetContent(recognizerClient, formUrl);
  }
    private static void GetContent(FormRecognizerClient recognizerClient, String invoiceUri) {
        String analyzeFilePath = invoiceUri;
        SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller = recognizerClient
                .beginRecognizeContentFromUrl(analyzeFilePath);

        List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
        // </snippet_getcontent_call>
        // <snippet_getcontent_print>
        contentResult.forEach(formPage -> {
            // Table information
            System.out.println("----Recognizing content ----");
            System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
                    formPage.getHeight(), formPage.getUnit());
            formPage.getTables().forEach(formTable -> {
                System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                        formTable.getColumnCount());
                formTable.getCells().forEach(formTableCell -> {
                    System.out.printf("Cell has text %s.%n", formTableCell.getText());
                });
                System.out.println();
            });
        });
    }

```

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos de determinados tipos de documentos comunes con modelos entrenados previamente, utilizando una factura como ejemplo.

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.
> * Para analizar un archivo determinado en un URI, usará el método `beginRecognizeInvoicesFromUrl`.
> * Se ha agregado el valor del URI del archivo a la variable `invoiceUrl` en el método main.
> * Por motivos de simplicidad, aquí no se muestran todos los campos que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto de [factura](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Elección de un modelo precompilado

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los modelos precompilados que actualmente admite el servicio Form Recognizer:

* [**Factura**](../../concept-invoice.md): extrae texto, marcas de selección, tablas, campos e información clave de las facturas.
* [**Recibo**](../../concept-receipt.md): extrae texto e información clave de los recibos.
* [**Documento de identificación**](../../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**Tarjeta de presentación**](../../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

Actualice la clase **FormRecognizer** de la aplicación con el código siguiente (asegúrese de actualizar las variables de clave y punto de conexión con valores de la instancia de Form Recognizer en Azure Portal):

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder().credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

    System.out.println("Analyze invoice...");
        AnalyzeInvoice(recognizerClient, invoiceUrl);
  }
    private static void AnalyzeInvoice(FormRecognizerClient recognizerClient, String invoiceUrl) {
      SyncPoller < FormRecognizerOperationResult,
        List < RecognizedForm >> recognizeInvoicesPoller = recognizerClient.beginRecognizeInvoicesFromUrl(invoiceUrl);
      List < RecognizedForm > recognizedInvoices = recognizeInvoicesPoller.getFinalResult();

      for (int i = 0; i < recognizedInvoices.size(); i++) {
        RecognizedForm recognizedInvoice = recognizedInvoices.get(i);
        Map < String,
        FormField > recognizedFields = recognizedInvoice.getFields();
        System.out.printf("----------- Recognized invoice info for page %d -----------%n", i);
        FormField vendorNameField = recognizedFields.get("VendorName");
        if (vendorNameField != null) {
            if (FieldValueType.STRING == vendorNameField.getValue().getValueType()) {
                String merchantName = vendorNameField.getValue().asString();
                System.out.printf("Vendor Name: %s, confidence: %.2f%n", merchantName, vendorNameField.getConfidence());
            }
        }

        FormField vendorAddressField = recognizedFields.get("VendorAddress");
        if (vendorAddressField != null) {
            if (FieldValueType.STRING == vendorAddressField.getValue().getValueType()) {
                String merchantAddress = vendorAddressField.getValue().asString();
                System.out.printf("Vendor address: %s, confidence: %.2f%n", merchantAddress, vendorAddressField.getConfidence());
            }
        }

        FormField customerNameField = recognizedFields.get("CustomerName");
        if (customerNameField != null) {
            if (FieldValueType.STRING == customerNameField.getValue().getValueType()) {
                String merchantAddress = customerNameField.getValue().asString();
                System.out.printf("Customer Name: %s, confidence: %.2f%n", merchantAddress, customerNameField.getConfidence());
            }
        }

        FormField customerAddressRecipientField = recognizedFields.get("CustomerAddressRecipient");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.STRING == customerAddressRecipientField.getValue().getValueType()) {
                String customerAddr = customerAddressRecipientField.getValue().asString();
                System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n", customerAddr, customerAddressRecipientField.getConfidence());
            }
        }

        FormField invoiceIdField = recognizedFields.get("InvoiceId");
        if (invoiceIdField != null) {
            if (FieldValueType.STRING == invoiceIdField.getValue().getValueType()) {
                String invoiceId = invoiceIdField.getValue().asString();
                System.out.printf("Invoice Id: %s, confidence: %.2f%n", invoiceId, invoiceIdField.getConfidence());
            }
        }

        FormField invoiceDateField = recognizedFields.get("InvoiceDate");
        if (customerNameField != null) {
            if (FieldValueType.DATE == invoiceDateField.getValue().getValueType()) {
                LocalDate invoiceDate = invoiceDateField.getValue().asDate();
                System.out.printf("Invoice Date: %s, confidence: %.2f%n", invoiceDate, invoiceDateField.getConfidence());
            }
        }

        FormField invoiceTotalField = recognizedFields.get("InvoiceTotal");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.FLOAT == invoiceTotalField.getValue().getValueType()) {
                Float invoiceTotal = invoiceTotalField.getValue().asFloat();
                System.out.printf("Invoice Total: %.2f, confidence: %.2f%n", invoiceTotal, invoiceTotalField.getConfidence());
            }
        }
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
> [Documentación de referencia de la API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Referencia de la biblioteca de Form Recognizer para Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-stable&preserve-view=true)
