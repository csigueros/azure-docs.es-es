---
title: Archivo de inclusión
description: archivo de inclusión
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: e9ba8efefd2238b1aadc2fbeaf17a286e0714bcd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484142"
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
| Definición de iniciativa | Parámetros | 300 |
| Asignaciones de iniciativas o directivas | Exclusiones (notScopes) | 400 |
| Regla de directiva | Condicionales anidados | 512 |
| Tarea de corrección | Recursos | 500 |
