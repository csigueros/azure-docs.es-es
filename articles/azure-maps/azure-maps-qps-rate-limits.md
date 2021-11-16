---
title: Límites de frecuencia de QPS de Azure Maps
description: Limitación del número de consultas por segundo en Azure Maps.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/15/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 0015eeccd2ea4ed12eb0435cac72db8e86f9119a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846452"
---
# <a name="azure-maps-qps-rate-limits"></a>Límites de frecuencia de QPS de Azure Maps

Azure Maps no tiene límites diarios máximos para el número de solicitudes que se pueden realizar, pero sí hay límites para el número máximo de consultas por segundo (QPS).

A continuación, se muestran los límites de uso de QPS para cada servicio de Azure Maps según el plan de tarifa.

| Servicio de Azure Maps | Límite de QPS: plan de tarifa Gen 2 | Límite de QPS: plan de tarifa Gen 1 S1 | Límite de QPS: plan de tarifa Gen 1 S0 |
|  ----------------- |  :--------------------------: | :------------------------------: | :------------------------: |
| Servicio de derechos de autor | 10 | 10 | 10 |
| Creador: alias, TilesetDetails | 10 | No disponible | No disponible |
| Creador: conversión, conjunto de datos, estado de características, WFS | 50 | No disponible | No disponible |
| Data Service | 50 | 50 | 50 |
| Servicio Elevation | 50 | 50 | 50 |
| Servicio de geolocalización | 50 | 50 | 50 |
| Servicio Render: iconos de contorno, iconos de DEM, iconos de elevación, iconos de cliente, iconos de tráfico y mapas estáticos | 50 | 50 | 50 |
| Servicio Render: iconos de carretera | 500 | 500 | 50 |
| Servicio Render: iconos de satélite | 250 | 250 | 50 |
| Servicio Render: iconos meteorológicos | 100 | 100 | 50 |
| Servicio Route: lote | 10 | 10 | 10 |
| Servicio Route: sin lote | 50 | 50 | 50 |
| Servicio Search: lote | 10 | 10 | 10 |
| Servicio Search: sin lote | 500 | 500 | 50 |
| Servicio Search: inverso sin lote | 250 | 250 | 50 |
| Servicio espacial | 50 | 50 | 50 |
| Servicio de zona horaria | 50 | 50 | 50 |
| Servicio Traffic | 50 | 50 | 50 |
| Servicio meteorológico | 50 | 50 | 50 |

Cuando se alcancen los límites de QPS, se devolverá un error HTTP 429. Si usa los planes de tarifa Gen 2 o Gen 1 S1, puede crear una solicitud de soporte *técnico* de Azure Maps en [Azure Portal](https://ms.portal.azure.com/) para aumentar un límite de QPS específico si es necesario. No se pueden aumentar los límites de QPS en el plan de tarifa Gen 1 S0.
