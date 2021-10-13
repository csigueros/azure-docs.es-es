---
title: Petición de consentimiento inesperada al iniciar sesión en una aplicación
titleSuffix: Azure AD
description: Cómo solucionar problemas cuando un usuario ve una solicitud de consentimiento para una aplicación que ha integrado con Azure AD que no esperaba
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d10368a6225fe1e5d09e7d6088b500febd6b1ff
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620761"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Petición de consentimiento inesperada al iniciar sesión en una aplicación

Muchas aplicaciones que se integran con Azure Active Directory requieren permisos para acceder a diversos recursos para poder ejecutarse. Cuando estos recursos también se integran con Azure Active Directory, suelen solicitarse permisos para acceder a ellos mediante el marco de consentimiento de Azure AD.

Esto da como resultado que la primera vez que se usa una aplicación aparece una solicitud de consentimiento, que suele ser una operación única.

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Escenarios en los que los usuarios ven solicitudes de consentimiento

Se pueden esperar más solicitudes en distintos escenarios:

* La aplicación se ha configurado para requerir la asignación. El consentimiento del usuario no se admite actualmente para las aplicaciones que requieren asignación. Si configura una aplicación para requerir la asignación, asegúrese de conceder también el consentimiento del administrador a todos los inquilinos para que los usuarios asignados puedan iniciar sesión.

* El conjunto de permisos que requiere la aplicación ha cambiado.

* El usuario que originalmente otorgó su consentimiento a la aplicación no era administrador, y ahora un usuario diferente (no administrador) está usando la aplicación por primera vez.

* El usuario que originalmente otorgó su consentimiento a la aplicación era administrador, pero no otorgó el consentimiento en nombre de toda la organización.

* La aplicación usa [consentimiento incremental y dinámico](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) para solicitar permisos adicionales después de haberse otorgado inicialmente el consentimiento. Esto se suele usar cuando las características opcionales de una aplicación adicional requieren permisos más allá de los que se requieren para la funcionalidad de línea base.

* Se ha revocado el consentimiento después de que inicialmente se otorgó.

* El desarrollador ha configurado la aplicación para que requiera una solicitud de consentimiento cada vez que se utilice (nota: esto no es recomendable).

   > [!NOTE]
   > Siguiendo las recomendaciones y los procedimientos recomendados de Microsoft, muchas organizaciones han deshabilitado o limitado el permiso de los usuarios para conceder consentimiento a las aplicaciones. Si una aplicación obliga a los usuarios a dar su consentimiento cada vez que inicien sesión, la mayoría de los usuarios no podrán usar estas aplicaciones incluso si un administrador concede consentimiento de administrador a todos los inquilinos. Si encuentra una aplicación que requiere el consentimiento del usuario incluso después de que se haya concedido el consentimiento del administrador, consulte al publicador de la aplicación para ver si existe una configuración o una opción para dejar de forzar el consentimiento del usuario en cada inicio de sesión.

## <a name="next-steps"></a>Pasos siguientes

* [Aplicaciones, permisos y consentimiento en Azure Active Directory (punto de conexión v1.0)](../develop/quickstart-register-app.md)

* [Ámbitos, permisos y consentimiento en Azure Active Directory (punto de conexión v2.0)](../develop/v2-permissions-and-consent.md)
