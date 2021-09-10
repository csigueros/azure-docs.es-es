---
title: 'Inicio rápido: Biblioteca cliente v3 de Text Analytics para Java | Microsoft Docs'
description: Introducción a la biblioteca cliente v3 de Text Analytics para Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 3284cef03ce899a096816ba23736d29fdc228eb5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864958"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

[Documentación de referencia de v3.2-preview.1](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paquete v3.2-preview](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.1) | [Ejemplos](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

# <a name="version-31"></a>[Versión 3.1](#tab/version-3-1)

[Documentación de referencia](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-stable) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [Paquete v3.1](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [Ejemplos](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* [Kit de desarrollo de Java](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK), versión 8 o posterior
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="cree un recurso de Text Analytics"  target="_blank">cree un recurso de Text Analytics </a> en Azure Portal para obtener la clave y el punto de conexión.  Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a API Text Analytics. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.
* Para usar la característica Analyze, necesitará un recurso de Text Analytics con un plan de tarifa Estándar (S).

## <a name="setting-up"></a>Instalación

### <a name="add-the-client-library"></a>Incorporación de la biblioteca cliente

# <a name="version-32-preview"></a>[Versión 3.2-preview](#tab/version-3-2)

Cree un proyecto de Maven en el entorno de desarrollo o IDE que prefiera. Luego, agregue la siguiente dependencia al archivo *pom.xml* del proyecto. La sintaxis de implementación [de otras herramientas de compilación](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.1) se puede encontrar en línea.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.2.0-beta.1</version>
    </dependency>
</dependencies>
```

Características incluidas en esta versión de Text Analytics API:

* análisis de opiniones
* Minería de opiniones
* Detección de idiomas
* Reconocimiento de entidades
* Vinculación de entidad
* Reconocimiento de información de identificación personal
* Extracción de la frase clave
* Métodos asincrónicos
* Text Analytics for Health
* Resumen de texto

# <a name="version-31"></a>[Versión 3.1](#tab/version-3-1)

Cree un proyecto de Maven en el entorno de desarrollo o IDE que prefiera. Luego, agregue la siguiente dependencia al archivo *pom.xml* del proyecto. La sintaxis de implementación [de otras herramientas de compilación](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) se puede encontrar en línea.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

Características incluidas en esta versión de Text Analytics API:

* análisis de opiniones
* Minería de opiniones
* Detección de idiomas
* Reconocimiento de entidades
* Vinculación de entidad
* Reconocimiento de información de identificación personal
* Extracción de la frase clave
* Métodos asincrónicos
* Text Analytics for Health

---

Cree un archivo de Java llamado `TextAnalyticsSamples.java`. Abra el archivo y agregue las siguientes instrucciones `import`:

```java
import com.azure.ai.textanalytics.TextAnalyticsAsyncClient;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

import java.util.Arrays;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;

import com.azure.core.util.polling.LongRunningOperationStatus;
import com.azure.ai.textanalytics.util.*;
import com.azure.core.http.rest.PagedResponse;
```

En el archivo de Java, agregue una nueva clase y agregue el punto de conexión y la clave del recurso de Azure, como se muestra a continuación.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Agregue el siguiente método principal a la clase. Más adelante definirá los métodos a los que llama aquí.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
    sentimentAnalysisExample(client);
    sentimentAnalysisWithOpinionMiningExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
    analyzeOperationExample(client);
}
```


## <a name="object-model"></a>Modelo de objetos

El cliente de Text Analytics es un objeto `TextAnalyticsClient` que se autentica en Azure mediante su clave y que proporciona funciones para aceptar texto como cadenas individuales o como un lote. Puede enviar texto a la API de forma sincrónica o asincrónica. El objeto de respuesta contendrá la información del análisis de todos los documentos que envíe. 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>Autenticar el cliente

Cree un método para crear una instancia del objeto `TextAnalyticsClient` con la clave y el punto de conexión del recurso Text Analytics. Este ejemplo es el mismo para las versiones 3.0 y 3.1 de la API.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


En el método de `main()` del programa, llame al método de autenticación para crear una instancia del cliente.

## <a name="sentiment-analysis"></a>análisis de opiniones

Cree una función denominada `sentimentAnalysisExample()` que tome el cliente que creó anteriormente y llame a su función `analyzeSentiment()`. El objeto `AnalyzeSentimentResult` devuelto contendrá `documentSentiment` y `sentenceSentiments` si la operación se realiza correctamente, o `errorMessage` si no. 

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="opinion-mining"></a>Minería de opiniones

Para realizar análisis de sentimiento con la minería de opiniones, cree una nueva función llamada `sentimentAnalysisWithOpinionMiningExample()` que tome el cliente que creó anteriormente y llame a su función `analyzeSentiment()` con el objeto de opción de configuración `AnalyzeSentimentOptions`. El objeto `AnalyzeSentimentResult` devuelto contendrá `documentSentiment` y `sentenceSentiments` si la operación se realiza correctamente, o `errorMessage` si no. 

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getOpinions().forEach(opinion -> {
            TargetSentiment targetSentiment = opinion.getTarget();
            System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                    targetSentiment.getText());
            for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                        assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
            }
        });
    });
}
```

### <a name="output"></a>Output

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

## <a name="language-detection"></a>Detección de idiomas

Cree una función denominada `detectLanguageExample()` que tome el cliente que creó anteriormente y llame a su función `detectLanguage()`. El objeto `DetectLanguageResult` devuelto contendrá un idioma principal detectado, una lista de otros idiomas detectados si la operación se realiza correctamente o `errorMessage` si no. Este ejemplo es el mismo para las versiones 3.0 y 3.1 de la API.

> [!Tip]
> En algunos casos, puede ser difícil eliminar la ambigüedad de los idiomas en función de la entrada. Puede usar el parámetro `countryHint` para especificar un código de país de dos letras. De forma predeterminada, la API usa "US" como valor predeterminado de countryHint; para eliminar este comportamiento, puede restablecer este parámetro y configurarlo como una cadena `countryHint = ""` vacía. Para establecer un valor predeterminado diferente, establezca la propiedad `TextAnalyticsClientOptions.DefaultCountryHint` y pásela durante la inicialización del cliente.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Output

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Reconocimiento de entidades con nombre (NER)

Cree una función denominada `recognizeEntitiesExample()` que tome el cliente que creó anteriormente y llame a su función `recognizeEntities()`. El objeto devuelto `CategorizedEntityCollection` contendrá una lista de `CategorizedEntity` si la operación se realiza correctamente o `errorMessage` si no.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```


## <a name="personally-identifiable-information-pii-recognition"></a>Reconocimiento de la información de identificación personal

Cree una función denominada `recognizePiiEntitiesExample()` que tome el cliente que creó anteriormente y llame a su función `recognizePiiEntities()`. El objeto devuelto `PiiEntityCollection` contendrá una lista de `PiiEntity` si la operación se realiza correctamente o `errorMessage` si no. También contendrá el texto censurado, que consta del texto de entrada con todas las entidades identificables reemplazadas por `*****`.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Output

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

## <a name="entity-linking"></a>Vinculación de entidad

Cree una función denominada `recognizeLinkedEntitiesExample()` que tome el cliente que creó anteriormente y llame a su función `recognizeLinkedEntities()`. El objeto devuelto `LinkedEntityCollection` contendrá una lista de `LinkedEntity` si la operación se realiza correctamente o `errorMessage` si no. Puesto que las entidades vinculadas se identifican de forma única, las apariciones de la misma entidad se agrupan bajo un objeto `LinkedEntity` como una lista de objetos `LinkedEntityMatch`.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```

## <a name="key-phrase-extraction"></a>Extracción de la frase clave

Cree una función denominada `extractKeyPhrasesExample()` que tome el cliente que creó anteriormente y llame a su función `extractKeyPhrases()`. El objeto `ExtractKeyPhraseResult` devuelto contendrá una lista de frases clave si la operación se realiza correctamente o `errorMessage` si no. Este ejemplo es el mismo para las versiones 3.0 y 3.1 de la API.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Output

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="extract-health-entities"></a>Extracción de entidades de mantenimiento

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

Puede usar Text Analytics para realizar una solicitud asincrónica para extraer entidades de atención sanitaria del texto. En el ejemplo siguiente se muestra un ejemplo básico. Puede encontrar un ejemplo más avanzado [en GitHub](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeHealthcareEntities.java).

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```java
static void healthExample(TextAnalyticsClient client){
    List<TextDocumentInput> documents = Arrays.asList(
            new TextDocumentInput("0",
                    "Prescribed 100mg ibuprofen, taken twice daily."));

    AnalyzeHealthcareEntitiesOptions options = new AnalyzeHealthcareEntitiesOptions().setIncludeStatistics(true);

    SyncPoller<AnalyzeHealthcareEntitiesOperationDetail, AnalyzeHealthcareEntitiesPagedIterable>
            syncPoller = client.beginAnalyzeHealthcareEntities(documents, options, Context.NONE);

    System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());
    syncPoller.waitForCompletion();

    // Task operation statistics
    AnalyzeHealthcareEntitiesOperationDetail operationResult = syncPoller.poll().getValue();
    System.out.printf("Operation created time: %s, expiration time: %s.%n",
            operationResult.getCreatedAt(), operationResult.getExpiresAt());
    System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());

    for (AnalyzeHealthcareEntitiesResultCollection resultCollection : syncPoller.getFinalResult()) {
        // Model version
        System.out.printf(
                "Results of Azure Text Analytics \"Analyze Healthcare Entities\" Model, version: %s%n",
                resultCollection.getModelVersion());

        for (AnalyzeHealthcareEntitiesResult healthcareEntitiesResult : resultCollection) {
            System.out.println("Document ID = " + healthcareEntitiesResult.getId());
            System.out.println("Document entities: ");
            // Recognized healthcare entities
            for (HealthcareEntity entity : healthcareEntitiesResult.getEntities()) {
                System.out.printf(
                        "\tText: %s, normalized name: %s, category: %s, subcategory: %s, confidence score: %f.%n",
                        entity.getText(), entity.getNormalizedText(), entity.getCategory(),
                        entity.getSubcategory(), entity.getConfidenceScore());
            }
            // Recognized healthcare entity relation groups
            for (HealthcareEntityRelation entityRelation : healthcareEntitiesResult.getEntityRelations()) {
                System.out.printf("Relation type: %s.%n", entityRelation.getRelationType());
                for (HealthcareEntityRelationRole role : entityRelation.getRoles()) {
                    HealthcareEntity entity = role.getEntity();
                    System.out.printf("\tEntity text: %s, category: %s, role: %s.%n",
                            entity.getText(), entity.getCategory(), role.getName());
                }
            }
        }
    }
}
```

En el método `main()`, llame al ejemplo de código anterior. 

```java
public static void main(String[] args) {
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
    healthExample(client);
}
```

### <a name="output"></a>output

```console
Poller status: IN_PROGRESS.
Operation created time: 2021-07-20T19:45:50Z, expiration time: 2021-07-21T19:45:50Z.
Poller status: SUCCESSFULLY_COMPLETED.
Results of Azure Text Analytics "Analyze Healthcare Entities" Model, version: 2021-05-15
Document ID = 0
Document entities: 
    Text: 100mg, normalized name: null, category: Dosage, subcategory: null, confidence score: 1.000000.
    Text: ibuprofen, normalized name: ibuprofen, category: MedicationName, subcategory: null, confidence score: 1.000000.
    Text: twice daily, normalized name: null, category: Frequency, subcategory: null, confidence score: 1.000000.
Relation type: DosageOfMedication.
    Entity text: 100mg, category: Dosage, role: Dosage.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
Relation type: FrequencyOfMedication.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
    Entity text: twice daily, category: Frequency, role: Frequency.
```

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Uso de la API de forma asincrónica con la operación Analyze

Puede usar la operación Analyze para realizar solicitudes por lotes asincrónicas para: NER, extracción de frases clave, análisis de sentimiento y detección de información de identificación personal. En el ejemplo siguiente se muestra un ejemplo básico sobre una operación. Puede encontrar un ejemplo más avanzado [en GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md).

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Cree una nueva función llamada `analyzeBatchActionsExample()`, que llame a la función `beginAnalyzeBatchActions()`. El resultado será una operación de larga duración que se sondeará para encontrar resultados.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void analyzeActionsExample(TextAnalyticsClient client){
    List<TextDocumentInput> documents = new ArrayList<>();
    documents.add(new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen."));


    SyncPoller<AnalyzeActionsOperationDetail, AnalyzeActionsResultPagedIterable> syncPoller =
            client.beginAnalyzeActions(documents,
                    new TextAnalyticsActions().setDisplayName("Example analyze task")
                            .setRecognizeEntitiesActions(new RecognizeEntitiesAction())
                            .setExtractKeyPhrasesActions(
                                    new ExtractKeyPhrasesAction().setModelVersion("latest")),
                    new AnalyzeActionsOptions().setIncludeStatistics(false),
                    Context.NONE);

    // Task operation statistics details
    while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
        final AnalyzeActionsOperationDetail operationDetail = syncPoller.poll().getValue();
        System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d,"
                        + " failed actions: %d, total actions: %d%n",
                operationDetail.getDisplayName(), operationDetail.getSucceededCount(),
                operationDetail.getInProgressCount(), operationDetail.getFailedCount(),
                operationDetail.getTotalCount());
    }

    syncPoller.waitForCompletion();

    Iterable<PagedResponse<AnalyzeActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
    for (PagedResponse<AnalyzeActionsResult> perPage : pagedResults) {
        System.out.printf("Response code: %d, Continuation Token: %s.%n", perPage.getStatusCode(),
                perPage.getContinuationToken());
        for (AnalyzeActionsResult actionsResult : perPage.getElements()) {
            System.out.println("Entities recognition action results:");
            for (RecognizeEntitiesActionResult actionResult : actionsResult.getRecognizeEntitiesResults()) {
                if (!actionResult.isError()) {
                    for (RecognizeEntitiesResult documentResult : actionResult.getDocumentsResults()) {
                        if (!documentResult.isError()) {
                            for (CategorizedEntity entity : documentResult.getEntities()) {
                                System.out.printf(
                                        "\tText: %s, category: %s, confidence score: %f.%n",
                                        entity.getText(), entity.getCategory(), entity.getConfidenceScore());
                            }
                        } else {
                            System.out.printf("\tCannot recognize entities. Error: %s%n",
                                    documentResult.getError().getMessage());
                        }
                    }
                } else {
                    System.out.printf("\tCannot execute Entities Recognition action. Error: %s%n",
                            actionResult.getError().getMessage());
                }
            }

            System.out.println("Key phrases extraction action results:");
            for (ExtractKeyPhrasesActionResult actionResult : actionsResult.getExtractKeyPhrasesResults()) {
                if (!actionResult.isError()) {
                    for (ExtractKeyPhraseResult documentResult : actionResult.getDocumentsResults()) {
                        if (!documentResult.isError()) {
                            System.out.println("\tExtracted phrases:");
                            for (String keyPhrases : documentResult.getKeyPhrases()) {
                                System.out.printf("\t\t%s.%n", keyPhrases);
                            }
                        } else {
                            System.out.printf("\tCannot extract key phrases. Error: %s%n",
                                    documentResult.getError().getMessage());
                        }
                    }
                } else {
                    System.out.printf("\tCannot execute Key Phrases Extraction action. Error: %s%n",
                            actionResult.getError().getMessage());
                }
            }
        }
    }
}
```

Después de agregar este ejemplo a la aplicación, llame a su método `main()`.

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Output

```console
Action display name: Example analyze task, Successfully completed actions: 1, in-process actions: 1, failed actions: 0, total actions: 2
Response code: 200, Continuation Token: null.
Entities recognition action results:
    Text: Microsoft, category: Organization, confidence score: 1.000000.
    Text: Bill Gates, category: Person, confidence score: 1.000000.
    Text: Paul Allen, category: Person, confidence score: 1.000000.
Key phrases extraction action results:
    Extracted phrases:
        Bill Gates.
        Paul Allen.
        Microsoft.
```

También puede usar la operación Analyze para ejecutar NER, la extracción de frases clave, el análisis de sentimiento y la detección de información de identificación personal. Consulte el [ejemplo de Analyze](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeActionsAsync.java) de GitHub.

## <a name="text-summarization"></a>Resumen de texto

# <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

Puede usar Text Analytics para resumir fragmentos grandes de texto. El método es asincrónico y devolverá las oraciones superiores cuando finalice la operación de larga duración.

```java
static void summarizationExample(TextAnalyticsClient client) {
    List<String> documents = new ArrayList<>();
    documents.add(
            "The extractive summarization feature in Text Analytics uses natural language processing techniques "
            + "to locate key sentences in an unstructured text document. "
            + "These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. "
            + "They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. "
            + "In the public preview, extractive summarization supports several languages. "
            + "It is based on pretrained multilingual transformer models, part of our quest for holistic representations. "
            + "It draws its strength from transfer learning across monolingual and harness the shared nature of languages "
            + "to produce models of improved quality and efficiency.");

    SyncPoller<AnalyzeActionsOperationDetail, AnalyzeActionsResultPagedIterable> syncPoller =
            client.beginAnalyzeActions(documents,
                    new TextAnalyticsActions().setDisplayName("{tasks_display_name}")
                            .setExtractSummaryActions(
                                    new ExtractSummaryAction()),
                    "en",
                    new AnalyzeActionsOptions());

    syncPoller.waitForCompletion();

    syncPoller.getFinalResult().forEach(actionsResult -> {
        System.out.println("Extractive Summarization action results:");
        for (ExtractSummaryActionResult actionResult : actionsResult.getExtractSummaryResults()) {
            if (!actionResult.isError()) {
                for (ExtractSummaryResult documentResult : actionResult.getDocumentsResults()) {
                    if (!documentResult.isError()) {
                        System.out.println("\tExtracted summary sentences:");
                        for (SummarySentence summarySentence : documentResult.getSentences()) {
                            System.out.printf(
                                    "\t\t Sentence text: %s, length: %d, offset: %d, rank score: %f.%n",
                                    summarySentence.getText(), summarySentence.getLength(),
                                    summarySentence.getOffset(), summarySentence.getRankScore());
                        }
                    } else {
                        System.out.printf("\tCannot extract summary sentences. Error: %s%n",
                                documentResult.getError().getMessage());
                    }
                }
            } else {
                System.out.printf("\tCannot execute Extractive Summarization action. Error: %s%n",
                        actionResult.getError().getMessage());
            }
        }
    });
}
```

Después de agregar este ejemplo a la aplicación, llame a su método `main()`.

```java
summarizationExample(client);
```

# <a name="version-31"></a>[Versión 3.1](#tab/version-3-1)

Esta característica no está disponible en la versión 3.1.

---
