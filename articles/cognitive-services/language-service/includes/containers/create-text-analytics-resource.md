---
title: Creación de un recurso de Cognitive Services Language
titleSuffix: Azure Cognitive Services
description: Aprenda a crear un recurso de lenguaje de Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d21f76e73e9ef4581355890b56876ff2e71a954
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092950"
---
## <a name="create-a-cognitive-services-language-resource"></a>Creación de un recurso de Cognitive Services Language

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Crear un recurso** y, a continuación, vaya a **AI + Machine Learning** > **Language**.
   O vaya a [Crear un recurso de lenguaje](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Especifique todas las opciones de configuración necesarias:

    |Configuración|Valor|
    |--|--|
    |Nombre|Escriba un nombre (2-64 caracteres).|
    |Suscripción|Seleccione la suscripción adecuada.|
    |Location|Seleccione una ubicación cercana.|
    |Plan de tarifa| Escriba **S**, que corresponde al plan de tarifa estándar.|
    |Resource group|Seleccione un grupo de recursos disponible.|

1. Seleccione **Crear** y espere a que el recurso se cree. El explorador se redirige automáticamente a la página de recursos recién creada.
1. Recopile el elemento `endpoint` configurado y una clave de API:

    |Pestaña de recursos en el portal|Configuración|Value|
    |--|--|--|
    |**Información general**|Punto de conexión|Copie el punto de conexión. Tiene un aspecto similar a `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0`.|
    |**Claves**|Clave de API|Copie una de las dos claves. Es una cadena alfanumérica de 32 caracteres sin espacios ni guiones: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
