---
title: Archivo de inclusión
description: archivo de inclusión
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 07/13/2021
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7ea469c4d549d968745f08a5eff341bbc650854e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "114200950"
---
- Cada identidad administrada cuenta para el límite de cuota de objetos de un inquilino de Azure AD, como se indica en [Restricciones y límites del servicio Azure AD](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   La velocidad a la que se pueden crear identidades administradas tiene los siguientes límites:

    1. Por inquilino de Azure AD por región de Azure: 400 operaciones de creación en 20 segundos.
    2. Por suscripción de Azure por región de Azure: 80 operaciones de creación en 20 segundos.

-   Velocidad a la que se puede asignar una identidad administrada asignada por el usuario con un recurso de Azure:

    1. Por inquilino de Azure AD por región de Azure: 400 operaciones de asignación en 20 segundos.
    2. Por suscripción de Azure por región de Azure: 300 operaciones de asignación en 20 segundos.




