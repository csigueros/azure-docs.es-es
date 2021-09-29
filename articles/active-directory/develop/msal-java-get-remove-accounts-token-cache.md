---
title: Obtención y eliminación de cuentas de la caché de tokens (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a ver y eliminar cuentas de la caché de tokens con la biblioteca de autenticación de Microsoft para Java.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 8ea51bfd356e7479f421a6db804ec94bf656284a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612536"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Obtención y eliminación de cuentas de la memoria caché de tokens mediante MSAL para Java

MSAL para Java proporciona una caché de tokens en memoria de forma predeterminada. La caché de tokens en memoria tiene la misma duración que la instancia de la aplicación.

## <a name="see-which-accounts-are-in-the-cache"></a>Consulta de las cuentas que están en la memoria caché

Puede comprobar qué cuentas se encuentran en la memoria caché mediante una llamada a `PublicClientApplication.getAccounts()`, tal y como se muestra en el ejemplo siguiente:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Quitar cuentas de la memoria caché

Para quitar una cuenta de la memoria caché, busque la cuenta que tiene que quitar y realice una llamada a `PublicClientApplication.removeAccount()`, tal y como se muestra en el ejemplo siguiente:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Más información

Si usa MSAL para Java, obtenga información sobre [Serialización de la memoria caché de tokens personalizada en MSAL para Java](msal-java-token-cache-serialization.md).
