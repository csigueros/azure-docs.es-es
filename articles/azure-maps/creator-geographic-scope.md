---
title: Ámbito geográfico del servicio Creator de Azure Maps
description: Obtenga información sobre las asignaciones geográficas del servicio Creator de Azure Maps en Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 2a9a6755e7c9785df8d4874fb240996b0655a8b3
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026828"
---
# <a name="creator-service-geographic-scope"></a>Ámbito geográfico del servicio Creator

Creator de Azure Maps es un servicio con ámbito geográfico. Creator ofrece una API de proveedor de recursos que, dada una región de Azure, crea una instancia de datos de Creator implementada en el nivel geográfico. La asignación de una región de Azure a una ubicación geográfica se produce en segundo plano, como se describe en la tabla siguiente. Para más información sobre las regiones y zonas geográficas de Azure, consulte [Zonas geográficas de Azure](https://azure.microsoft.com/global-infrastructure/geographies).

## <a name="data-locations"></a>Ubicaciones de datos

Para la recuperación ante desastres y la alta disponibilidad, Microsoft puede replicar los datos de los clientes en otras regiones solo dentro de la misma área geográfica. Por ejemplo, los datos de la región Oeste de Europa se pueden replicar en la región Norte de Europa, pero no en Estados Unidos.  Independientemente de la ubicación geográfica que haya seleccionado el cliente, Microsoft no controla ni limita las ubicaciones desde las que los clientes, o sus usuarios finales, pueden acceder a los datos del cliente a través de la API de Azure Maps.  

## <a name="geographic-and-regional-mapping"></a>Asignación geográfica y regional

En la tabla siguiente se describe la asignación entre la geografía y las regiones de Azure admitidas, y el punto de conexión de API geográfico correspondiente. Por ejemplo, si se aprovisiona una cuenta de Creator en la región Oeste de EE. UU. 2 que se encuentra dentro de la geográfica de Estados Unidos, todas las llamadas API al servicio de conversión deben realizarse en `us.atlas.microsoft.com/conversion/convert`.


| Áreas geográficas de Azure (geoáreas) | Centros de datos de Azure (regiones) | Punto de conexión geográfico de API |
|------------------------|----------------------|-------------|
| Europa| Oeste de Europa y Norte de Europa | eu.atlas.microsoft.com |
|Estados Unidos | Oeste de EE. UU. 2, Este de EE. UU. 2 | us.atlas.microsoft.com |
