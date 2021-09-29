---
title: Búsqueda de una API para una aplicación de desarrollo personalizado | Azure
description: Configuración de los permisos necesarios para acceder a una API determinada en una aplicación desarrollada internamente con Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2d5c7f6e603b79b87297b0bbadb3d210ceaf0484
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154667"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Búsqueda de una API específica necesaria para una aplicación desarrollada internamente

El acceso a API requiere configurar los ámbitos y roles de acceso. Si quiere exponer sus API web de aplicaciones de recursos a aplicaciones cliente, configure los roles y ámbitos de acceso de la API. Si quiere que una aplicación cliente pueda acceder a una API web, configure permisos de acceso a la API en el registro de la aplicación.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuración de una aplicación de recursos para exponer las API web

Al exponer la API web, la API se muestra en la lista **Seleccionar una API** al agregar permisos al registro de una aplicación. Para agregar ámbitos de acceso, siga los pasos descritos en [Configuración de una aplicación para exponer las API web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configuración de una aplicación cliente para acceder a las API web

Al agregar permisos al registro de la aplicación, también puede **agregar acceso a API** a las API web expuestas. Para obtener acceso a las API web, siga los pasos descritos en [Configuración de una aplicación cliente para tener acceso a las API web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Pasos siguientes

- [Descripción del manifiesto de aplicación de Azure Active Directory](./reference-app-manifest.md)
