---
title: Pasos comunes para la migración de cliente público a MSAL
description: Archivo de inclusión que explica los pasos comunes que debe seguir para todas las aplicaciones cliente públicas en lo que respecta a la migración de ADAL a MSAL.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 09/08/2021
ms.author: sahmalik
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 419b038f89df0d7fb5185fe2ef8ae43c76db7f7f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837929"
---
Los pasos siguientes para actualizar el código se aplican en todos los escenarios de cliente confidenciales:

1. Agregue el espacio de nombres MSAL.NET en el código fuente: `using Microsoft.Identity.Client;`.
2. En lugar de crear instancias de `AuthenticationContext`, use `PublicClientApplicationBuilder.Create` para crear instancias de `IPublicClientApplication`.
3. En lugar de la cadena `resourceId`, MSAL.NET usa ámbitos. Dado que las aplicaciones que usan ADAL.NET están preautorizadas, siempre puede usar los siguientes ámbitos: `new string[] { $"{resourceId}/.default" }`.
4. Reemplace la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IPublicClientApplication.AcquireTokenXXX`, donde *XXX* depende de su escenario.