---
title: 'Inicio rápido: Incorporación del inicio de sesión de usuario a una aplicación web Node.js | Azure'
titleSuffix: Microsoft identity platform
description: En este inicio rápido, aprenderá a implementar la autenticación en una aplicación web Node.js mediante OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: 26e0ae48c65953312757eb7ac4d6cb51ba2a44c5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590088"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Inicio rápido: Incorporación del inicio de sesión mediante OpenID Connect a una aplicación web de Node.js

En este inicio rápido, descargará y ejecutará un código de ejemplo que muestra cómo configurar la autenticación de OpenID Connect en una aplicación web creada mediante Node.js con Express. El ejemplo está diseñado para ejecutarse en cualquier plataforma.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Registrar su aplicación

1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Si tiene acceso a varios inquilinos, use el filtro **Directorios y suscripciones** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para ir al inquilino en el que quiere registrar la aplicación.
1. Busque y seleccione **Azure Active Directory**.
1. En **Administrar**, seleccione **Registros de aplicaciones** >  y, luego, **Nuevo registro**.
1. Escriba el **Nombre** de la aplicación, por ejemplo `MyWebApp`. Los usuarios de la aplicación pueden ver este nombre, el cual se puede cambiar más tarde.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier directorio organizativo y cuentas Microsoft personales (por ejemplo, Skype, Xbox, Outlook.com)** .

    Si hay más de un URI de redirección, agréguelos más adelante desde la pestaña **Autenticación**, una vez que la aplicación se haya creado correctamente.

1. Seleccione **Registrar** para crear la aplicación.
1. En la página **Información general** de la aplicación, busque el valor de **Id. de aplicación (cliente)** y regístrelo para usarlo más tarde. Necesitará este valor para configurar la aplicación más adelante en este proyecto.
1. En **Administrar**, seleccione **Autenticación**.
1. Seleccione **Agregar una plataforma** > **Web**. 
1. En la sección **URI de redirección**, escriba `http://localhost:3000/auth/openid/return`.
1. Especifique un valor para **Front-channel logout URL** (Dirección URL de cierre de sesión del canal frontal) de `https://localhost:3000`.
1. En la sección **Implicit grant and hybrid flows** (Flujos de concesión implícita e híbridos), seleccione **Tokens de id.** , ya que este ejemplo requiere que la opción [Implicit grant flow](./v2-oauth2-implicit-grant-flow.md) (Flujo de concesión implícita) esté habilitada para iniciar la sesión del usuario.
1. Seleccione **Configurar**.
1. En **Administrar**, seleccione **Certificados y secretos** > **Nuevo secreto de cliente**.
1. Introduzca una descripción de clave (por ejemplo, secreto de aplicación).
1. Seleccione la duración de clave **En 1 año, En 2 años** o **Nunca expira**.
1. Seleccione **Agregar**. Se mostrará el valor de la clave. Copie el valor de clave y guárdelo en una ubicación segura para usarlo más adelante.


## <a name="download-the-sample-application-and-modules"></a>Descarga de la aplicación de ejemplo y los módulos

A continuación, clone el repositorio de ejemplo e instale los módulos de NPM.

Desde el shell o la línea de comandos, ejecute:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

or

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

En el directorio raíz del proyecto, ejecute el comando:

`$ npm install`

## <a name="configure-the-application"></a>Configuración de la aplicación

Especifique los parámetros de `exports.creds` en config.js como se indica.

* Actualice `<tenant_name>` en `exports.identityMetadata` con el nombre de inquilino de Azure AD en el formato \*.onmicrosoft.com.
* Actualice `exports.clientID` con el Id. de la aplicación que anotó en el registro de la aplicación.
* Actualice `exports.clientSecret` con el secreto de la aplicación que anotó en el registro de la aplicación.
* Actualice `exports.redirectUrl` con el URI de redirección que anotó en el registro de la aplicación.

**Configuración opcional para las aplicaciones de producción:**

* Actualice `exports.destroySessionUrl` en config.js si quiere utilizar un `post_logout_redirect_uri` diferente.

* Establezca `exports.useMongoDBSessionStore` en config.js en true si quiere usar [mongoDB](https://www.mongodb.com) u otros [almacenes de sesión compatibles](https://github.com/expressjs/session#compatible-session-stores).
El almacén de sesión predeterminado de este ejemplo es `express-session`. El almacén de sesión predeterminado no es adecuado para entornos de producción.

* Actualice `exports.databaseUri` si quiere utilizar el almacén de sesiones de mongoDB y un URI de base de datos diferente.

* Actualice `exports.mongoDBSessionMaxAge`. Aquí puede especificar cuánto tiempo quiere conservar una sesión en mongoDB. La unidad es segundo(s).

## <a name="build-and-run-the-application"></a>Compilación y ejecución de la aplicación

Inicie el servicio de mongoDB. Si usa el almacén de sesiones de mongoDB en esta aplicación, debe [instalar mongoDB](http://www.mongodb.org/) e iniciar primero el servicio. Si usa el almacén de sesiones predeterminado, puede omitir este paso.

Ejecute la aplicación mediante el siguiente comando desde la línea de comandos.

```
$ node app.js
```

**¿Es difícil comprender la salida del servidor?:** Se usó `bunyan` para el registro en este ejemplo. La consola no tendrá mucho sentido a menos que también instale bunyan y ejecute el servidor como se mostró anteriormente, aunque canalizado a través del archivo binario bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>¡Y ya está!

Tendrá un servidor ejecutándose correctamente en `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el escenario de aplicación web que admite la plataforma de identidad de Microsoft:
> [!div class="nextstepaction"]
> [Escenario de una aplicación web que permite iniciar sesión a los usuarios](scenario-web-app-sign-user-overview.md)