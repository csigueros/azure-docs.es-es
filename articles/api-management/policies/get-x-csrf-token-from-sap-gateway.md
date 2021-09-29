---
title: 'Ejemplo de directiva de Azure API Management: implementación del patrón X-CSRF | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: muestra cómo implementar el patrón X-CSRF que usan numerosas API. Este ejemplo es específico de puerta de enlace de SAP.'
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
ms.openlocfilehash: 0e6bcd315600739df6e9b5ec6df0d5297772fb83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606615"
---
# <a name="implement-x-csrf-pattern"></a>Implementación del patrón de X-CSRF

Este artículo muestra un ejemplo de directiva de Azure API Management que demuestra cómo implementar el patrón X-CSRF que usan numerosas API. Este ejemplo es específico de puerta de enlace de SAP. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-reference.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)