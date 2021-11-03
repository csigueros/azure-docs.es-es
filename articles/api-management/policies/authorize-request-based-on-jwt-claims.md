---
title: 'Ejemplo de directiva de API Management: autorización de acceso con notificaciones JWT'
titleSuffix: Azure API Management
description: 'Ejemplo de directiva de Azure API Management: demuestra cómo autorizar el acceso a los métodos HTTP específicos en una API con notificaciones JWT.'
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
ms.openlocfilehash: 72f9659bd93cea4cc0adcc638897ba2109fcc046
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065653"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorización de acceso con notificaciones JWT

Este artículo incluye un ejemplo de directiva de Azure API Management que muestra cómo autorizar el acceso a los métodos HTTP específicos en una API con notificaciones JWT. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](index.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)
