---
title: 'Tutorial: Creación de una aplicación web segura en Azure App Service | Azure'
description: En este tutorial aprenderá a compilar una aplicación web con Azure App Service, habilitar la autenticación, llamar a Azure Storage y llamar a Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/26/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: c6b236c3ce906c5b573db40e1c002924a3e75660
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065520"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutorial: Habilitación de la autenticación en App Service y acceso a Storage y a Microsoft Graph

En este tutorial se describe un escenario de aplicación común en el que aprenderá a:

- [Configurar la autenticación para una aplicación web](scenario-secure-app-authentication-app-service.md) y limitar el acceso solo a los usuarios de su organización. Vea A en el diagrama.
- [Establecer el acceso de forma segura a Azure Storage](scenario-secure-app-access-storage.md) para la aplicación web mediante identidades administradas. Vea B en el diagrama.
- Establecer el acceso a los datos en Microsoft Graph [para el usuario con la sesión iniciada](scenario-secure-app-access-microsoft-graph-as-user.md) o [para la aplicación web](scenario-secure-app-access-microsoft-graph-as-app.md) mediante identidades administradas. Vea C en el diagrama.
- [Limpie los recursos](scenario-secure-app-clean-up-resources.md) que creó para este tutorial.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagrama que muestra los escenarios de aplicaciones en la Plataforma de identidad de Microsoft." border="false":::

Para empezar, obtenga información sobre cómo habilitar la autenticación para una aplicación web.

> [!div class="nextstepaction"]
> [Configuración de la autenticación de una aplicación web](scenario-secure-app-authentication-app-service.md)
