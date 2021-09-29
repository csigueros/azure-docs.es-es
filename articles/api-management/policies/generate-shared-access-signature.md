---
title: 'Ejemplo de directiva de API Management: generación de una firma de acceso compartido'
titleSuffix: Azure API Management
description: 'Ejemplo de directiva de Azure API Management: muestra cómo generar una firma de acceso compartido mediante expresiones y cómo reenviar la solicitud a Azure Storage con la directiva de reescritura del identificador URI.'
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
ms.openlocfilehash: 80f0260da56db61205122cde05e2e8be24250187
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654392"
---
# <a name="generate-shared-access-signature"></a>Generación de una firma de acceso compartido

Este artículo muestra un ejemplo de directiva de Azure API Management: que muestra cómo generar una [firma de acceso compartido](../../storage/common/storage-sas-overview.md) mediante expresiones y reenviar la solicitud a Azure Storage con la directiva de reescritura del identificador URI. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-reference.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-reference.md)