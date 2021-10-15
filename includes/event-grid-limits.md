---
title: Archivo de inclusión
description: archivo de inclusión
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/07/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0afa9814a7fb6b726d07fe5f1a5b2c863df72e2e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778901"
---
Los siguientes límites se aplican a los **temas** de Azure Event Grid (temas del sistema, personalizados y de asociados). 

> [!NOTE]
> Estos límites son por región. 

| Resource | Límite |
| --- | --- |
| Temas personalizados por suscripción de Azure | 100 |
| Suscripciones de eventos por tema | 500 |
| Velocidad de publicación de un tema personalizado o de asociado (entrada) | 5000 eventos por segundo o 5 MB por segundo (lo que se cumpla primero) |
| Tamaño del evento | 1 MB  |
| Conexiones de punto de conexión privado por tema  | 64 | 
| Reglas de firewall de IP por tema | 16 | 

Los límites siguientes se aplican a los **dominios** de Azure Event Grid. 

| Resource | Límite |
| --- | --- |
| Temas por dominio de eventos | 100 000 |
| Suscripciones de eventos por tema dentro de un dominio | 500 |
| Suscripciones de eventos del ámbito de dominio | 50 |
| Tasa de publicación para un dominio de eventos (entrada) | 5000 eventos por segundo o 5 MB por segundo (lo que se cumpla primero) |
| Dominios de eventos por suscripción de Azure | 100 |
| Conexiones de punto de conexión privado por dominio | 64 | 
| Reglas de firewall de IP por dominio | 16 | 


