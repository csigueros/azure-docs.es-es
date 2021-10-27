---
title: Aplicaciones de uno o varios inquilinos en Azure AD
titleSuffix: Microsoft identity platform
description: Obtenga información acerca de las características y diferencias entre las aplicaciones de inquilino único y las de varios inquilinos de Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/13/2021
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 9a21d6f4a59709c74a02dff2334402510e156918
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987734"
---
# <a name="tenancy-in-azure-active-directory"></a>Inquilinos en Azure Active Directory

Azure Active Directory (Azure AD), organiza los objetos como usuarios y aplicaciones en grupos llamados _inquilinos_. Los inquilinos permiten a los administradores establecer directivas en los usuarios de la organización y de las aplicaciones que posee la organización para cumplir sus directivas de seguridad y funcionamiento.

## <a name="who-can-sign-in-to-your-app"></a>¿Quién puede iniciar sesión en su aplicación?

En cuanto al desarrollo de aplicaciones, los desarrolladores pueden elegir configurar su aplicación para que sea de un único inquilino o multiinquilino durante el registro de la misma en [Azure Portal](https://portal.azure.com).

- Las aplicaciones de inquilino único solo están disponibles en el inquilino en el que se han registrado, que también se conoce como su inquilino principal.
- Las aplicaciones de varios inquilinos están disponibles para los usuarios en su inquilino principal y en otros inquilinos.

En Azure Portal, puede configurar la aplicación para que sea de inquilino único o multiinquilino. Para ello, debe establecer la audiencia como se indica a continuación.

| Público                                                                                              | Inquilino único/multiinquilino | Quién puede iniciar sesión                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Solo las cuentas de este directorio                                                                       | Un solo inquilino       | Todas las cuentas de usuario y de invitados del directorio pueden usar la aplicación o la API.<br>_Esta opción se usa si la audiencia de destino está dentro de la organización._                                                                                                                                                         |
| Las cuentas de cualquier directorio de Azure AD                                                                    | Multiinquilino        | Todos los usuarios e invitados con una cuenta profesional o educativa de Microsoft pueden usar la aplicación o API, incluidos los centros educativos y las empresas que usen Microsoft 365.<br>_Use esta opción si la audiencia de destino son clientes empresariales o del sector educativo._                                                                    |
| Las cuentas de cualquier directorio de Azure AD y las cuentas personales de Microsoft (por ejemplo, las de Skype, Xbox, Outlook.com) | Multiinquilino        | Cualquier usuario con una cuenta profesional o educativa, o bien con una cuenta Microsoft personal puede usar la aplicación o API, incluidos los centros educativos y las empresas que usen Microsoft 365, así como las cuentas personales que se usan para iniciar sesión en servicios como Xbox y Skype.<br>_Use esta opción para establecer como destino el mayor conjunto posible de cuentas de Microsoft._ |

## <a name="best-practices-for-multi-tenant-apps"></a>Procedimientos recomendados para aplicaciones multiinquilino

La creación de magníficas aplicaciones multiinquilino puede ser difícil debido al número de directivas que los administradores de TI pueden establecer en sus inquilinos. Si decide crear una aplicación multiinquilino, siga estos procedimientos recomendados:

- Pruebe la aplicación en un inquilino que tenga configuradas [directivas de acceso condicional](../azuread-dev/conditional-access-dev-guide.md).
- Siga el principio de acceso mínimo de los usuarios para asegurarse de que la aplicación solo solicita los permisos que realmente necesita.
- Especifique los nombres apropiados y las descripciones pertinentes de los permisos que exponga como parte de la aplicación. Esto ayuda a los usuarios y administradores a saber lo que aceptan cuando intentan usar las API de la aplicación. Para más información, consulte la sección de procedimientos recomendado en la [guía de permisos](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el inquilinato en Azure AD, vea:

- [Conversión de una aplicación de inquilino único en una aplicación multiinquilino](howto-convert-app-to-be-multi-tenant.md)
- [Habilitación de inicios de sesión multiinquilino](howto-convert-app-to-be-multi-tenant.md)
