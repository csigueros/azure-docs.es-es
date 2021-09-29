---
title: 'Directiva de ejemplo de API Management: filtrado activado en la dirección IP al usar Application Gateway'
titleSuffix: Azure API Management
description: 'Directiva de ejemplo de Azure API Management: muestra cómo activar el filtro de solicitudes de direcciones IP al usar Application Gateway.'
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 0308d8ee4b2b23c578a804c981613338d682a9ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590646"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Activación del filtro de solicitudes de direcciones IP al usar Application Gateway

Este artículo incluye una directiva de ejemplo de Azure API Management que muestra cómo activar el filtro de la solicitud de dirección IP cuando se accede a la instancia de API Management mediante Application Gateway u otro intermediario. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-reference.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de restricciones de acceso](../api-management-access-restriction-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)