---
title: Casos de uso de dominios de eventos en Azure Event Grid
description: En este artículo se describen algunos casos de uso de los dominios de eventos en Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 24a338717b44567bad0ec1575d98ddaeada71113
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413628"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Casos de uso de dominios de eventos en Azure Event Grid
En este artículo se describen algunos casos de uso de los dominios de eventos en Azure Event Grid. 

## <a name="use-case-1"></a>Caso de uso 1 
[!INCLUDE [event-grid-domain-example-use-case.md](./includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Caso de uso 2
Cuando se usan temas del sistema, hay un límite de 500 suscripciones a eventos. Si necesita más de 500 suscripciones a eventos de un tema del sistema, puede usar dominios. 

Supongamos que ha creado un tema del sistema para una instancia de Azure Blob Storage y necesita crear más de 500 suscripciones al tema; no podría hacerlo debido a la limitación (500 suscripciones por tema). En este caso, podría optar por la siguiente solución, que utiliza un dominio de eventos. 

1. Cree un dominio, que puede admitir hasta 100 000 temas. Cada tema de dominio puede tener 500 suscripciones a eventos. Por tanto, este modelo proporciona 500 * 100 000 suscripciones a eventos. 
1. Cree una suscripción a una función de Azure para el tema de sistema de Azure Storage. Cuando la función recibe eventos de Azure Storage, puede enriquecerlos y publicarlos en un tema de dominio adecuado. Por ejemplo, la función podría analizar el nombre del archivo de blob para determinar el tema de dominio de destino y publicar el evento en ese tema. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Dominios de Azure Event Grid: caso de uso 2":::


## <a name="next-steps"></a>Pasos siguientes
Para información acerca de cómo configurar dominios de eventos, crear temas, crear suscripciones a eventos y publicar eventos, consulte [Administración de dominios de eventos](./how-to-event-domains.md).
