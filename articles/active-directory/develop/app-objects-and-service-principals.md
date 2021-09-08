---
title: Aplicaciones y entidades de servicio en Azure AD
titleSuffix: Microsoft identity platform
description: Conozca la relación entre los objetos de aplicación y de entidad de servicio en Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/23/2021
ms.author: ryanwi
ms.custom: contperf-fy21q4-portal, aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: b8748bc5f628db654f67d68176e5cd10a1f02ec3
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539041"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos de aplicación y de entidad de servicio de Azure Active Directory

En este artículo se describe el registro de aplicación, los objetos de aplicación y las entidades de servicio de Azure Active Directory (Azure AD): qué son, cómo se usan y cómo se relacionan entre sí. También se presenta un escenario de ejemplo multiinquilino para ilustrar la relación entre un objeto de aplicación de la aplicación y los objetos de entidad de servicio correspondientes.

## <a name="application-registration"></a>Registro de la aplicación

Para delegar las funciones de administración de identidades y acceso a Azure AD, la aplicación debe registrarse con un inquilino de Azure AD. Cuando se registra una aplicación con Azure AD, se crea una configuración de identidad para la aplicación, lo que permite integrarla con Azure AD. Cuando registra una aplicación en Azure Portal, elige si es de un solo inquilino (solo accesible en el inquilino) o multiinquilino (accesible en otros inquilinos) y, opcionalmente, puede establecer un identificador URI de redirección (al que se envía el token de acceso). Puede encontrar instrucciones paso a paso sobre cómo registrar una aplicación en el [inicio rápido del registro de aplicaciones](quickstart-register-app.md).

Una vez completado el registro de la aplicación, tiene una instancia globalmente única de la aplicación (el objeto de aplicación) que reside en su directorio o inquilino principal. También tiene un identificador único global para la aplicación (el identificador de la aplicación o del cliente). En el portal, puede entonces agregar secretos o certificados y ámbitos para que la aplicación funcione, personalizar la marca de la aplicación en el cuadro de diálogo de inicio de sesión y mucho más.

Al registrar una aplicación en Azure Portal, se crean automáticamente un objeto de aplicación y un objeto de entidad de servicio en su inquilino principal. Si registra o crea una aplicación mediante las API de Microsoft Graph, la creación del objeto de entidad de servicio se hace en un paso independiente.

## <a name="application-object"></a>Objeto de aplicación

Una aplicación de Azure AD se define por su único objeto de aplicación, que reside en el inquilino de Azure AD donde se registró la aplicación, (conocido como inquilino "principal" de la aplicación).  El objeto de aplicación se usa como plantilla o plano técnico para crear uno o varios objetos de entidad de servicio.  La entidad de servicio se crea en todos los inquilinos en los que se utiliza la aplicación. De forma similar a una clase en la programación orientada a objetos, el objeto de aplicación tiene algunas propiedades estáticas que se aplican a todas las entidades de servicio creadas (o instancias de aplicación).

El objeto de aplicación describe tres aspectos de una aplicación: la forma en que el servicio emite tokens para tener acceso a la aplicación, los recursos a los que la aplicación podría necesitar tener acceso y las acciones que puede realizar la aplicación.

Puede usar el panel **Registros de aplicaciones** de [Azure Portal][AZURE-Portal] para mostrar y administrar los objetos de aplicación del inquilino principal.

![Hoja Registros de aplicaciones](./media/app-objects-and-service-principals/app-registrations-blade.png)

La [entidad de aplicación][MS-Graph-App-Entity] de Microsoft Graph define el esquema para las propiedades de un objeto de aplicación.

## <a name="service-principal-object"></a>Objeto de entidad de servicio

Para acceder a los recursos que están protegidos por un inquilino de Azure AD, la entidad que requiere acceso debe estar representada por una entidad de seguridad. Este requisito es cierto para los usuarios (entidad de seguridad de usuario) y para las aplicaciones (entidad de servicio). La entidad de seguridad define la directiva de acceso y los permisos para el usuario o aplicación de ese inquilino de Azure AD. Esto habilita características básicas como la autenticación del usuario o de la aplicación durante el inicio de sesión y la autorización durante el acceso a los recursos.

Hay tres tipos de entidad de servicio:

- **Aplicación**: el tipo de entidad de servicio es la representación local o la instancia de aplicación de un objeto de aplicación global en un único inquilino o directorio. En este caso, una entidad de servicio es una instancia concreta creada a partir del objeto de aplicación, que hereda determinadas propiedades de ese objeto de aplicación. La entidad de servicio se crea en cada inquilino donde se usa la aplicación y hace referencia al objeto de aplicación único global.  El objeto de entidad de servicio define lo que la aplicación puede hacer en el inquilino específico, quién puede acceder a la aplicación y a qué recursos tiene acceso la aplicación.

   Cuando una aplicación tiene permiso para acceder a los recursos de un inquilino (tras el registro o consentimiento), se crea un objeto de entidad de seguridad de servicio. Al registrar una aplicación desde Azure Portal, se crea automáticamente una entidad de servicio. También puede crear objetos de entidad de servicio en un inquilino mediante Azure PowerShell, la CLI de Azure, Microsoft Graph y otras herramientas.

- **Identidad administrada**: este tipo de entidad de servicio se usa para representar una [identidad administrada](../managed-identities-azure-resources/overview.md). Las identidades administradas eliminan la necesidad de que los desarrolladores administren las credenciales. Además, proporcionan una identidad que usan las aplicaciones al conectarse a recursos que admiten la autenticación de Azure AD. Cuando se habilita una identidad administrada, se crea en el inquilino una entidad de servicio que representa esa identidad administrada. A las entidades de servicio que representan identidades administradas se les pueden conceder acceso y permisos, pero no se pueden actualizar ni modificar directamente.

- **Heredado**: este tipo de entidad de servicio representa una aplicación heredada (la que se ha creado antes de que se introdujeran o crearan registros de aplicaciones mediante experiencias heredadas). Una entidad de servicio heredada puede tener credenciales, nombres de entidad de servicio, direcciones URL de respuesta y otras propiedades que un usuario autorizado puede editar, pero no tiene un registro de aplicación asociado. La entidad de servicio solo se puede usar en el inquilino donde se creó.

La [entidad ServicePrincipal][MS-Graph-Sp-Entity] de Microsoft Graph define el esquema para las propiedades de un objeto de entidad de servicio.

Puede usar el panel **Aplicaciones empresariales** de Azure Portal para enumerar y administrar las entidades de servicio de un inquilino. Se pueden ver los permisos de la entidad de servicio, los permisos con consentimiento del usuario, los usuarios que han dado ese consentimiento, la información de inicio de sesión, etc.

![Hoja Aplicaciones empresariales](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Relación entre los objetos de aplicación y las entidades de servicio

El objeto de aplicación es la representación *global* de la aplicación para su uso en todos los inquilinos, y la entidad de servicio es la representación *local* para su uso en un inquilino específico. El objeto de aplicación actúa como la plantilla a partir de la cual se *derivan* las propiedades comunes y predeterminadas para su uso en la creación de objetos de entidad de servicio correspondientes.

Un objeto de aplicación tiene lo siguiente:

- Una relación 1:1 con la aplicación de software y
- Una relación 1:varios con sus objetos de entidad de servicio correspondientes.

Debe crearse una entidad de servicio en cada inquilino donde se usa la aplicación. Esto permite establecer una identidad para el inicio de sesión o el acceso a los recursos que va a proteger el inquilino. Una aplicación de inquilino único tendrá solo una entidad de servicio (en su inquilino principal), que normalmente se crea y se consiente para su uso durante el registro de la aplicación. Una aplicación multiinquilino también tiene una entidad de servicio creada en cada inquilino donde un usuario de ese inquilino ha dado su consentimiento para su uso.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Consecuencias de la modificación y eliminación de aplicaciones

Los cambios que realice en el objeto de aplicación también se reflejan en su objeto de entidad de servicio, solo en el inquilino principal de la aplicación (es decir, el inquilino en donde se registró). Esto significa que, al eliminar un objeto de la aplicación, también se eliminará su objeto de la entidad de servicio del inquilino principal. Sin embargo, si restaura el objeto de la aplicación no se restaurará su entidad de servicio correspondiente. Para las aplicaciones multiinquilino, los cambios realizados en el objeto de aplicación no se reflejan en los objetos de entidad de servicio del inquilino consumidor hasta que el acceso se quita por medio del [panel de acceso de la aplicación](https://myapps.microsoft.com) y se concede de nuevo.

## <a name="example"></a>Ejemplo

En el diagrama siguiente se muestra la relación entre un objeto de aplicación de la aplicación y los objetos de entidad de servicio correspondientes, en el contexto de una aplicación multiinquilino de ejemplo denominada **HR app**. En este escenario de ejemplo hay tres inquilinos de Azure AD:

- **Adatum**: el inquilino usado por la empresa que desarrolló la **aplicación de recursos humanos**.
- **Contoso**: el inquilino usado por la organización Contoso, que es un consumidor de la **aplicación de recursos humanos**.
- **Fabrikam**: el inquilino usado por la organización Fabrikam, que también consume la **aplicación de recursos humanos**.

![Relación entre un objeto de aplicación y un objeto de entidad de servicio](./media/app-objects-and-service-principals/application-objects-relationship.svg)

En este escenario de ejemplo:

| Paso | Descripción |
|------|-------------|
| 1    | Es el proceso de creación de los objetos de aplicación y de entidad de servicio del inquilino principal de la aplicación. |
| 2    | Cuando los administradores de Contoso y Fabrikam completan el consentimiento, se crea un objeto de entidad de servicio en el inquilino de Azure AD de la empresa y se asignan los permisos que concede el administrador. Tenga en cuenta también que la aplicación de recursos humanos podría estar configurada o diseñada para dar consentimiento a los usuarios para el uso individual. |
| 3    | Los inquilinos consumidores de la aplicación HR (Contoso y Fabrikam) tienen cada uno de ellos su propio objeto de entidad de servicio. Cada uno representa su uso de una instancia de la aplicación en tiempo de ejecución, que se rige por los permisos que consiente el administrador correspondiente. |

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear una entidad de servicio:

- [Uso de Azure Portal](howto-create-service-principal-portal.md)
- [Uso de Azure PowerShell](howto-authenticate-service-principal-powershell.md)
- [Uso de la CLI de Azure](/cli/azure/create-an-azure-service-principal-azure-cli)
- [Uso de Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http) y después de [Probador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) para consultar tanto objetos de aplicación como objetos de entidad de servicio.

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com