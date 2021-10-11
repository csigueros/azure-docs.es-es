---
title: ¿Qué es el inicio de sesión único?
description: Obtenga información el inicio de sesión único en aplicaciones empresariales de Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q1
ms.openlocfilehash: c3b2cc9f3fa6656cf4d5e493f725c17b64f4d7d8
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233585"
---
# <a name="what-is-single-sign-on-in-azure-active-directory"></a>¿Qué es el inicio de sesión único en Azure Active Directory?

En este artículo se proporciona información sobre las opciones de inicio de sesión único (SSO) disponibles y una introducción al planeamiento de una implementación de inicio de sesión único al usar Azure Active Directory (Azure AD). El inicio de sesión único es un método de autenticación que permite a los usuarios iniciar sesión con un conjunto de credenciales en varios sistemas de software independientes. El inicio de sesión único permite que un usuario no tenga que iniciar sesión en cada aplicación que use. Con el inicio de sesión único, los usuarios pueden acceder a todas las aplicaciones necesarias sin tener que autenticarse con otras credenciales. 

En Azure AD hay muchas aplicaciones que puede usar con el inicio de sesión único. Tiene varias opciones para el inicio de sesión único en función de las necesidades de la aplicación y de cómo se implemente. Tómese tiempo para planear la implementación del inicio de sesión único antes de crear aplicaciones en Azure AD. La administración de aplicaciones se puede facilitar mediante el portal Aplicaciones.

## <a name="single-sign-on-options"></a>Opciones de inicio de sesión único

La elección de un método de inicio de sesión único depende de cómo esté configurada la aplicación para la autenticación. Las aplicaciones en la nube pueden usar opciones basadas en federación, como OpenID Connect, OAuth y SAML. La aplicación también puede usar el inicio de sesión único basado en contraseña, el inicio de sesión único basado en vinculación o el inicio de sesión único se puede deshabilitar.

- **Federación**: a la acción de configurar el inicio de sesión único para que funcione entre varios proveedores de identidades se le conoce como federación. Una implementación del inicio de sesión único basada en protocolos de federación mejora la seguridad, la confiabilidad y las experiencias de los usuarios finales, y es más fácil de implementar. 

    Con el inicio de sesión único federado, Azure AD autentica al usuario en la aplicación mediante su cuenta de Azure AD. Este método es compatible con las aplicaciones [SAML 2.0](../develop/single-sign-on-saml-protocol.md), WS-Federation u [OpenID Connect](../develop/active-directory-v2-protocols.md). El inicio de sesión único federado es el modo más completo de inicio de sesión único. Use el inicio de sesión único federado con Azure AD cuando una aplicación lo admita, en lugar del inicio de sesión único basado en contraseña y Servicios de federación de Active Directory (AD FS).

    Hay algunos escenarios en los que la opción de inicio de sesión único no está presente para una aplicación empresarial. Si la aplicación se registró mediante los **registros de aplicaciones** en el portal, la funcionalidad de inicio de sesión único está configurada para usar OpenID Connect y OAuth de forma predeterminada. En este caso, la opción de inicio de sesión único no se mostrará en la navegación en Aplicaciones empresariales.

    El inicio de sesión único no está disponible cuando una aplicación se hospeda en otro inquilino. El inicio de sesión único tampoco está disponible si la cuenta no tiene los permisos necesarios (Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio). Los permisos también pueden provocar un escenario en el que se puede abrir la opción de inicio de sesión único pero no se podrá guardar.

    > [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]

- **Contraseña**: las aplicaciones locales pueden usar métodos basados en contraseñas, en la autenticación integrada de Windows, en encabezados o vinculados para el inicio de sesión único. Las opciones locales funcionan si las aplicaciones están configuradas para el [proxy de aplicación](../app-proxy/what-is-application-proxy.md).

    Con el inicio de sesión único con contraseña, los usuarios finales inician sesión con un nombre de usuario y una contraseña en la aplicación la primera vez que acceden a ella. Después del primer inicio de sesión, Azure AD proporciona el nombre de usuario y la contraseña a la aplicación. El inicio de sesión único basado en contraseña permite el almacenamiento seguro de contraseñas de las aplicaciones y la reproducción mediante una extensión de explorador web o aplicación móvil. Esta opción usa el proceso de inicio de sesión existente proporcionado por la aplicación, permite que un administrador administre las contraseñas y no requiere que el usuario conozca la contraseña.

- **Vinculado**: el inicio de sesión vinculado puede proporcionar una experiencia de usuario coherente durante la migración de aplicaciones durante un período de tiempo. Si va a migrar aplicaciones a Azure AD, puede usar un inicio de sesión único vinculado para publicar rápidamente los vínculos a todas las aplicaciones que pretende migrar. Los usuarios pueden encontrar todos los vínculos en los portales Aplicaciones o Microsoft 365.

    Cuando el usuario se ha autenticado con una aplicación vinculada, se debe crear una cuenta antes de proporcionar al usuario el acceso de inicio de sesión único. El aprovisionamiento de esta cuenta puede producirse automáticamente o bien lo puede realizar manualmente un administrador. No se pueden aplicar directivas de acceso condicional ni autenticación multifactor a una aplicación vinculada porque una aplicación vinculada no proporciona funcionalidades de inicio de sesión único a través de Azure AD. Al configurar una aplicación vinculada, simplemente agrega un vínculo que aparece para iniciar la aplicación.

- **Deshabilitado**: cuando el inicio de sesión único está deshabilitado, no está disponible para la aplicación. Si el inicio de sesión único está deshabilitado, es posible que los usuarios tengan que autenticarse dos veces. Primero, los usuarios se autentican en Azure AD y, posteriormente, inician sesión en la aplicación.

    Deshabilite el inicio de sesión único cuando:

    - No está preparado para integrar esta aplicación con el inicio de sesión único de Azure AD.
    - Está probando otros aspectos de la aplicación.
    - Una aplicación local no requiere que los usuarios se autentiquen, pero quiere que lo haga. Con el inicio de sesión único deshabilitado, el usuario debe autenticarse.

    Si ha configurado la aplicación para el inicio de sesión único basado en SAML iniciado por SP y cambia el modo del inicio de sesión único para deshabilitarlo, no impedirá que los usuarios firmen a la aplicación fuera del portal Aplicaciones. Para ello, debe [deshabilitar la capacidad de inicio de sesión de los usuarios](disable-user-sign-in-portal.md).

## <a name="plan-sso-deployment"></a>Planeación de la implementación del inicio de sesión único

Varias empresas hospedan aplicaciones web y permiten que estén disponibles como servicio. Algunos ejemplos conocidos de aplicaciones web son Microsoft 365, GitHub y Salesforce, pero hay muchas otras. Las personas acceden a las aplicaciones web mediante un explorador web en su equipo. El inicio de sesión único permite a los usuarios desplazarse entre las distintas aplicaciones web sin tener que iniciar sesión varias veces.

La forma de implementar el inicio de sesión único depende de dónde se hospede la aplicación. El hospedaje es importante debido a la manera en que el tráfico de red se enruta para acceder a la aplicación. Los usuarios no necesitan usar Internet para acceder a aplicaciones locales (hospedadas en una red local). Si la aplicación se hospeda en la nube, los usuarios necesitan Internet para usarla. Las aplicaciones hospedadas en la nube también se denominan aplicaciones de software como servicio (SaaS).

Para las aplicaciones en la nube, se usan protocolos de federación. También puede usar el inicio de sesión único en aplicaciones locales. Puede usar Application Proxy configurar el acceso para la aplicación local. Para más información, consulte [Acceso remoto a aplicaciones locales mediante Azure AD Application Proxy](../app-proxy/application-proxy.md).

## <a name="my-apps"></a>Mis aplicaciones

Si es un usuario de una aplicación, es probable que no le interesen mucho los detalles del inicio de sesión único. Solo deseará usar las aplicaciones que le permitan ser productivo sin tener que escribir su contraseña a menudo. Puede encontrar y administrar las aplicaciones en el [portal Aplicaciones](https://myapps.microsoft.com). 

## <a name="next-steps"></a>Pasos siguientes

- [Planificación de una implementación de inicio de sesión único de Azure Active Directory](plan-sso-deployment.md)
