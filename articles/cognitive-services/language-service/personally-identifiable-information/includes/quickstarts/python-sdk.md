---
author: aahill
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: d6f1b615bafaecadcb452018af7bf36f05072f31
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093349"
---
[Documentación de referencia](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paquete (PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.1.0/) | [Ejemplos](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)


## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">cree un recurso de idioma</a> en Azure Portal para obtener la clave y el punto de conexión. Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a la API. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.
* Para usar la característica Analizar, necesitará un recurso de idioma con el plan de tarifa estándar (S).

## <a name="setting-up"></a>Instalación

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Después de instalar Python, puede instalar la biblioteca cliente con:

```console
pip install azure-ai-textanalytics==5.1.0
```

## <a name="code-example"></a>Ejemplo de código

Cree un nuevo archivo de Python y copie el código siguiente. No olvide reemplazar la variable `key` por la clave del recurso y la variable `endpoint` por el punto de conexión del recurso. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```python
key = "paste-your-key-here"
endpoint = "paste-your-endpoint-here"

from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

# Authenticate the client using your key and endpoint 
def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()

# Example method for detecting sensitive information (PII) from text 
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

## <a name="output"></a>Output

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
