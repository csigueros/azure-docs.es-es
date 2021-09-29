---
title: 'Ejemplo de directiva de Azure API Management: filtro de contenido de respuestas | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: demuestra cómo filtrar los elementos de datos de la carga de respuesta según el producto asociado a la solicitud.'
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
ms.openlocfilehash: 4bcd9f9db9af45149028707ba2c055b29e2bb420
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590627"
---
# <a name="filter-response-content"></a>Filtro de contenido de respuestas

Este artículo muestra un ejemplo de directiva de Azure API Management que demuestra cómo filtrar los elementos de datos de la carga de respuesta según el producto asociado a la solicitud. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-reference.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **salida**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)