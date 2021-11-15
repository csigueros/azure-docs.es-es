---
title: Regiones admitidas de Service Connector
description: Disponibilidad de regiones de Service Connector y lista de regiones admitidas
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021, references_regions
ms.openlocfilehash: 75678da43f400eb3ee56f956dcd307207b501e91
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853061"
---
# <a name="service-connector-region-support"></a>Regiones admitidas de Service Connector

Cuando se crea una conexión de servicio con Service Connector, se aprovisiona el recurso de conexión conceptual en la misma región con la instancia de servicio de proceso de forma predeterminada. En esta página se muestra la información de regiones admitidas y el comportamiento correspondiente de la versión preliminar pública de Service Connector.

## <a name="supported-regions-with-regional-endpoint"></a>Regiones admitidas con punto de conexión regional

Si la instancia del servicio de proceso se encuentra en una de las regiones que admite Service Connector siguientes, puede usar Service Connector para crear y administrar conexiones de servicio.

- Centro-Oeste de EE. UU.
- Oeste de Europa
- Norte de Europa
- Este de EE. UU.
- Oeste de EE. UU. 2

## <a name="supported-regions-with-geographical-endpoint"></a>Regiones admitidas con punto de conexión geográfico

Es posible que la instancia del servicio de proceso se cree en la región en la que Service Connector tiene regiones geográficas admitidas. Esto significa que la conexión de servicio se creará en una región diferente de la instancia de proceso. Verá un banner sobre esta información al crear una conexión de servicio. La diferencia de región puede afectar al cumplimiento, la residencia de datos y la latencia de los datos.

- Este de EE. UU. 2
- Oeste de EE. UU. 3
- Centro-sur de EE. UU.

## <a name="not-supported-regions-in-public-preview"></a>Regiones no admitidas en versión preliminar pública

Todavía puede ver el comando de la CLI de Service Connector o el nodo del portal en la región que admite Service Connector. Pero no puede crear ni administrar conexiones de servicio en estas regiones. El equipo del producto está trabajando activamente para admitir más regiones.
