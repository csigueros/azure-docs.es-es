---
title: Regiones admitidas de Service Connector
description: Disponibilidad de regiones de Service Connector y lista de regiones admitidas
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33eefc9a36fcd6e230d95e962da344d7ad738d7d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090781"
---
# <a name="service-connector-region-support"></a>Regiones admitidas de Service Connector

Cuando se crea una conexión de servicio con Service Connector, se aprovisiona el recurso de conexión conceptual en la misma región con la instancia de servicio de proceso de forma predeterminada. En esta página se muestra la información de regiones admitidas y el comportamiento correspondiente de la versión preliminar pública de Service Connector.

## <a name="supported-regions-with-regional-endpoint"></a>Regiones admitidas con punto de conexión regional

Puede encontrar la lista de regiones admitidas más reciente en[Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=service-connector). Si la instancia del servicio de proceso se encuentra en una de las regiones que admite Service Connector, puede usar Service Connector para crear y administrar conexiones de servicio.

## <a name="supported-regions-with-geographical-endpoint"></a>Regiones admitidas con punto de conexión geográfico

Es posible que la instancia del servicio de proceso se cree en la región en la que Service Connector tiene regiones geográficas admitidas. Esto significa que la conexión de servicio se creará en una región diferente de la instancia de proceso. Verá un banner sobre esta información al crear una conexión de servicio. La diferencia de región puede afectar al cumplimiento, la residencia de datos y la latencia de los datos.

## <a name="not-supported-regions-in-public-preview"></a>Regiones no admitidas en versión preliminar pública

Todavía puede ver el comando de la CLI de Service Connector o el nodo del portal en la región que admite Service Connector. Pero no puede crear ni administrar conexiones de servicio en estas regiones. El equipo del producto está trabajando activamente para admitir más regiones.
