---
title: 'Muestra de directiva de API Management: envío de información contextual al servicio de back-end'
titleSuffix: Azure API Management
description: 'Ejemplo de directiva de Azure API Management: demuestra cómo enviar información contextual de la solicitud al servicio back-end.'
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 2397126153acaab008d581146234cd402c8e2294
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639848"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Envío de información contextual de la solicitud al servicio de back-end

Este artículo incluye un ejemplo de directiva de Azure API Management que muestra cómo enviar información contextual de la solicitud al servicio back-end. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-reference.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)