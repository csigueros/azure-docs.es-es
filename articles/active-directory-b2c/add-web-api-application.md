---
title: 'Adición de una aplicación API web: Azure Active Directory B2C | Microsoft Docs'
description: Obtenga información sobre cómo agregar una aplicación API web en su inquilino de Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 08/24/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b30bb973c75551fc635b5a80131190bbb36ab118
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570243"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adición de una aplicación de API web al inquilino de Azure Active Directory B2C

 Registre los recursos de API web en el inquilino para que puedan aceptar y responder a solicitudes de aplicaciones cliente que presenten un token de acceso. En este artículo se muestra cómo registrar una API web en Azure Active Directory B2C (Azure AD B2C).

Para registrar una aplicación en el inquilino de Azure AD B2C, puede usar la nueva experiencia unificada **Registros de aplicaciones**, o bien la experiencia anterior **Aplicaciones (heredado)** . [Más información acerca de la nueva experiencia](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *webapi1*.
1. En **URI de redirección**, seleccione **Web** y escriba el punto de conexión donde Azure AD B2C deberá devolver los tokens que solicite la aplicación. En una aplicación de producción, puede establecer el URI de redirección en un punto de conexión como `https://localhost:5000`. Con fines de desarrollo o prueba, puede establecerlo en `https://jwt.ms`, una aplicación web propiedad de Microsoft que muestra el contenido descodificado de un token (el contenido del token nunca sale del explorador). Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento.
1. Seleccione **Registrar**.
1. Anote el **Id. de aplicación (cliente)** para usarlo en el código de la API web.

Si tiene una aplicación que implementa el flujo de concesión implícita, por ejemplo, una [aplicación de página única basada en JavaScript (SPA)](tutorial-register-spa.md), puede habilitar el flujo siguiendo estos pasos:

1. En **Administrar**, seleccione **Autenticación**.
1. En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**
1. Seleccione **Guardar**.

#### <a name="applications-legacy"></a>[Applications (Legacy)](#tab/applications-legacy/) (Aplicaciones [heredadas])

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Aplicaciones (heredado)** y, a continuación, **Agregar**.
1. Escriba un nombre para la aplicación. Por ejemplo, *webapi1*.
1. En **Incluir aplicación web o API web** y **Permitir flujo implícito**, seleccione **Sí**.
1. En **Dirección URL de respuesta**, escriba un punto de conexión donde Azure AD B2C devolverá los tokens que solicite la aplicación. En la aplicación de producción, puede establecer la dirección URL de respuesta en un valor como `https://localhost:44332`. Para fines de prueba, establezca la dirección URL de respuesta en `https://jwt.ms`.
1. En **URI de id. de aplicación**, escriba el identificador usado para la API web. Se genera el identificador URI completo, incluido el dominio. Por ejemplo, `https://contosotenant.onmicrosoft.com/api`.
1. Haga clic en **Crear**.
1. En la página de propiedades, registre el identificador de la aplicación que usará cuando configure la aplicación web.

* * *

## <a name="configure-scopes"></a>Configuración de ámbitos

Los ámbitos proporcionan una manera de controlar el acceso a los recursos protegidos. La API web utiliza los ámbitos para implementar el control de acceso basado en el ámbito. Por ejemplo, los usuarios de la API web pueden tener ambos accesos de lectura y de escritura, o pueden tener solo acceso de lectura. En este tutorial, utilizará ámbitos para definir los permisos de lectura y escritura para la API web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Concesión de permisos

Para llamar a una API web protegida desde una aplicación, deberá conceder permisos de aplicación a la API. Por ejemplo, en [Tutorial: registro de una aplicación en Azure Active Directory B2C](tutorial-register-applications.md), se registra una aplicación web en Azure AD B2C webapp1 denominada *webapp1*. Puede usar esta aplicación para llamar a la API web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

La aplicación está registrada para llamar a la API web protegida. Un usuario se autentica con Azure AD B2C para utilizar la aplicación. La aplicación obtiene una concesión de autorización de Azure AD B2C para acceder a la API web protegida.