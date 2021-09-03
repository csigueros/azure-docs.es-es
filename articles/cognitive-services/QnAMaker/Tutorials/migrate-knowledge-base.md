---
title: Migración de bases de conocimiento (QnA Maker)
description: La migración de una base de conocimiento requiere la exportación de una base de conocimiento y la posterior importación a otra.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ea0a02366f2c2d2c3fd656d9a6dbce111d632422
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121196"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar una base de conocimiento mediante la exportación e importación

Es posible que quiera crear una copia de la base de conocimiento por varios motivos:

* Copia de una base de conocimiento de QnA Maker de disponibilidad general a Respuesta a preguntas personalizada 
* Para implementar un proceso de copia de seguridad y restauración 
* Integración con una canalización de CI/CD 
* Cuando se quieren mover los datos a otras regiones

## <a name="prerequisites"></a>Requisitos previos

# <a name="qna-maker-ga-stable-release"></a>[Disponibilidad general de QnA Maker (versión estable)](#tab/v1)

> * Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.
> * Un [recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) creado en Azure Portal. Recuerde el identificador de Azure Active Directory, la suscripción y el nombre de recurso de QnA que seleccionó al crear el recurso.
> * Configurar un [servicio QnA Maker](../How-To/set-up-qnamaker-service-azure.md) nuevo

# <a name="custom-question-answering-preview-release"></a>[Respuesta a preguntas personalizada (versión preliminar)](#tab/v2)

> * Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.
> * Un [recurso de Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) con la característica de respuesta a preguntas personalizada habilitada en Azure Portal. Recuerde el identificador de Azure Active Directory, la suscripción y el nombre del recurso de Text Analytics que seleccionó al crear el recurso.
> * Configure [Respuesta a preguntas personalizada](../How-To/set-up-qnamaker-service-azure.md)

---

## <a name="export-a-knowledge-base"></a>Exportación de una base de conocimiento
1. Inicie sesión en el [portal de QnA Maker](https://qnamaker.ai).
1. Seleccione la base de conocimiento que desea migrar.

1. En la página **Settings** (Configuración), tiene las opciones para exportar **QnAs**, (Preguntas y respuestas) **Synonyms** (Sinónimos) o **Knowledge Base Replica** (Réplica de base de conocimiento). Puede optar por descargar los datos en formato .tsv/.xlsx.

   1. **QnAs** (PyR): al exportar PyR, se descargan todos los pares de pregunta y respuesta (con preguntas, respuestas, metadatos, solicitud de seguimiento y los nombres de origen de datos). Los identificadores de QnA que se exportan con las preguntas y respuestas se pueden usar para actualizar un par de pregunta y respuesta concreto mediante la [API de actualización](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). El identificador de QnA de un par de pregunta y respuesta concreto permanece sin cambios en varias operaciones de exportación.
   2. **Synonyms** (Sinónimos): puede exportar sinónimos que se han agregado a la base de conocimiento.
   4. **Knowledge Base Replica** (Réplica de base de conocimiento): si quiere descargar toda la base de conocimiento con sinónimos y otras configuraciones, puede elegir esta opción.

## <a name="import-a-knowledge-base"></a>Importación de una base de conocimiento
1. Haga clic en **Create a knowledge base** (Crear una base de conocimiento) en el menú superior del portal de qnamaker.ai y, después, cree una base de conocimiento _vacía_ sin agregar direcciones URL ni archivos. Establezca el nombre que prefiera para la nueva base de conocimiento y haga clic en  **Create your KB** (Crear la base de conocimiento). 

1. En esta nueva base de conocimiento, abra la pestaña **Settings** (Configuración) y, en _Import knowledge base_ (Importar base de conocimiento), seleccione una de las siguientes opciones: **QnAs** (Preguntas y respuestas), **Synonyms** (Sinónimos) o **Knowledge Base Replica** (Réplica de base de conocimiento). 

   1. **QnAs** (PyR): esta opción importa todos los pares de pregunta y respuesta. **Los pares de pregunta y respuesta creados en la knowledge base nueva tendrán el mismo identificador de QnA ID que el que hay en el archivo exportado**. Puede consultar [SampleQnAs.xlsx](https://aka.ms/qnamaker-sampleqnas), [SampleQnAs.tsv](https://aka.ms/qnamaker-sampleqnastsv) para importar los pares PyR.
   2. **Synonyms** (Sinónimos): esta opción se puede usar para importar sinónimos a la base de conocimiento. Puede consultar [SampleSynonyms.xlsx](https://aka.ms/qnamaker-samplesynonyms), [SampleSynonyms.tsv](https://aka.ms/qnamaker-samplesynonymstsv) para importar sinónimos.
   3. **Knowledge Base Replica** (Réplica de base de conocimiento): esta opción se puede usar para importar una réplica de KB con PyR, Sinónimos y Configuración. Puede consultar [KBReplicaSampleExcel](https://aka.ms/qnamaker-samplereplica), [KBReplicaSampleTSV](https://aka.ms/qnamaker-samplereplicatsv) para obtener más información. Si también quiere agregar contenido no estructurado a la réplica, consulte [CustomQnAKBReplicaSample](https://aka.ms/qnamaker-samplev2replica).

      Al importar la réplica se necesitan PyR o contenido no estructurado. Los documentos no estructurados solo son válidos para Respuesta personalizada a preguntas.
      El archivo de sinónimos no es obligatorio al importar la réplica.
      El archivo de configuración es obligatorio al importar la réplica.

         |Configuración|¿Actualización permitida al importar a KB de QnA Maker?|¿Actualización permitida al importar a KB de Respuesta personalizada a preguntas?|
         |:--|--|--|
         |DefaultAnswerForKB|No|Sí|
         |EnableActiveLearning (True/False)|Sí|No|
         |EnableMultiTurnExtraction (True/False)|Sí|Sí|
         |DefaultAnswerforMultiturn|Sí|Sí|
         |Lenguaje|No|No|

1. **Pruebe** la base de conocimiento nueva mediante el panel de pruebas. Vea cómo [probar la base de conocimiento](../How-To/test-knowledge-base.md).

1. **Publique** la base de conocimiento y cree un bot de chat. Vea cómo [publicar la base de conocimiento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

   > [!div class="mx-imgBorder"]
   > ![Migración de la base de conocimiento](../media/qnamaker-how-to-migrate-kb/import-export-kb.png)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migración de una base de conocimiento desde QnA Maker mediante programación

El proceso de migración está disponible mediante programación con las siguientes API REST:

**Exportarar**

* [Descarga de la API de la base de conocimiento](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importar**

* [API Replace (volver a cargar con el mismo identificador de base de conocimiento)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [API Create (cargar con un identificador de base de conocimiento nuevo)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs"></a>Registros de chat
No hay forma de migrar registros de chat, ya que la base de conocimiento nueva usa Application Insights para almacenar registros de chat.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Editar una base de conocimiento](../How-To/edit-knowledge-base.md)
