---
title: Tabla de límites de Azure SignalR Service
description: Describe los límites del sistema para Azure SignalR Service.
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2020
ms.author: zhshang
ms.openlocfilehash: f5b197448529e953d5d7d188b00a88d60e87e900
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454017"
---
| Resource | Límite predeterminado | Límite máximo | 
| --- | --- | --- |
| Unidades de Azure SignalR Service por instancia para el nivel Gratis |1 |1 |
| Unidades de Azure SignalR Service por instancia para el nivel Estándar |100 |100 |
| Unidades de Azure SignalR Service por suscripción por región para el nivel Gratis|5 |5 |
| Recuento total de unidades de Azure SignalR Service por suscripción por región |150 |Sin límite |
| Conexiones por unidad por día del nivel Gratis |20 |20 |
| Conexiones por unidad por día del nivel Estándar |1,000 |1,000|
| Mensajes incluidos por unidad por día del nivel Gratis|20.000 |20.000 |
| Mensajes adicionales por unidad y día en el nivel Gratis|0 |0 |
| Mensajes incluidos por unidad por día del nivel Estándar|1 000 000 |1 000 000 |
| Mensajes adicionales por unidad y día en el nivel Estándar|Sin límite |Sin límite |

Para solicitar una actualización de los límites predeterminados de la suscripción, abra un vale de soporte técnico.

Para más información sobre cómo se cuentan las conexiones y los mensajes, consulte [Mensajes y conexiones de Azure SignalR Service](../articles/azure-signalr/signalr-concept-messages-and-connections.md).

Si sus requisitos superan los límites, cambie del nivel Gratis al nivel Estándar y agregue unidades. Para más información, consulte [Escalado de una instancia de Azure SignalR Service](../articles/azure-signalr/signalr-howto-scale-signalr.md). 

Si los requisitos superan los límites de una sola instancia, agregue instancias. Para más información, consulte [Escalado de Azure SignalR Service con varias instancias](../articles/azure-signalr/signalr-howto-scale-multi-instances.md).
