---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.custom: devx-track-java, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: e71fc8573798c96676edfe9b172a8e419d60330a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091103"
---
[Documentación de referencia](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-stable) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paquete](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [Ejemplos](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* [Kit de desarrollo de Java](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK), versión 8 o posterior
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">cree un recurso de idioma</a> en Azure Portal para obtener la clave y el punto de conexión.  Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a la API. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.
* Para usar la característica Analizar, necesitará un recurso de idioma con el plan de tarifa estándar (S).

## <a name="setting-up"></a>Instalación

### <a name="add-the-client-library"></a>Incorporación de la biblioteca cliente

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

## <a name="code-example"></a>Ejemplo de código

Cree un archivo de Java llamado `EntityLinking.java`. Abra el archivo y copie el código siguiente. No olvide reemplazar la variable `key` por la clave del recurso y la variable `endpoint` por el punto de conexión del recurso. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

import java.util.List;
import java.util.Arrays;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;

import com.azure.ai.textanalytics.util.*;


public class Example {

    private static String KEY = "replace-with-your-key-here";
    private static String ENDPOINT = "replace-with-your-endpoint-here";

    public static void main(String[] args) {
        TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
        healthExample(client);
    }

    // Method to authenticate the client object with your key and endpoint
    static TextAnalyticsClient authenticateClient(String key, String endpoint) {
        return new TextAnalyticsClientBuilder()
                .credential(new AzureKeyCredential(key))
                .endpoint(endpoint)
                .buildClient();
    }

    // Example method for extracting information from healthcare-related text 
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
                    "Results of Azure Text Analytics for health entities\" Model, version: %s%n",
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
}

```

### <a name="output"></a>Output

```console
Poller status: IN_PROGRESS.
Operation created time: 2021-08-24T16:05:15Z, expiration time: 2021-08-25T16:05:15Z.
Poller status: SUCCESSFULLY_COMPLETED.
Results of Azure Text Analytics for health entities Model, version: 2021-05-15
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
