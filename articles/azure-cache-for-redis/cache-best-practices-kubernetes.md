---
title: Procedimientos recomendados para hospedar una aplicación cliente de Kubernetes
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo hospedar una aplicación cliente de Kubernetes que usa Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 5d61b7047262c86471dba988bd0f80cb4fdac8ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663637"
---
# <a name="kubernetes-hosted-client-application"></a>Aplicaciones cliente hospedadas por Kubernetes

## <a name="multiple-pods"></a>Varios pods

Si tiene varios pods que se conectan a un servidor de Redis, asegúrese de que las nuevas conexiones de los pods se crean de manera escalonada. Si se inician varios pods en poco tiempo sin escalonarse, se produce un pico repentino en el número de conexiones de cliente creadas. El alto número de conexiones conduce a una carga elevada en el servidor de Redis y puede provocar tiempos de espera.

Evite el mismo escenario al apagar varios pods al mismo tiempo. Si no se escalona el apagado, podría producirse una caída fuerte en el número de conexiones, lo que podría provocar presión en la CPU.

## <a name="sufficient-resources"></a>Recursos suficientes

Asegúrese de que el nodo de Kubernetes que hospeda el pod que se conecta al servidor de Redis tiene suficiente memoria, CPU y ancho de banda de red.  

## <a name="noisy-neighbor-problem"></a>Problema de vecino ruidoso

Tenga en cuenta el problema del *vecino ruidoso*. Un pod que ejecuta el cliente puede verse afectado por otros pods que se ejecutan en el mismo nodo y limitan las conexiones de Redis o las operaciones de E/S.

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo](cache-best-practices-development.md)
- [Preguntas frecuentes sobre el desarrollo para Azure Cache for Redis](cache-development-faq.yml)
