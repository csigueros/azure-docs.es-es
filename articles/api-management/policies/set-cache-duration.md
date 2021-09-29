---
title: 'Ejemplo de directiva de API Management: establecimiento de la duración en caché de las respuestas'
titleSuffix: Azure API Management
description: 'Ejemplo de directiva de Azure API Management: muestra cómo establecer la duración en caché de las respuestas con el valor maxAge en el encabezado de control de caché que envía el back-end.'
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
ms.openlocfilehash: 512310da23b4157bfcd732fdf429bdc2243d630c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651966"
---
# <a name="set-response-cache-duration"></a>Establecimiento de la duración en caché de las respuestas

Este artículo incluye un ejemplo de directiva de Azure API Management que muestra cómo establecer la duración en caché de las respuestas con el valor maxAge en el encabezado de control de caché que envía el back-end. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-reference.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)