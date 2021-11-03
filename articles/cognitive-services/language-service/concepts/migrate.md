---
title: 'Migración a Azure Cognitive Service for Language desde: LUIS, QnA Maker y Text Analytics'
titleSuffix: Azure Cognitive Services
description: Use este artículo para saber si debe migrar las aplicaciones de LUIS, QnA Maker y Text Analytics.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1e2a2945700e0537b7ae4b220f97eafb1ce19a69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090828"
---
# <a name="migrating-to-azure-cognitive-service-for-language"></a>Migración a Azure Cognitive Service for Language

El 2 de noviembre de 2021, Azure Cognitive Service for Language se publicó en versión preliminar. Este servicio de lenguaje unifica las ofertas de servicio de Text Analytics, QnA Maker y LUIS, además de proporcionar varias características nuevas. 

## <a name="do-i-need-to-migrate-to-the-language-service-if-i-am-using-text-analytics"></a>¿Debo migrar al servicio de lenguaje si uso Text Analytics?

Text Analytics se ha incorporado al servicio de lenguaje y sus características siguen estando disponibles. Si usaba Text Analytics, las aplicaciones deberían seguir funcionando sin cambios importantes. También puede ver la [guía de migración de Text Analytics v2](migrate-from-text-analytics-v2.md) si necesita actualizar una aplicación anterior. 

Considere la posibilidad de usar uno de los artículos de inicio rápido disponibles para ver la información más reciente sobre los puntos de conexión de servicio y las llamadas API. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-luis"></a>¿Cómo migro al servicio de lenguaje si uso LUIS?

Si usa Language Understanding (LUIS), puede [importar el archivo JSON de LUIS](../conversational-language-understanding/concepts/backwards-compatibility.md) a la nueva característica Conversational Language Understanding. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-qna-maker"></a>¿Cómo migro al servicio de lenguaje si uso QnA Maker?

Si usa QnA Maker, consulte la [guía de migración](../question-answering/how-to/migrate-qnamaker.md) para obtener información sobre la migración de knowledge bases de QnA Maker a la respuesta a preguntas.

## <a name="see-also"></a>Consulte también

* [Información general sobre Azure Cognitive Service for Language](../overview.md)
* [Información general sobre el reconocimiento del lenguaje de conversación](../conversational-language-understanding/overview.md)
* [Información general sobre la respuesta a preguntas](../question-answering/overview.md)
