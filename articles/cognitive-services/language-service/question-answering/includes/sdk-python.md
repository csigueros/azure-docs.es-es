---
title: 'Guía de inicio rápido: Biblioteca cliente de respuesta a preguntas para Python'
description: En esta guía de inicio rápido se muestra cómo empezar a trabajar con la biblioteca cliente para Python.
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/11/2021
ms.openlocfilehash: 0aa0f65e9a277cbcb28870b970ead3e357057b2f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354025"
---
Utilice esta guía de inicio rápido para la biblioteca cliente de respuesta a preguntas de Python para:

* Obtener una respuesta de una instancia de knowledge base.
* Obtener una respuesta de un cuerpo de texto que envíe junto con la pregunta.
* Obtener la puntuación de confianza para la respuesta a la pregunta.

[Documentación de referencia de API][questionanswering_refdocs] | [Código fuente][questionanswering_client_src] | [Paquete (PyPi)][questionanswering_pypi_package] | [Ejemplos de Python][questionanswering_samples] |

[questionanswering_client_class]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/
[questionanswering_pypi_package]: https://pypi.org/project/azure-ai-language-questionanswering/
[questionanswering_refdocs]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/samples/README.md

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Las preguntas y respuestas requieren un [recurso de idioma](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) con la característica de respuesta a preguntas personalizada habilitada para generar un punto de conexión y una clave de API.
    * Una vez implementado el recurso de idioma, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que cree para conectarse a la API. Más adelante en este inicio rápido, debe pegar la clave y el punto de conexión en el código que se incluye a continuación.
* Instancia de knowledge base existente en la consulta. Si no ha configurado una instancia de knowledge base, puede seguir las instrucciones de la [**guía de inicio rápido de Language Studio**](../quickstart/sdk.md). O bien, agregue una instancia de knowledge base que use esta [dirección URL de la guía del usuario de Surface](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) como origen de datos.

## <a name="setting-up"></a>Instalación

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Después de instalar Python, puede instalar la biblioteca cliente con:

```console
pip install azure-ai-language-questionanswering
```

## <a name="query-a-knowledge-base"></a>Consulta de una base de conocimiento

### <a name="generate-an-answer-from-a-knowledge-base"></a>Generación de una respuesta de la instancia de knowledge base

En el ejemplo siguiente podrá consultar una instancia de knowledge base mediante [get_answers](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers) para obtener una respuesta a su pregunta. Puede copiar este código en un archivo .py dedicado o en una celda de [Jupyter Notebook/Lab](https://jupyter.org/).

Recuerde que deberá actualizar el código y proporcionar sus propios valores para las variables siguientes.

|Nombre de la variable | Valor |
|--------------------------|-------------|
| `endpoint`               | Este valor se puede encontrar en la sección **Claves y punto de conexión** al examinar el recurso en Azure Portal. Como alternativa, puede encontrar el valor en **Language Studio** > **respuesta a la pregunta** > **Implementar la instancia de knowledge base** > **Obtener la dirección URL de predicción**. Un punto de conexión de ejemplo es: `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | Este valor se puede encontrar en la sección **Claves y punto de conexión** al examinar el recurso en Azure Portal. Puede usar Key1 o Key2. Tenga siempre dos claves válidas para poder realizar la rotación segura de claves sin tiempo de inactividad. Como alternativa, puede encontrar el valor en **Language Studio** > **respuesta a la pregunta** > **Implementar la instancia de knowledge base** > **Obtener la dirección URL de predicción**. El valor de clave forma parte de la solicitud de ejemplo.|
| `knowledge_base_project` | Nombre del proyecto de respuesta a preguntas.|
| `deployment`             | Hay dos posibles valores: `test` y `production`. `production` depende de que haya implementado su instancia de knowledge base de **Language Studio** > **respuesta a la pregunta** > **Implementar la knowledge bases**.|

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}")
knowledge_base_project = "{YOUR-PROJECT-NAME}"
deployment = "production"

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How much battery life do I have left?"
        output = client.get_answers(
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
    print("Q: {}".format(question))
    print("A: {}".format(output.answers[0].answer))

if __name__ == '__main__':
    main()
```

Aunque estamos codificando de forma rígida las variables para nuestro ejemplo. En el caso de producción, considere la posibilidad de usar alguna forma segura de almacenar las credenciales, y acceder a ellas. Por ejemplo, [Azure Key Vault](../../../../key-vault/general/overview.md) proporciona almacenamiento de claves seguro.

Al ejecutar el código anterior, si usa el origen de datos de los requisitos previos, recibirá una respuesta similar a la siguiente:

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

Para obtener información sobre la fiabilidad de la respuesta a la pregunta de si esta es la respuesta correcta, agregue una instrucción de impresión adicional debajo de las instrucciones impresas existentes:

```python
print("Q: {}".format(question))
print("A: {}".format(output.answers[0].answer))
print("Confidence Score: {}".format(output.answers[0].confidence_score)) # add this line 
```

Ahora recibirá un resultado con una puntuación de confianza:

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
Confidence Score: 0.9185
```

La puntuación de confianza devuelve un valor entre 0 y 1. Puede pensar en este valor como un porcentaje y multiplicarlo por 100, por lo que una puntuación de confianza de 0,9185 significa que la respuesta a preguntas es un 91,85 % segura, ya que esta es la respuesta correcta a la pregunta en función de la instancia de knowledge base.

Si quiere excluir las respuestas en las que la puntuación de confianza está por debajo de un umbral determinado, puede modificar [AnswerOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersOptions) para agregar el parámetro `confidence_threshold`.

```python
        output = client.get_answers(
            confidence_threshold = 0.95, #add this line
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
```

Puesto que sabemos por nuestra ejecución anterior del código que nuestra puntuación de confianza es `.9185`, establecer el umbral en `.95` dará como resultado la [ respuesta predeterminada ](../how-to/change-default-answer.md) que se devuelve.

```
Q: How much battery life do I have left?
A: No good match found in KB
Confidence Score: 0.0
```

## <a name="query-text-without-a-knowledge-base"></a>Consulta de texto sin instancia de knowledge base

También puede usar la respuesta a preguntas sin instancia de knowledge base con [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text). En este caso, se proporciona la respuesta a la pregunta mediante una pregunta y los registros de texto asociados en los que le gustaría buscar una respuesta en el momento en que se envía la solicitud.

En este ejemplo, solo necesita modificar las variables para `endpoint` y `credential`.

```python
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient
from azure.ai.language.questionanswering import models as qna

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY")

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How long does it takes to charge a surface?"
        input = qna.AnswersFromTextOptions(
            question=question,
            text_documents=[
                "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.",
                "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
            ]
        )


        output = client.get_answers_from_text(input)

    best_answer = [a for a in output.answers if a.confidence > 0.9][0]
    print(u"Q: {}".format(input.question))
    print(u"A: {}".format(best_answer.answer))
    print("Confidence Score: {}".format(output.answers[0].confidence))

if __name__ == '__main__':
    main()
```

Puede copiar este código en un archivo .py dedicado o en una celda nueva de [Jupyter Notebook/Lab](https://jupyter.org/). Este ejemplo devolverá un resultado de:

```
Q: How long does it takes to charge surface?
A: Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.
Confidence Score: 0.9254655838012695
```

En este caso, se recorren en iteración todas las respuestas y solo se devuelve la respuesta con la puntuación de confianza más alta que sea mayor que 0,9. Para obtener más información sobre las opciones disponibles con [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text), revise los parámetros [AnswersFromTextOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersFromTextOptions).