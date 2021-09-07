---
title: 'Cliente de ingesta: servicio de Voz'
titleSuffix: Azure Cognitive Services
description: En este artículo se describe una herramienta publicada en GitHub que permite a los clientes insertar archivos de audio en el servicio Voz de forma fácil y rápida.
services: cognitive-services
author: panosperiorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 069c0b587fd3ff69ca8e883850d44b84ab12486f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743576"
---
# <a name="ingestion-client-for-the-speech-service"></a>Cliente de ingesta para el servicio Voz

El Cliente de ingesta es una herramienta publicada en [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/ingestion) que permite a los clientes transcribir archivos de audio a través de servicios de Voz de manera rápida y con poco o ningún esfuerzo de desarrollo. Funciona mediante la conexión de una cuenta de [Azure Storage](https://azure.microsoft.com/product-categories/storage/) dedicada con instancias de [Azure Functions](https://azure.microsoft.com/services/functions/) personalizadas que usan la [API REST](rest-speech-to-text.md) o el [SDK](speech-sdk.md) sin servidor para pasar solicitudes de transcripción al servicio.  

## <a name="architecture"></a>Architecture

La herramienta ayuda a los clientes que quieren hacerse a una idea de la calidad de la transcripción sin realizar inversiones de desarrollo por adelantado. La herramienta conecta algunos recursos para transcribir archivos de audio que llevan al [contenedor de Azure Storage](https://azure.microsoft.com/product-categories/storage/) dedicado.

Internamente, la herramienta usa nuestro SDK o API o de Batch v3.0, y sigue los procedimientos recomendados para controlar el escalado vertical, los reintentos y la conmutación por error. En el esquema siguiente se describen los recursos y las conexiones.

:::image type="content" source="media/ingestion-client/architecture-1.png" alt-text="Arquitectura del Cliente de ingesta.":::

En la [Guía de introducción al Cliente de ingesta](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md) se describe cómo configurar y usar la herramienta.

> [!IMPORTANT]
> Los precios varían en función del modo de operación (por lotes frente a tiempo real), así como de la SKU de Azure Functions seleccionada. De manera predeterminada, la herramienta creará una SKU Premium de Azure Functions para controlar grandes volúmenes. Visite la página de [precios](https://azure.microsoft.com/pricing/details/functions/) para más información.

Tanto el [SDK de Voz](speech-sdk.md) de Microsoft como la [API REST de conversión de voz en texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) se pueden usar para obtener transcripciones. La decisión afecta a los costos generales, tal como se explica en la guía. 

> [!TIP]
> Puede usar la herramienta y la solución resultante en producción para procesar un gran volumen de audio.

## <a name="tool-customization"></a>Personalización de la herramienta

La herramienta se creó para mostrar los resultados de los clientes rápidamente. Puede personalizar la herramienta para las SKU y la configuración de su preferencia. Las SKU se pueden editar desde [Azure Portal](https://portal.azure.com) y [el propio código está disponible en GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch).

> [!NOTE]
> Se recomienda crear los recursos en el mismo grupo de recursos dedicado para comprender y mantener un seguimiento de los costos más fácilmente.

## <a name="next-steps"></a>Pasos siguientes

* [Obtener la suscripción de evaluación gratuita del servicio de voz](https://azure.microsoft.com/try/cognitive-services/)
* [Más información sobre el SDK de Voz](./speech-sdk.md).
* [Más información sobre la herramienta de la CLI de Voz](./spx-overview.md)
