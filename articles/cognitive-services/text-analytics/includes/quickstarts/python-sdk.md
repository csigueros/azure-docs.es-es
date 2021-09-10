---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.author: aahi
ms.openlocfilehash: c24d206c330aa5244dbc174302b447c1ec4a1706
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864941"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

[Documentación de referencia de v3.2-preview](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paquete v3.2-preview (PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.2.0b1/) | [Ejemplos](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-31"></a>[Versión 3.1](#tab/version-3-1)

[Documentación de referencia de v3.1](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python-preview) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paquete v3.1 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.1.0/) | [Ejemplos](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="cree un recurso de Text Analytics"  target="_blank">cree un recurso de Text Analytics </a> en Azure Portal para obtener la clave y el punto de conexión. Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a API Text Analytics. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.
* Para usar la característica Analyze, necesitará un recurso de Text Analytics con un plan de tarifa Estándar (S).

## <a name="setting-up"></a>Instalación

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Después de instalar Python, puede instalar la biblioteca cliente mediante los siguientes comandos. 

# <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```console
pip install azure-ai-textanalytics==5.2.0b1
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

```console
pip install azure-ai-textanalytics==5.1.0
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

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

Cree un archivo de Python y variables para el punto de conexión y la clave de suscripción de Azure del recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modelo de objetos

El cliente de Text Analytics es un objeto `TextAnalyticsClient` que se autentica en Azure. El cliente proporciona varios métodos para analizar el texto. 

Al realizar el procesamiento por lotes, el texto se envía a la API como una lista de `documents`, que son objetos `dictionary` que contienen una combinación de atributos `id`, `text` y `language`, según el método usado. El atributo `text` almacena el texto que se va a analizar en el origen `language` y `id` puede ser cualquier valor. 

El objeto de respuesta es una lista que contiene la información analizada para cada documento.

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>Autenticar el cliente

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


Cree una función para crear una instancia del objeto `TextAnalyticsClient` con los objetos `key` y `endpoint` creados anteriormente. Luego, cree un cliente. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

## <a name="sentiment-analysis"></a>análisis de opiniones

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


Cree una función denominada `sentiment_analysis_example()` que tome el cliente como argumento y, luego, llame a la función `analyze_sentiment()`. El objeto de respuesta devuelto contendrá la etiqueta de sentimiento y la puntuación de todo el documento de entrada, así como un análisis de sentimiento de cada oración.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Minería de opiniones

Para realizar el análisis de sentimiento con la minería de opiniones, cree una nueva función llamada `sentiment_analysis_with_opinion_mining_example()` que tome el cliente como argumento y, a continuación, llame a la función `analyze_sentiment()` con la marca de opción `show_opinion_mining=True`. El objeto de respuesta devuelto contendrá la etiqueta de sentimiento y la puntuación de todo el documento de entrada con el análisis de sentimiento de cada oración, así como del aspecto y del nivel de opinión.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00

Press any key to continue . . .

```

## <a name="language-detection"></a>Detección de idiomas

Cree una función denominada `language_detection_example()` que tome el cliente como argumento y, luego, llame a la función `detect_language()`. El objeto de respuesta devuelto contendrá el idioma detectado en `primary_language` si la operación se realiza correctamente y `error` si no.

> [!NOTE]
> * El método siguiente se aplica tanto a las versiones `3.1` como `3.2-preview` de la API.
> * En algunos casos, puede ser difícil eliminar la ambigüedad de los idiomas en función de la entrada. Puede usar el parámetro `country_hint` para especificar un código de país de dos letras. De forma predeterminada, la API usa "US" como valor predeterminado de countryHint; para eliminar este comportamiento, puede restablecer este parámetro y configurarlo como una cadena `country_hint : ""` vacía. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```

### <a name="output"></a>Output

```console
Language:  French
```

## <a name="named-entity-recognition-ner"></a>Reconocimiento de entidades con nombre (NER)

Cree una función denominada `entity_recognition_example` que tome el cliente como argumento y, luego, llame a la función `recognize_entities()` y recorra en iteración los resultados. El objeto de respuesta devuelto contendrá la lista de entidades detectadas en `entity` si la operación se realiza correctamente y `error` si no. Para cada entidad detectada, imprima su categoría y subcategoría si existen.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Output

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

## <a name="personally-identifiable-information-pii-recognition"></a>Reconocimiento de la información de identificación personal

Cree una función denominada `pii_recognition_example` que tome el cliente como argumento y, luego, llame a la función `recognize_pii_entities()` y recorra en iteración los resultados. El objeto de respuesta devuelto contendrá la lista de entidades detectadas en `entity` si la operación se realiza correctamente y `error` si no. Para cada entidad detectada, imprima su categoría y subcategoría si existen.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Output

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

## <a name="entity-linking"></a>Vinculación de entidad

Cree una función denominada `entity_linking_example()` que tome el cliente como argumento y, luego, llame a la función `recognize_linked_entities()` y recorra en iteración los resultados. El objeto de respuesta devuelto contendrá la lista de entidades detectadas en `entities` si la operación se realiza correctamente y `error` si no. Puesto que las entidades vinculadas se identifican de forma única, las apariciones de la misma entidad se agrupan bajo un objeto `entity` como una lista de objetos `match`.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Output

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

## <a name="key-phrase-extraction"></a>Extracción de la frase clave

Cree una función denominada `key_phrase_extraction_example()` que tome el cliente como argumento y, luego, llame a la función `extract_key_phrases()`. El resultado contendrá la lista de frases clave detectadas en `key_phrases` si la operación ha tenido éxito, o `error` si no. Imprima las frases clave detectadas.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Output

```console
    Key Phrases:
         cat
         veterinarian
```

## <a name="extract-health-entities"></a>Extracción de entidades de mantenimiento

Puede usar Text Analytics para realizar una solicitud asincrónica para extraer entidades de atención sanitaria del texto. En el ejemplo siguiente se muestra un ejemplo básico. Puede encontrar un ejemplo más avanzado [en GitHub](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_healthcare_entities.py).

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

``` python
def health_example(client):
    documents = [
        """
        Patient needs to take 50 mg of ibuprofen.
        """
    ]

    poller = client.begin_analyze_healthcare_entities(documents)
    result = poller.result()

    docs = [doc for doc in result if not doc.is_error]

    for idx, doc in enumerate(docs):
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("...Normalized Text: {}".format(entity.normalized_text))
            print("...Category: {}".format(entity.category))
            print("...Subcategory: {}".format(entity.subcategory))
            print("...Offset: {}".format(entity.offset))
            print("...Confidence score: {}".format(entity.confidence_score))
        for relation in doc.entity_relations:
            print("Relation of type: {} has the following roles".format(relation.relation_type))
            for role in relation.roles:
                print("...Role '{}' with entity '{}'".format(role.name, role.entity.text))
        print("------------------------------------------")
health_example(client)
```

### <a name="output"></a>Output

```console
Entity: 50 mg
...Normalized Text: None
...Category: Dosage
...Subcategory: None
...Offset: 31
...Confidence score: 1.0
Entity: ibuprofen
...Normalized Text: ibuprofen
...Category: MedicationName
...Subcategory: None
...Offset: 40
...Confidence score: 1.0
Relation of type: DosageOfMedication has the following roles
...Role 'Dosage' with entity '50 mg'
...Role 'Medication' with entity 'ibuprofen'
```

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Uso de la API de forma asincrónica con la operación Analyze

Puede usar la operación Analyze para realizar solicitudes por lotes asincrónicas para: NER, extracción de frases clave, análisis de sentimiento y detección de información de identificación personal. En el ejemplo siguiente se muestra un ejemplo básico. Puede encontrar un ejemplo más avanzado [en GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_actions.py).

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

Cree una función denominada `analyze_batch_example()` que tome el cliente como argumento y, luego, llame a la función `begin_analyze_actions()`. El resultado será una operación de larga duración que se sondeará para encontrar resultados.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction,
    ExtractKeyPhrasesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction(), ExtractKeyPhrasesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result)]
        first_action_result = action_results[0][0]
        print("Results of Entities Recognition action:")

        for entity in first_action_result.entities:
            print("Entity: {}".format(entity.text))
            print("...Category: {}".format(entity.category))
            print("...Confidence Score: {}".format(entity.confidence_score))
            print("...Offset: {}".format(entity.offset))
            print("...Length: {}".format(entity.length))
        print("------------------------------------------")

        second_action_result = action_results[0][1]
        print("Results of Key Phrase Extraction action:")
        
        for key_phrase in second_action_result.key_phrases:
            print("Key Phrase: {}\n".format(key_phrase))
        print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Output

```console
Results of Entities Recognition action:
Entity: Microsoft
...Category: Organization
...Confidence Score: 1.0
...Offset: 0
...Length: 9
Entity: Bill Gates
...Category: Person
...Confidence Score: 1.0
...Offset: 25
...Length: 10
Entity: Paul Allen
...Category: Person
...Confidence Score: 1.0
...Offset: 40
...Length: 10
------------------------------------------
Results of Key Phrase Extraction action:
Key Phrase: Bill Gates

Key Phrase: Paul Allen

Key Phrase: Microsoft

------------------------------------------
```

## <a name="text-summarization"></a>Resumen de texto

# <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

[!INCLUDE [Summarization operation pricing](../summarization-operation-pricing-caution.md)]

Puede usar Text Analytics para resumir fragmentos grandes de texto. Cree una función denominada `summarization_example()` que tome el cliente como argumento y, luego, llame a la función `begin_analyze_actions()`. El resultado será una operación de larga duración que se sondeará para encontrar resultados. 

```python
def sample_extractive_summarization(client):
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import (
        TextAnalyticsClient,
        ExtractSummaryAction
    ) 

    document = [
        "The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document. "
        "These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. " 
        "They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. "
        "In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. "
        "It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency. "
    ]

    poller = client.begin_analyze_actions(
        document,
        actions=[
            ExtractSummaryAction(MaxSentenceCount=4)
        ],
    )

    document_results = poller.result()
    for result in document_results:
        extract_summary_result = result[0]  # first document, first result
        if extract_summary_result.is_error:
            print("...Is an error with code '{}' and message '{}'".format(
                extract_summary_result.code, extract_summary_result.message
            ))
        else:
            print("Summary extracted: \n{}".format(
                " ".join([sentence.text for sentence in extract_summary_result.sentences]))
            )

sample_extractive_summarization(client)
```

# <a name="version-31"></a>[Versión 3.1](#tab/version-3-1)

Esta característica no está disponible en la versión 3.1.

---
