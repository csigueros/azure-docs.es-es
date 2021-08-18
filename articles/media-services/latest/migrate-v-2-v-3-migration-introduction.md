---
title: Introducción a la migración de Media Services v2 a v3
description: Este artículo es una introducción a la migración de Media Services v2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 49d804fdf354dc942ca256e1b5960fd201358a81
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114708177"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Introducción a la migración de Media Services v2 a v3

![logotipo de la guía de migración](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

La guía de migración de Media Services le ayuda a migrar de las API de Media Services v2 a las API de v3 basadas en una migración que aprovecha las nuevas características y funciones que ya están disponibles. Debe usar su criterio y determinar lo que mejor se adapte a su escenario.

[!INCLUDE [v2 deprecation notice](./includes/v2-deprecation-notice.md)]

## <a name="how-to-use-this-guide"></a>Cómo usar esta guía

### <a name="navigating"></a>Navegación

A lo largo de la guía, verá el siguiente gráfico.

![pasos de migración](./media/migration-guide/steps.svg)<br/>

El paso en el que se encuentre se indicará con un cambio de color en el número del paso de la siguiente manera:

![pasos de migración 2](./media/migration-guide/steps-2.svg)<br/>

Al final de cada página, verá vínculos al resto de los documentos de migración que podrá leer debajo del encabezado **Pasos siguientes**.

### <a name="guidance"></a>Guía

Las instrucciones que se proporcionan aquí son *generales*. Incluye contenido para mejorar su comprensión de lo que está disponible en este momento en v3 y lo que ha cambiado en los flujos de trabajo de Media Services.

Para obtener instrucciones más detalladas, como capturas de pantallas y gráficos conceptuales, hay vínculos a conceptos, tutoriales, guías de inicio rápido, ejemplos y referencias de API en cada tema basado en escenarios. También se incluyen ejemplos que le ayudarán a comparar la API v2 con la API v3.

## <a name="migration-guidance-overview"></a>Información general de la guía de migración

Durante la migración, se deben seguir cuatro pasos generales:

## <a name="step-1-benefits"></a>Paso 1: Ventajas

<hr color="#5ea0ef" size="10">

[Obtenga información sobre las ventajas](migrate-v-2-v-3-migration-benefits.md) de migrar a la API de Media Services v3.

## <a name="step-2-differences"></a>Paso 2: Diferencias

<hr color="#5ea0ef" size="10">

Comprenda las diferencias entre la API de Media Services v2 y la API v3.

- [Acceso de API](migrate-v-2-v-3-differences-api-access.md)
- [Intervalos de características](migrate-v-2-v-3-differences-feature-gaps.md)
- [Cambios de terminología y de entidad](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Paso 3: Configuración del SDK

<hr color="#5ea0ef" size="10">

Obtenga información sobre las diferencias del SDK y [prepárese para migrar a la API REST v3 o al SDK de cliente](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Paso 4: Guía basada en escenarios

<hr color="#5ea0ef" size="10">

La aplicación de Media Services v2 puede ser única. Por lo tanto, hemos proporcionado instrucciones basadas en escenarios en función de cómo *podría haber usado* Media Services en el pasado y los pasos para cada una de las características del servicio, como:

- [Encoding](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Streaming en directo](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Empaquetado y entrega](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Protección de contenido](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Unidades reservadas de multimedia (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)
