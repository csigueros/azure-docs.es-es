---
title: Procedimientos recomendados para hospedar una aplicación cliente de Kubernetes
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo hospedar una aplicación cliente de Kubernetes que usa Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 33628fc16dfc2693cad0f8cd13f555cc10e55f25
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808742"
---
# <a name="kubernetes-hosted-client-application"></a>Aplicaciones cliente hospedadas por Kubernetes

## <a name="client-connections-from-multiple-pods"></a>Conexiones de cliente desde varios pods

Si tiene varios pods que se conectan a un servidor de Redis, asegúrese de que las nuevas conexiones de los pods se creen de manera escalonada. Si se inician varios pods en poco tiempo sin escalonarse, se produce un pico repentino en el número de conexiones de cliente creadas. El alto número de conexiones conduce a una carga elevada en el servidor de Redis y puede provocar tiempos de espera.

Evite el mismo escenario al apagar varios pods al mismo tiempo. Si no se escalona el apagado, podría producirse una caída fuerte en el número de conexiones, lo que podría provocar presión en la CPU.

## <a name="sufficient-pod-resources"></a>Recursos de pod suficientes

Asegúrese de que el pod que ejecuta la aplicación cliente tenga suficientes recursos de CPU y memoria. Si la aplicación cliente se ejecuta cerca de sus límites de recursos, puede dar lugar a tiempos de espera.

## <a name="sufficient-node-resources"></a>Recursos de nodo suficientes

Un pod que ejecuta la aplicación cliente puede verse afectado por otros pods que se ejecutan en el mismo nodo y limitan las conexiones de Redis o las operaciones de E/S. Por lo tanto, asegúrese siempre de que el nodo en el que se ejecutan los pods de la aplicación cliente tenga suficiente memoria, CPU y ancho de banda de red. Si se agota cualquiera de estos recursos, se podrían producir problemas de conectividad.

## <a name="linux-hosted-client-applications-and-tcp-settings"></a>Aplicaciones cliente hospedadas en Linux y configuración de TCP

Si la aplicación cliente de Azure Cache for Redis se ejecuta en un contenedor basado en Linux, se recomienda actualizar algunas configuraciones de TCP tal y como se detalla en [Configuración de TCP para aplicaciones cliente hospedadas en Linux](cache-best-practices-connection.md#tcp-settings-for-linux-hosted-client-applications).

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo](cache-best-practices-development.md)
- [Preguntas frecuentes sobre el desarrollo para Azure Cache for Redis](cache-development-faq.yml)
