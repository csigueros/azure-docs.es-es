---
title: 'Tutorial: Registro de una aplicación'
titleSuffix: Azure AD B2C
description: Siga este tutorial para aprender a registrar una aplicación web en Azure Active Directory B2C con Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913393b36ec91b1db576a39711deeddca0f98258
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581628"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Tutorial: Registro de una aplicación web en Azure Active Directory B2C

Para que sus [aplicaciones](application-types.md) puedan interactuar con Azure Active Directory B2C (Azure AD B2C), deben estar registradas en un inquilino que administre. En este tutorial se muestra cómo registrar una aplicación web mediante Azure Portal. 

El término "aplicación web" hace referencia a una aplicación web tradicional que realiza la mayor parte de la lógica de la aplicación en el servidor. Estas aplicaciones pueden compilarse con marcos como ASP.NET Core, Maven (Java), Flask (Python) y Express (Node.js).

> [!IMPORTANT]
> Si usa una aplicación de página única ("SPA") en su lugar (por ejemplo, con Angular, Vue o React), aprenda [cómo registrar este tipo de aplicación](tutorial-register-spa.md).
> 
> Si usa una aplicación nativa (por ejemplo, iOS, Android, móvil y de escritorio), aprenda a [registrar una aplicación cliente nativa](add-native-application.md).

## <a name="prerequisites"></a>Requisitos previos
Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Si aún no ha creado su propio [inquilino de Azure AD B2C](tutorial-create-tenant.md), cree una ahora. Puede usar un inquilino de Azure AD B2C existente.

## <a name="register-a-web-application"></a>Registro de una aplicación web

Para registrar una aplicación web en el inquilino de Azure AD B2C, puede usar la nueva experiencia unificada **Registros de aplicaciones**, o bien la experiencia anterior **Aplicaciones (heredado)** . [Más información acerca de la nueva experiencia](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *webapp1*.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** .
1. En **URI de redirección**, seleccione **Web** y escriba `https://jwt.ms` en el cuadro de texto.

    El URI de redirección es el punto de conexión al que el servidor de autorización envía al usuario (Azure AD B2C, en este caso) después de completar su interacción con este, y al que se envía un token de acceso o un código de autorización tras una autorización correcta. En una aplicación de producción, suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com/auth-response`. Con fines de prueba como este tutorial, puede establecerlo en `https://jwt.ms`, una aplicación web propiedad de Microsoft que muestra el contenido descodificado de un token (el contenido del token nunca sale del explorador). Durante el desarrollo de aplicaciones, puede agregar el punto de conexión en el que la aplicación realiza escuchas localmente, como `https://localhost:5000`. Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento.

    Las siguientes restricciones se aplican a los URI de redireccionamiento:

    * La dirección URL de respuesta debe comenzar con el esquema `https`.
    * La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. Por ejemplo, si la aplicación incluye como parte de su ruta de acceso `.../abc/response-oidc`, no especifique `.../ABC/response-oidc` en la dirección URL de respuesta. Dado que el explorador web tiene en cuenta las mayúsculas y minúsculas de la ruta de acceso, se pueden excluir las cookies asociadas con `.../abc/response-oidc` si se redirigen a la dirección URL `.../ABC/response-oidc` con mayúsculas y minúsculas no coincidentes.

1. En **Permisos**, active la casilla *Conceda consentimiento del administrador a los permisos openid y offline_access*.
1. Seleccione **Registrar**.

#### <a name="applications-legacy"></a>[Aplicaciones (heredado)](#tab/applications-legacy/)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Aplicaciones (heredado)** y, luego, **Agregar**.
1. Escriba un nombre para la aplicación. Por ejemplo, *webapp1*.
1. En **Incluir aplicación web o API web**, seleccione **Sí**.
1. En **Dirección URL de respuesta**, escriba un punto de conexión donde Azure AD B2C devolverá los tokens que solicite la aplicación. Por ejemplo, se puede establecer para la escucha local en `http://localhost:5000`. Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento.

    Las siguientes restricciones se aplican a los URI de redireccionamiento:

    * La dirección URL de respuesta debe comenzar con el esquema `https`, a menos que use `localhost`.
    * La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. Por ejemplo, si la aplicación incluye como parte de su ruta de acceso `.../abc/response-oidc`, no especifique `.../ABC/response-oidc` en la dirección URL de respuesta. Dado que el explorador web tiene en cuenta las mayúsculas y minúsculas de la ruta de acceso, se pueden excluir las cookies asociadas con `.../abc/response-oidc` si se redirigen a la dirección URL `.../ABC/response-oidc` con mayúsculas y minúsculas no coincidentes.

1. Seleccione **Crear** para completar el registro de la aplicación.

* * *

## <a name="create-a-client-secret"></a>Creación de un secreto de cliente

En el caso de una aplicación web, debe crear un secreto de aplicación. El secreto de cliente también se conoce como *contraseña de la aplicación*. La aplicación usará este secreto para intercambiar un código de autorización por un token de acceso.

#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/)

1. En la página **Azure AD B2C: Registros de aplicaciones**, seleccione la aplicación que ha creado, por ejemplo, *webapp1*.
1. En el menú de la izquierda, en **Administrar**, seleccione **Certificados y secretos**.
1. Seleccione **Nuevo secreto de cliente**.
1. Escriba una descripción para el secreto de cliente en el cuadro **Descripción**. Por ejemplo, *clientsecret1*.
1. En **Expira**, seleccione el tiempo durante el cual el secreto es válido y, a continuación, seleccione **Agregar**.
1. Registre el **valor** del secreto para usarlo en el código de la aplicación cliente. Este valor secreto no se volverá a mostrar una vez que abandone esta página. Use este valor como secreto de aplicación en el código de la aplicación.

#### <a name="applications-legacy"></a>[Aplicaciones (heredado)](#tab/applications-legacy/)

1. En la página de **aplicaciones de Azure AD B2C**, seleccione la aplicación que ha creado, por ejemplo, *webapp1*.
1. Seleccione **Claves** y haga clic en **Generar clave**.
1. Seleccione **Guardar** para ver la clave. Anote el valor de la **Clave de la aplicación**. Use este valor como secreto de aplicación en el código de la aplicación.

* * *

> [!NOTE]
> Por motivos de seguridad, puede sustituir el secreto de la aplicación periódicamente, o bien, inmediatamente en caso de emergencia. Cualquier aplicación que se integre con Azure AD B2C debe estar preparada para controlar un evento de sustitución de secretos, con independencia de la frecuencia con que se produzca. Puede establecer dos secretos de aplicación para que la aplicación siga usando el secreto antiguo durante los eventos de cambio de secretos de aplicación. Para agregar otro secreto de cliente, repita los pasos de esta sección. 

## <a name="enable-id-token-implicit-grant"></a>Habilitación de la concesión implícita de tokens de identificador

La característica que define la concesión implícita determina que los tokens, como los tokens de identificador y de acceso, se devuelven directamente desde Azure AD B2C a la aplicación. En el caso de las aplicaciones web, como ASP.NET Core y [https://jwt.ms](https://jwt.ms), que solicitan un token de identificador directamente desde el punto de conexión de autorización, habilite el flujo de concesión implícita en el registro de la aplicación.

1. En el menú izquierdo, en **Administrar**, seleccione **Autenticación**.
1. En Concesión implícita, active las casillas **Tokens de acceso** y **Tokens de id.**
1. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo:

> [!div class="checklist"]
> * Registro de una aplicación web
> * Creación de un secreto de cliente

A continuación, aprenda a crear flujos de usuario para permitir que los usuarios se registren, inicien sesión y administren sus perfiles.

> [!div class="nextstepaction"]
> [Creación de flujos de usuario en Azure Active Directory B2C >](tutorial-create-user-flows.md)
