---
title: Estadísticas de latencia de ida y vuelta de red de Azure | Microsoft Docs
description: Obtenga información sobre las estadísticas de latencia de ida y vuelta entre regiones de Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 06/08/2021
ms.author: kumud
ms.openlocfilehash: e86f0e38c71265761e0faddbbf1cc783557cd5df
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112297773"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Estadísticas de latencia de ida y vuelta de red de Azure

Azure supervisa continuamente la latencia (velocidad) de las áreas principales de su red mediante herramientas de supervisión internas, así como las mediciones recopiladas por [ThousandEyes](https://thousandeyes.com), un servicio de supervisión sintética de terceros.

## <a name="how-are-the-measurements-collected"></a>¿Cómo se recopilan las medidas?

Las medidas de latencia se recopilan de agentes de ThousandEyes hospedados en regiones en la nube de Azure de todo el mundo, que envían continuamente sondeos de red entre ellos, en intervalos de 1 minuto. Las estadísticas de latencia mensuales se derivan de calcular el promedio de las muestras recopiladas del mes.

## <a name="may-2021-round-trip-latency-figures"></a>Cifras de latencia de recorrido de ida y vuelta de mayo de 2021

Aquí se muestra el promedio mensual de los tiempos de ida y vuelta entre las regiones de Azure durante los últimos 31 días (que terminan el 31 de mayo de 2021). Las siguientes medidas cuentan con la tecnología de [ThousandEyes](https://thousandeyes.com).

[![Estadísticas de latencia interregionales de Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las [regiones de Azure](https://azure.microsoft.com/global-infrastructure/regions/).
