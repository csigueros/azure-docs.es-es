---
title: Implementación del control de acceso basado en roles en aplicaciones
titleSuffix: Microsoft identity platform
description: Aprenda a implementar el control de acceso basado en roles en aplicaciones.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/17/2021
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: ccbdbde261914e95470e37b0e2046a95e1fe5898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699454"
---
# <a name="implement-role-based-access-control-in-apps"></a>Implementación del control de acceso basado en roles en aplicaciones

El control de acceso basado en roles (RBAC) permite a los usuarios o grupos tener permisos concretos con respecto a los recursos a los que tienen acceso, qué pueden hacer con esos recursos y quién los administra.

Normalmente, cuando se habla de la implementación de RBAC para proteger un recurso, se busca proteger una aplicación web, una aplicación de página única (SPA) o una API.  Esto se podría realizar en toda la aplicación o API, o bien en áreas, características o métodos de API concretos.

En este artículo se explica cómo implementar el control de acceso basado en roles específico de la aplicación.  Para más información acerca de los aspectos básicos de la autorización, consulte [Conceptos básicos de autorización](./authorization-basics.md).

## <a name="implementing-rbac-using-the-microsoft-identity-platform"></a>Implementación de RBAC mediante la plataforma de identidad de Microsoft

Como se describe en [Control de acceso basado en rol para desarrolladores de aplicaciones](./custom-rbac-for-developers.md), hay tres formas de implementar RBAC mediante la plataforma de identidad de Microsoft:

- **Roles de aplicación**: uso de la [característica Roles de aplicación en un registro de aplicación](./howto-add-app-roles-in-azure-ad-apps.md#declare-roles-for-an-application) junto con la lógica dentro de la aplicación para interpretar las asignaciones entrantes de Roles de aplicación.
- **Grupos**: uso de las asignaciones de grupo de una identidad entrante junto con la lógica dentro de la aplicación para interpretar las asignaciones de grupo. 
- **Almacén de datos personalizado**: recupere e interprete las asignaciones de roles mediante lógica dentro de la aplicación.

El enfoque preferido es usar *Roles de aplicación*, ya que es el más fácil de implementar. Este enfoque es compatible directamente con los SDK que se usan en la creación de aplicaciones que usan la plataforma de identidad de Microsoft. Para más información sobre cómo elegir un enfoque, consulte [Elección de un enfoque](./custom-rbac-for-developers.md#choosing-an-approach).

En el resto de este artículo se mostrará cómo definir roles de aplicación e implementar el control de acceso basado en roles dentro de una aplicación mediante los roles de aplicación.

## <a name="defining-roles-for-your-application"></a>Definición de roles para una aplicación

El primer paso para implementar el control de acceso basado en roles en una aplicación es definir los roles que necesita la aplicación y asignar usuarios o grupos a esos roles.  Este proceso se describe en [Procedimiento: Incorporación de roles de aplicación a una aplicación y su recepción en el token](./howto-add-app-roles-in-azure-ad-apps.md). Una vez que haya definido los roles y haya asignado usuarios o grupos, puede acceder a las asignaciones de roles de los tokens que llegan a la aplicación y actuar en consecuencia.

## <a name="implementing-rbac-in-aspnet-core"></a>Implementación del control de acceso basado en roles en ASP.NET Core 

ASP.NET Core admite la adición del control de acceso basado en roles a una aplicación web ASP.NET Core web o una API web.  Esto permite que el control de acceso basado en roles se implemente fácilmente mediante [comprobaciones de roles](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks) con el atributo *Authorize* de ASP.NET Core. También es posible usar la compatibilidad de ASP.NET Core con las [comprobaciones de roles basadas en directivas](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#policy-based-role-checks).

### <a name="aspnet-core-mvc-web-application"></a>Aplicación web MVC de ASP.NET Core 

Es sencillo implementar el control de acceso basado en roles en una aplicación web MVC de ASP.NET Core.  Implica principalmente el uso del atributo *Authorize* para especificar a qué roles se les permite acceder a controladores concretos o a acciones en los controladores. Siga estos pasos para implementar el control de acceso basado en roles en la aplicación MVC de ASP.NET Core:
1. Cree un registro de aplicación con roles de aplicación y asignaciones como se describe en la sección *Definición de roles para una aplicación*.
1. Realice uno de los siguientes pasos:
    - Cree un proyecto de aplicación web MVC de ASP.NET Core mediante la **CLI de dotnet**.  Especifique la marca *--auth* con *SingleOrg* para la autenticación de inquilino único o *MultiOrg* para la autenticación multiinquilino, la marca *--client-id* con el cliente si es desde el registro de la aplicación y la marca *--tenant-id* con el inquilino si es desde el inquilino de Azure AD:
 
      ```bash 
    
      dotnet new mvc --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID>  
    
      ```
      
    - Agregue las bibliotecas Microsoft.Identity.Web y Microsoft.Identity.Web.UI a un proyecto MVC de ASP.NET Core existente:
 
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Microsoft.Identity.Web.UI 

      ```

   A continuación, siga las instrucciones especificadas en [Inicio rápido: Agregar información de inicio de sesión con Microsoft a una aplicación web de ASP.NET Core](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true) para agregar autenticación a una aplicación.
1. Agregue comprobaciones de roles a las acciones del controlador como se describe en [Adición de comprobaciones de roles](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks).
1. Pruebe la aplicación, para lo que debe intentar acceder a una de las rutas de MVC protegidas.

### <a name="aspnet-core-web-api"></a>API web de ASP.NET Core

La implementación del control de acceso basado en roles en una API web implica principalmente el uso del atributo *Authorize* para especificar a qué roles se les permite acceder a controladores concretos o a acciones en los controladores. Siga estos pasos para implementar el control de acceso basado en roles en la API web de ASP.NET Core:
1. Cree un registro de aplicación con roles de aplicación y asignaciones como se describe en la sección *Definición de roles para una aplicación*.
1. Realice uno de los siguientes pasos:
    - Cree un proyecto de la API web MVC de ASP.NET Core mediante la **CLI de dotnet**.  Especifique la marca *--auth* con *SingleOrg* para la autenticación de inquilino único o *MultiOrg* para la autenticación multiinquilino, la marca *--client-id* con el cliente si es desde el registro de la aplicación y la marca *--tenant-id* con el inquilino si es desde el inquilino de Azure AD:

      ```bash 
    
      dotnet new webapi --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID> 
    
      ```

    - Agregue las bibliotecas Microsoft.Identity.Web y Swashbuckle.AspNetCore a un proyecto de API web de ASP.NET Core existente:
      
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Swashbuckle.AspNetCore 

      ```
    
   A continuación, siga las instrucciones especificadas en [Inicio rápido: Agregar información de inicio de sesión con Microsoft a una aplicación web de ASP.NET Core](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true) para agregar autenticación a una aplicación.
1. Agregue comprobaciones de roles a las acciones del controlador como se describe en [Adición de comprobaciones de roles](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks).
1. Llame a la API desde una aplicación cliente.  Consulte el artículo sobre el hecho de que la [aplicación de página única Angular llama a la API web de .NET Core y usa Roles de aplicación para implementar el control de acceso basado en roles](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles) para obtener un ejemplo de un extremo a otro.


## <a name="implementing-rbac-in-other-platforms"></a>Implementación del control de acceso basado en roles en otras plataformas

### <a name="angular-spa-using-msalguard"></a>Aplicación de página única Angular con MsalGuard
La implementación del control de acceso basado en roles en una aplicación de página única Angular implica el uso de [msal-angular](https://www.npmjs.com/package/@azure/msal-angular) para autorizar el acceso a las rutas de Angular contenidas en la aplicación.  Esto se muestra en el ejemplo de [habilitación de la aplicación de página única Angular para iniciar sesión a los usuarios y llamar a las API con la plataforma de identidad de Microsoft](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial#chapter-5-control-access-to-your-protected-api-using-app-roles-and-security-groups).

> [!NOTE]
> Las implementaciones del control de acceso basado en roles en el cliente deben emparejarse con el control de acceso basado en roles en el servidor para evitar que aplicaciones no autorizadas accedan a recursos confidenciales.

### <a name="nodejs-with-express-application"></a>Aplicación Node.js con Express
La implementación del control de acceso basado en roles en la aplicación Node.js con Express implica el uso de MSAL para autorizar el acceso a las rutas de Express que contiene la aplicación.  Esto se muestra en el ejemplo de [habilitación de una aplicación web Node.js para iniciar sesión a los usuarios y llamar a las API con la plataforma de identidad de Microsoft](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial#chapter-4-control-access-to-your-app-using-app-roles-and-security-groups).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [permisos y consentimiento en la plataforma de identidad de Microsoft](./v2-permissions-and-consent.md).
- Más información sobre el [control de acceso basado en rol para desarrolladores de aplicaciones](./custom-rbac-for-developers.md).
