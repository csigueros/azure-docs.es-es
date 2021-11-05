---
title: 'Análisis de las bases de conocimiento: respuesta a preguntas personalizada'
titleSuffix: Azure Cognitive Services
description: La respuesta a preguntas personalizada usa el registro de diagnóstico de Azure para almacenar los datos de telemetría y los registros de chat.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: fba1481ffe7aa45514e53813ccf7b14ea01bd6bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093331"
---
# <a name="get-analytics-for-your-knowledge-base"></a>Obtención de análisis de la base de conocimiento

La respuesta a preguntas personalizada usa el registro de diagnóstico de Azure para almacenar los datos de telemetría y los registros de chat. Siga estos pasos para ejecutar consultas de ejemplo para obtener análisis sobre el uso del proyecto de respuesta de preguntas personalizada.

1. [Habilite el registro de diagnóstico](../../../diagnostic-logging.md) para el recurso de idioma con la respuesta a preguntas personalizada habilitada.

2. En el paso anterior, seleccione **Trace** (Seguimiento), además de **Audit, RequestResponse y AllMetrics** para el registro

    ![Habilitación del registro de seguimiento en la respuesta a preguntas personalizada](../media/analytics/qnamaker-v2-enable-trace-logging.png)

## <a name="kusto-queries"></a>Consultas de Kusto

### <a name="chat-log"></a>Registro de chat

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>Recuento de tráfico por knowledge base y usuario en un período

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>Latencia de GenerateAnswer API

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>Latencia media de todas las operaciones

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>Preguntas sin responder

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Selección de la capacidad](../../../qnamaker/how-to/improve-knowledge-base.md)
