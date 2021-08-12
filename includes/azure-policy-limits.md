---
title: Archivo de inclusión
description: archivo de inclusión
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: 4792245dff3784d1aa72120c6be1412bf6659c35
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113027461"
---
Hay un número máximo de cada tipo de objeto de Azure Policy. Para las definiciones, una entrada de _Scope_ significa el [grupo de administración](../articles/governance/management-groups/overview.md) o la suscripción.
En el caso de las asignaciones y exenciones, una entrada de _Scope_ significa el [grupo de administración](../articles/governance/management-groups/overview.md), la suscripción, el grupo de recursos o el recurso individual.

| Where | Qué | Número máximo |
|---|---|---|
| Ámbito | Definiciones de directiva | 500 |
| Ámbito | Definiciones de iniciativa | 200 |
| Inquilino | Definiciones de iniciativa | 2,500 |
| Ámbito | Asignaciones de iniciativas o directivas | 200 |
| Ámbito | Exenciones | 1000 |
| Definición de directiva | Parámetros | 20 |
| Definición de iniciativa | Directivas | 1000 |
| Definición de iniciativa | Parámetros | 250 |
| Asignaciones de iniciativas o directivas | Exclusiones (notScopes) | 400 |
| Regla de directiva | Condicionales anidados | 512 |
| Tarea de corrección | Recursos | 500 |
