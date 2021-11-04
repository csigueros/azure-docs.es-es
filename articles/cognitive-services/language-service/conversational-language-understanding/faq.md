---
title: Preguntas más frecuentes
titleSuffix: Azure Cognitive Services
description: Use este artículo para obtener rápidamente las respuestas a las preguntas más frecuentes sobre el reconocimiento del lenguaje de conversación
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e699beb4bae6c31c73e2e5eebc508306deeb262
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091316"
---
# <a name="frequently-asked-questions-for-conversational-language-understanding"></a>Preguntas más frecuentes sobre el reconocimiento del lenguaje de conversación

Use este artículo para obtener rápidamente las respuestas a preguntas comunes sobre el reconocimiento del lenguaje de conversación

## <a name="how-do-i-create-a-project"></a>¿Cómo se crea un proyecto?

Consulte el [inicio rápido](./quickstart.md) para crear rápidamente su primer proyecto o el [artículo de tutoriales](./how-to/create-project.md) para obtener más detalles. 

## <a name="how-do-i-connect-other-service-applications-in-orchestration-workflow-projects"></a>¿Cómo conectar otras aplicaciones de servicio en proyectos de flujo de trabajo de orquestación?

Consulte el [artículo Esquema de compilación](./how-to/build-schema.md#build-project-schema-for-orchestration-workflow-projects) para obtener información sobre cómo conectar otro proyecto como una intención.

## <a name="which-luis-applications-can-i-connect-to-in-orchestration-workflow-projects"></a>¿A qué aplicaciones de LUIS puedo conectarme en proyectos de flujo de trabajo de orquestación?

Las aplicaciones de LUIS que usan el recurso de lenguaje como recurso de creación estarán disponibles para la conexión. Solo puede conectarse a aplicaciones de LUIS que pertenecen al mismo recurso. Esta opción solo estará disponible para los recursos de Oeste de Europa, ya que es la única región disponible común entre LUIS y CLU. Para obtener más información consulte [límites de región](./service-limits.md#region-limits). 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>El entrenamiento está tardando mucho tiempo, ¿es esto lo esperado?

Para los proyectos de conversación, se esperan tiempos de entrenamiento largos. En función del número de ejemplos que tenga, los tiempos de entrenamiento pueden variar de 5 minutos a 1 hora o más. 

## <a name="can-i-add-entities-to-orchestration-workflow-projects"></a>¿Puedo agregar entidades a proyectos de flujo de trabajo de orquestación?

No. Los proyectos de orquestación solo están habilitados para las intenciones que se pueden conectar a otros proyectos para el enrutamiento. 

## <a name="how-do-i-use-entity-components"></a>¿Cómo usar componentes de entidad?

Consulte el artículo sobre [componentes de entidad](./concepts/entity-components.md).

## <a name="which-languages-are-supported-in-this-feature"></a>¿Qué idiomas se admiten en esta característica?

Consulte el artículo sobre [compatibilidad de lenguaje](./language-support.md).

## <a name="how-do-i-get-more-accurate-results-for-my-project"></a>¿Cómo obtener resultados más precisos para mi proyecto?

Consulte las [guías recomendadas](./how-to/build-schema.md#guidelines-and-recommendations) para obtener información sobre cómo mejorar la precisión.

## <a name="how-do-i-get-predictions-in-different-languages"></a>¿Cómo obtener predicciones en distintos idiomas?

Al entrenar e implementar un proyecto de conversación en cualquier idioma, puede intentar consultarlo inmediatamente en [varios idiomas](./concepts/multiple-languages.md). Puede obtener resultados variados para distintos idiomas. Para mejorar la precisión de cualquier idioma, agregue expresiones al proyecto en ese idioma para introducir el modelo entrenado a más sintaxis de ese idioma.

## <a name="how-many-intents-entities-utterances-can-i-add-to-a-project"></a>¿Cuántas intenciones, entidades y expresiones puedo agregar a un proyecto?

Consulte el artículo sobre [límites de servicio](./service-limits.md). 

## <a name="can-i-label-the-same-word-as-2-different-entities"></a>¿Puedo etiquetar la misma palabra que dos entidades diferentes?

A diferencia de LUIS, no se puede etiquetar el mismo texto que dos entidades diferentes. Los componentes aprendidos en distintas entidades son mutuamente excluyentes y solo se predice un intervalo aprendido para cada conjunto de caracteres.

## <a name="can-i-import-a-luis-json-file-into-conversational-language-understanding"></a>¿Puedo importar un archivo JSON de LUIS al reconocimiento del lenguaje de conversación?

Sí, puede [importar cualquier archivo JSON de aplicación de LUIS](./concepts/backwards-compatibility.md) desde la versión más reciente del servicio.

## <a name="can-i-import-a-luis-lu-file-into-conversational-language-understanding"></a>¿Puedo importar un archivo `.LU` de LUIS al reconocimiento del lenguaje de conversación?

No, el servicio solo admite el formato JSON. Puede ir a LUIS, importar el archivo `.LU` y exportarlo como un archivo JSON. 

## <a name="is-there-any-sdk-support"></a>¿Hay compatibilidad con el SDK?

Sí, solo para las predicciones y las [muestras están disponibles](https://aka.ms/cluSampleCode). Actualmente no hay compatibilidad con la creación para el SDK.

## <a name="are-there-apis-for-this-feature"></a>¿Hay API para esta característica?

Sí, todas las API [están disponibles](https://aka.ms/clu-apis).

## <a name="next-steps"></a>Pasos siguientes

[Información general sobre el reconocimiento del lenguaje de conversación](overview.md)
