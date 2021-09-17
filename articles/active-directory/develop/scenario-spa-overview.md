---
title: Escenario de aplicación de una sola página de JavaScript
titleSuffix: Microsoft identity platform
description: Aprenda a compilar una aplicación de página única (información general del escenario) mediante la plataforma de identidad de Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: be14a8cb9d72c439f5ba127858ccd3a3249b8a14
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770775"
---
# <a name="scenario-single-page-application"></a>Escenario: Aplicación de una sola página

Aprenda lo necesario para crear una aplicación de página única (SPA)

## <a name="getting-started"></a>Introducción

Si aún no lo ha hecho, cree su primera aplicación según se indica en la guía de inicio rápido de JavaScript SPA:

[Inicio rápido: Aplicación de página única](./quickstart-v2-javascript-auth-code.md)

## <a name="overview"></a>Información general

Muchas aplicaciones web modernas se compilan como aplicaciones de página única del lado cliente. Los desarrolladores las escriben mediante JavaScript o un marco SPA, como Angular, Vue y React. Estas aplicaciones se ejecutan en un explorador web y tienen características de autenticación diferentes a las de las aplicaciones web del lado servidor tradicionales.

La Plataforma de identidad de Microsoft ofrece **dos** opciones para que las aplicaciones de página única inicien la sesión de los usuarios y obtengan tokens para acceder a los servicios de back-end o a las API web:

- [Flujo de códigos de autorización de OAuth 2.0 (con PKCE)](./v2-oauth2-auth-code-flow.md) El flujo del código de autorización permite a la aplicación intercambiar un código de autorización para los tokens de **identificador** para representar al usuario autenticado y también los tokens de **acceso** necesarios para llamar a las API protegidas. PKCE es una clave de prueba para intercambio de códigos y está diseñada para evitar varios ataques y para poder realizar de forma segura el intercambio de OAuth desde clientes públicos. PKCE es un estándar de IETF documentado en RFC 7636. Además, devuelve tokens de **actualización** que proporcionan acceso a largo plazo a los recursos en nombre de los usuarios sin necesidad de interacción con estos. Este es el enfoque **recomendado**.

![Aplicaciones de página única: autorización](./media/scenarios/spa-app-auth.svg)

- [Flujo implícito de OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). El flujo de concesión implícita permite a la aplicación obtener tokens de **identificador** y de **acceso**. A diferencia del flujo del código de autorización, el flujo de concesión implícita no devuelve un **token de actualización**.

![Aplicaciones de página única: implícito](./media/scenarios/spa-app.svg)

Este flujo de autenticación no incluye escenarios de aplicaciones que usan marcos de JavaScript multiplataforma, como Electron y React Native, ya que requieren más funcionalidades para la interacción con las plataformas nativas.

## <a name="specifics"></a>Características específicas

Para habilitar este escenario en la aplicación, necesita lo siguiente:

* Registro de aplicaciones con Azure Active Directory (Azure AD). Los pasos para el registro difieren entre el flujo de concesión implícita y el flujo de código de autorización.
* La configuración de la aplicación con las propiedades de aplicación registradas, como el identificador de aplicación.
* Usar la biblioteca de autenticación de Microsoft para JavaScript (MSAL.js) para completar el flujo de autenticación a fin de iniciar sesión y adquirir tokens.

## <a name="recommended-reading"></a>Lecturas recomendadas

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Registro de la aplicación](scenario-spa-app-registration.md).
