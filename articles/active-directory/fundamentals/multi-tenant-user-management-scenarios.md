---
title: Escenarios comunes para usar la administración de usuarios multiinquilino en Azure Active Directory
description: Obtenga información sobre los escenarios comunes en los que se pueden usar cuentas de invitado para configurar el acceso de usuario entre inquilinos de Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fde715f688d7e19477894f3239d174d7925edb78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059402"
---
# <a name="multi-tenant-user-management-scenarios"></a>Escenarios de administración de usuarios multiinquilino

## <a name="end-user-initiated-scenario"></a>Escenario iniciado por el usuario final
En el escenario iniciado por el usuario final, los administradores de inquilinos de recursos delegan determinadas capacidades a los usuarios del inquilino. Los administradores permiten a los usuarios finales invitar a usuarios invitados al inquilino, una aplicación o un recurso. Los usuarios del inquilino principal se invitan o se sucriben individualmente.

Un caso de uso de ejemplo sería para una empresa global de servicios profesionales que trabaja con subcontratistas en un proyecto. Los usuarios de subcontratistas requieren acceso a las aplicaciones y documentos de la empresa. Los administradores de la empresa pueden delegar a los usuarios finales de la empresa la capacidad de invitar a subcontratistas o configurar el autoservicio para el acceso a recursos de subcontratista.

### <a name="provision-accounts"></a>Aprovisionamiento de cuentas

Hay muchas maneras de invitar a los usuarios finales a acceder a los recursos del inquilino de recursos. Estas son cinco de las más usadas: 

* [Invitaciones basadas en la aplicación](../external-identities/o365-external-user.md). Las aplicaciones de Microsoft pueden habilitar la invitación de usuarios invitados. La configuración de invitación B2B debe configurarse tanto en Azure AD B2B como en la aplicación o aplicaciones correspondientes.

* [MyApps]../user-help/my-apps-portal-end-user-access). Los usuarios invitan y asignan un usuario invitado a una aplicación mediante MyApps. La cuenta de usuario debe tener permisos de aprobador de [registro de autoservicio de la aplicación](../manage-apps/manage-self-service-access.md). Pueden invitar a usuarios invitados a un grupo si son propietarios del grupo.

* [Administración de derechos](../governance/entitlement-management-overview.md): permite a los administradores o propietarios de recursos vincular recursos, organizaciones externas permitidas, expiración de usuarios invitados y directivas de acceso en paquetes de acceso. Los paquetes de acceso se pueden publicar para habilitar el registro de autoservicio para que los usuarios invitados puedan acceder a los recursos.

* [Azure Portal](../external-identities/add-users-administrator.md) Los usuarios finales con el [rol Invitador de usuarios invitados](../external-identities/delegate-invitations.md) pueden iniciar sesión en Azure Portal e invitar a usuarios invitados desde el menú Usuarios de Azure Active Directory.

* [Programación (PowerShell, Graph API)](../external-identities/customize-invitation-api.md) Los usuarios finales con el [rol Invitador de usuarios invitados](../external-identities/delegate-invitations.md) pueden invitar a usuarios invitados a través de PowerShell o Graph API. 

### <a name="redeem-invitations"></a>Canje de invitaciones

Como parte del aprovisionamiento de cuentas para acceder a un recurso, se envían invitaciones de correo electrónico a la dirección de correo de los usuarios invitados. Cuando un usuario invitado recibe una invitación, puede hacer lo siguiente:
* Siga el vínculo incluido en el correo electrónico a la dirección URL de canje.
* Intente acceder al recurso directamente. 

Cuando el usuario intenta acceder al recurso directamente, se denomina "canje Just-In-Time (JIT)". A continuación se incluyen las experiencias del usuario para cada método de canje. 

#### <a name="redemption-url"></a>Dirección URL de canje

Al acceder a la [dirección URL de canje](../external-identities/redemption-experience.md) en el correo electrónico, el usuario invitado puede aprobar o denegar la invitación (creando una cuenta de usuario invitado si es necesario). 

#### <a name="just-in-time-redemption"></a>Canje Just-In-Time

El usuario puede acceder a la dirección URL del recurso directamente para el canje Just-In-Time si se dan los siguientes casos:

* El usuario invitado ya tiene una cuenta Azure AD o Microsoft  
‎-o-

* Si los [códigos de acceso de un solo uso por correo electrónico](../external-identities/one-time-passcode.md) están habilitados

Algunos puntos que tener en cuenta durante el canje JIT:

* Si los administradores no han suprimido la aceptación de términos de privacidad, el usuario debe aceptar la página del contrato términos de privacidad antes de acceder al recurso.

* PowerShell permite controlar si se envía un correo electrónico al invitar [a través de PowerShell](/powershell/module/azuread/new-azureadmsinvitation?view=azureadps-2.0&preserve-view=true).

* Puede permitir o bloquear invitaciones a usuarios invitados de organizaciones específicas mediante una [lista de permitidos o una lista de bloqueados](../external-identities/allow-deny-list.md).

Para más información, consulte [Experiencia de invitación de colaboración B2B de Azure Active Directory](../external-identities/redemption-experience.md).

#### <a name="important--enable-one-time-passcode-authentication"></a>Importante: Habilitación de la autenticación de códigos de acceso de un solo uso 

Se recomienda encarecidamente habilitar la [autenticación de códigos de acceso de un solo uso por correo electrónico](../external-identities/one-time-passcode.md). Esta característica autentica a los usuarios invitados cuando no se pueden autenticar a través de otros medios, como:

* Azure AD

* Una cuenta Microsoft (MSA)

* Una cuenta de Gmail a través de la federación de Google

* Una cuenta de un IDP de SAML/WS-Fed a través de la federación directa

Con la autenticación por código de acceso de un solo uso, no hay necesidad de crear una cuenta de Microsoft. Cuando el usuario invitado canjea una invitación o accede a un recurso compartido, recibe un código temporal. El código se envía a su dirección de correo electrónico y, a continuación, escribe el código para continuar con el inicio de sesión. 

Sin la autenticación de código de acceso de un solo uso habilitada por correo electrónico, se puede crear una cuenta Microsoft o un inquilino "no administrado" Just-In-Time de Azure AD.

>**Importante**: Microsoft está eliminando la posibilidad de crear inquilinos no administrados y sus usuarios, ya que esta característica pasa a estar disponible con carácter general (GA) en cada entorno de nube. 

### <a name="manage-accounts"></a>Administrar cuentas

El administrador de inquilinos de recursos administra las cuentas de usuario invitado en el inquilino de recursos. Las cuentas de usuarios invitados no se actualizan en función de los valores actualizados en el inquilino principal. De hecho, los únicos atributos visibles recibidos incluyen la dirección de correo electrónico y el nombre para mostrar.

Puede configurar más atributos en objetos de usuario invitado para facilitar escenarios. Por ejemplo, puede incluir rellenar la libreta de direcciones con detalles de contacto o en escenarios de derechos. Por ejemplo, considere lo siguiente:

* HiddenFromAddressListsEnabled

* GivenName

* Surname

* Título

* Departamento

* TelephoneNumber

Estos atributos se pueden establecer para [agregar invitados a la lista global de direcciones](/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide&preserve-view=true). Otros escenarios pueden requerir atributos diferentes, como para establecer derechos y permisos para paquetes de acceso, pertenencia dinámica a grupos, notificaciones SAML, etc.

Nota: Los usuarios invitados están ocultos de forma predeterminada en la lista global de direcciones (GAL). Establezca que los atributos de los usuarios invitados no estén ocultos para que se incluyan en el GAL unificado. Para obtener más información, consulte la documentación del kit de desarrollo de [Microsoft Exchange Online](multi-tenant-common-considerations.md#microsoft-exchange-online).

### <a name="deprovision-accounts"></a>Desaprovisionamiento de cuentas

Los escenarios iniciados por el usuario final descentralizan las decisiones de acceso. Sin embargo, la descentralización de las decisiones de acceso crea el desafío de decidir cuándo quitar un usuario invitado y su acceso asociado. La [administración de derechos](../governance/entitlement-management-overview.md) y las [revisiones de acceso](../governance/manage-guest-access-with-access-reviews.md) proporcionan una manera de revisar y quitar los usuarios invitados existentes y su acceso a los recursos. 

Nota: Si se invita a los usuarios fuera de la administración de derechos, debe crear un proceso independiente para revisar y administrar el acceso de esos usuarios invitados. Por ejemplo, si el usuario invitado se invita directamente a través de SharePoint Online, no se incluye en el proceso de administración de derechos.

## <a name="scripted-scenario"></a>Escenario con script

En el escenario con script, los administradores de inquilinos de recursos implementan un proceso de extracción con script para automatizar la detección y el aprovisionamiento de usuarios invitados. Este enfoque es común para los clientes que usan un mecanismo con scripts.

Un caso de uso de ejemplo sería una empresa de transportes global que adquiere un competidor. Cada empresa tiene un único inquilino de Azure AD. Quieren que los siguientes escenarios de "día uno" funcionen, sin que los usuarios tengan que realizar ningún paso de invitación o canje. Todos los usuarios deben poder: 

* Usar el inicio de sesión único en todos los recursos que aprovisionan

* Buscarse entre sí y también buscar otros recursos en una GAL unificada

* Determinar la presencia de los demás usuarios y poder iniciar mensajes instantáneos

* Acceder a una aplicación basada en la pertenencia dinámica a grupos

En este caso, el inquilino de cada organización es el inquilino principal de sus empleados existentes y el inquilino de recursos para los empleados de la otra organización.

### <a name="provision-accounts"></a>Aprovisionamiento de cuentas

Con la [consulta delta](/graph/delta-query-overview), los administradores de inquilinos pueden implementar un proceso de extracción con script para automatizar la detección y el aprovisionamiento de identidades con el fin de admitir el acceso a los recursos. Este proceso comprueba si hay nuevos usuarios en el inquilino principal y usa las API Graph B2B para aprovisionar esos usuarios como usuarios invitados en el inquilino de recursos. En el diagrama siguiente se muestran los componentes. 
### <a name="multi-tenant-topology"></a>Topología multiinquilino

![Escenario multiinquilino](media\multi-tenant-user-management-scenarios\multi-tenant-scripted-scenario.png)

* Los administradores de cada inquilino organizan previamente las credenciales y el consentimiento para permitir la lectura de cada inquilino.

* Permite a los administradores de inquilinos automatizar la enumeración y la "extracción" de usuarios con ámbito al inquilino de recursos.

* Use Microsoft Graph API con permisos con consentimiento para leer y aprovisionar usuarios a través de la API de invitación.

* El aprovisionamiento inicial puede leer atributos de origen y aplicarlos al objeto de usuario de destino.

### <a name="manage-accounts"></a>Administrar cuentas

La organización de recursos puede optar por aumentar los datos de perfil para admitir escenarios de uso compartido actualizando los atributos de metadatos del usuario en el inquilino de recursos. Sin embargo, si es necesaria la sincronización en curso, una solución sincronizada podría ser una mejor opción.

### <a name="deprovision-accounts"></a>Desaprovisionamiento de cuentas

La [consulta Delta](/graph/delta-query-overview) puede indicar cuándo es necesario desaprovisionar un usuario invitado. La [administración de derechos](../governance/entitlement-management-overview.md) y las [revisiones de acceso](../governance/manage-guest-access-with-access-reviews.md) también proporcionan una manera de revisar y quitar los usuarios invitados existentes y su acceso a los recursos. 

Nota: Si se invita a los usuarios fuera de la administración de derechos, debe crear un proceso independiente para revisar y administrar el acceso de esos usuarios invitados. Por ejemplo, si el usuario invitado se invita directamente a través de SharePoint Online, no se incluye en el proceso de administración de derechos.

## <a name="automated-scenario"></a>Escenario automatizado

Con diferencia, el patrón más complejo es el uso compartido sincronizado entre inquilinos. Este patrón permite escenarios de administración y desaprovisionamiento más automatizados que los iniciados por el usuario o los scripts. En escenarios automatizados, los administradores de inquilinos de recursos usan un sistema de aprovisionamiento de identidades para automatizar los procesos de aprovisionamiento y desaprovisionamiento.

Un caso de uso de ejemplo sería una conglomeración multinacional que tiene varias subsidiarias. Cada uno tiene su propio inquilino de Azure AD, pero necesitan trabajar juntos. Además de sincronizar nuevos usuarios entre inquilinos, las actualizaciones de atributos se deben sincronizar automáticamente. El desaprovisionado debe automatizarse. Por ejemplo, si un empleado ya no está en una subsidiaria, su cuenta debe quitarse de todos los demás inquilinos durante la siguiente sincronización.

También puede considerar el siguiente escenario expandido. Un contratista de la Base Industrial de la Defensa (DIB) tiene una subsidiaria de defensa y otra comercial. Estos tienen requisitos de regulación de la competencia:

* La empresa de defensa de Estados Unidos reside en un inquilino de nube soberana de EE. UU. Por ejemplo, Microsoft 365 US Government GCC High.

* La empresa comercial reside en un inquilino de Azure AD en la nube pública. Por ejemplo, un entorno de Azure AD que se ejecuta en la nube global de Azure. 

 Para actuar como una sola empresa implementada en una arquitectura de "nube soberana cruzada", todos los usuarios se sincronizan con ambos inquilinos. Esto habilita una GAL unificada disponible en ambos inquilinos. También puede garantizar que los usuarios sincronizados automáticamente con ambos inquilinos incluyan derechos y restricciones para las aplicaciones y el contenido. Por ejemplo:

* Los empleados de Estados Unidos pueden tener acceso ubicuo a ambos inquilinos.

* Los empleados que no son de EE. UU. se muestran en la GAL unificada de ambos inquilinos, pero no tienen acceso al contenido protegido en GCC High. 

Esto requerirá la sincronización automática y la administración de identidades para configurar los usuarios de ambos inquilinos, a la vez que se asocian a las directivas de protección de datos y derechos adecuadas.

### <a name="provision-accounts"></a>Aprovisionamiento de cuentas

Esta implementación avanzada usa [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) (MIM) como motor de sincronización. MIM llama a la [API Microsoft Graph API](/developer.microsoft.com/graph/) y [Exchange Online PowerShell](/powershell/exchange/exchange-online/exchange-online-powershell?view=exchange-ps&preserve-view=true). Las implementaciones alternativas pueden incluir la oferta de servicio administrado [Active Directory Synchronization Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (ADSS) hospedada en la nube de [Microsoft Consulting Services](/industry/services/consulting). También hay ofertas que no son de Microsoft que se pueden crear desde cero con otras ofertas de administración de identidades.

Se trata de escenarios complejos y se recomienda trabajar con los asociados, el equipo de cuentas de Microsoft y cualquier otro recurso disponible a lo largo del planeamiento y la ejecución. 

Nota: Hay consideraciones que están fuera del ámbito de este documento. Por ejemplo, la [integración de aplicaciones locales](../app-proxy/what-is-application-proxy.md). 

### <a name="choose-the-right-topology"></a>Elección de la topología correcta

La mayoría de los clientes usan una de estas dos topologías en escenarios automatizados. 

* Una topología de malla permite compartir todos los recursos de todos los inquilinos. Los usuarios de otros inquilinos se crean en cada inquilino de recursos como usuarios invitados.

* Una topología de inquilino de un solo recurso usa un solo inquilino (el inquilino de recursos), en el que los usuarios de otras empresas son usuarios invitados externos. 

La tabla siguiente se puede usar como árbol de decisión mientras se diseña la solución. Se ilustran ambas topologías después de la tabla. Para ayudarle a determinar cuál es adecuado para su organización, tenga en cuenta lo siguiente. 

Comparación de topologías de malla frente a topologías de inquilino de un solo recurso

| Consideración| Topología en malla| Inquilino de un solo recurso |
| - | - |-|
| Cada empresa tiene un inquilino de Azure AD con usuarios y recursos| Sí| Sí |
| **Ubicación de recursos y colaboración**| |  |
| Las aplicaciones compartidas y otros recursos permanecen en su inquilino principal actual| Yes| No: solo se comparten los recursos del inquilino de recursos. |
| Todos los elementos que se pueden ver en las GAL de una empresa individual (GAL unificada)| Sí| No |
| **Acceso a recursos y administración**| |  |
| TODAS las aplicaciones conectadas de Azure AD pueden compartirse entre todas las empresas| Yes| No: solo se comparten los del inquilino de recursos. Los que quedan en otros inquilinos no lo son. |
| Administración global de recursos| Continuar en el nivel de inquilino| Consolidado en el inquilino de recursos |
| Licencias: Office 365 <br>SharePoint GAL en línea, unificada, acceso a Teams para todos los invitados admitidos; aunque no en otros escenarios de Exchange Online| Continúa en el nivel de inquilino| Continúa en el nivel de inquilino |
| Licencias: [Azure AD (premium)](../external-identities/external-identities-pricing.md)| Los primeros 50 000 usuarios activos mensuales son gratuitos (por inquilino).| Los primeros 50 000 usuarios activos mensuales son gratuitos. |
| Licencias: aplicaciones SaaS| Permanecer en inquilinos individuales, puede requerir licencias por usuario y inquilino| Todos los recursos compartidos residen en el inquilino de un solo recurso. Puede investigar la consolidación de licencias en el inquilino único si procede. |

#### <a name="mesh-topology"></a>Topología en malla

![Topología en malla](media/multi-tenant-user-management-scenarios/mesh.png)

En una topología de malla, todos los usuarios de cada inquilino principal se sincronizan con cada uno de los otros inquilinos, que se convierten en inquilinos de recursos. 

* Esto permite que cualquier recurso dentro de un inquilino se comparta con los usuarios invitados. 

* Esto permite a cada organización ver todos los usuarios del conglomerado. En la ilustración anterior hay cuatro GAL unificadas, cada una de las cuales contiene los usuarios de inicio y los usuarios invitados de los otros tres inquilinos.

Consulte la sección [Consideraciones comunes](multi-tenant-common-considerations.md#directory-object-considerations) de este documento para obtener información adicional sobre el aprovisionamiento, la administración y el desaprovisionamiento de usuarios en este escenario.

#### <a name="mesh-topology-for-cross-sovereign-cloud"></a>Topología de malla para nubes soberanas cruzadas

La topología de malla se puede usar en tan solo dos inquilinos, como en el escenario del contratista de defensa DIB que se encuentra en una solución de nube soberana cruzada. Al igual que con la topología de malla, todos los usuarios de cada inquilino principal se sincronizan con el otro inquilino, lo que se convierte de hecho en un inquilino de recursos. En la ilustración anterior, el usuario miembro del inquilino comercial público se sincroniza con el inquilino de la nube soberana de EE. UU. GCC High como una cuenta de usuario invitado. Al mismo tiempo, el usuario miembro de GCC High se sincroniza con Commercial como una cuenta de usuario invitado.

>**Nota**: En la ilustración también se describe dónde se almacenan los datos. La categorización y el cumplimiento de datos están fuera del ámbito de estas notas del producto, pero demuestra que puede incluir derechos y restricciones en aplicaciones y contenido. El contenido puede incluir dónde residen los "datos personales" de un usuario miembro. Por ejemplo, los datos almacenados en su buzón de Exchange Online o en OneDrive para la Empresa. El contenido solo puede estar en su inquilino principal, no en el inquilino de recursos. Los datos compartidos pueden residir en cualquiera de los inquilinos. Puede restringir el acceso al contenido a través del control de acceso y las directivas de acceso condicional.

#### <a name="single-resource-tenant-topology"></a>Topología de inquilino de un solo recurso

![Inquilino de un solo recurso](media/multi-tenant-user-management-scenarios/single-resource-tenant.png)

En una topología de inquilino de un solo recurso, los usuarios y sus atributos se sincronizan con el inquilino de recursos (empresa A en la ilustración anterior). 

* Todos los recursos compartidos entre las organizaciones miembro deben residir en el inquilino de un solo recurso.
  * Si varias subsidiarias tienen suscripciones a las mismas aplicaciones SaaS, podría ser una oportunidad para consolidar esas suscripciones.

* Solo la GAL del inquilino de recursos muestra los usuarios de todas las empresas.

### <a name="manage-accounts"></a>Administrar cuentas

Esta solución detecta y sincroniza los cambios de atributos de los usuarios del inquilino de origen a los usuarios invitados del inquilino de recursos. Estos atributos se pueden usar para tomar decisiones de autorización. Por ejemplo, cuando se usan grupos dinámicos.

### <a name="deprovision-accounts"></a>Desaprovisionamiento de cuentas

Automation detecta la eliminación del objeto en el entorno de origen y elimina el objeto de usuario invitado asociado en el entorno de destino.

Consulte la sección [Consideraciones comunes](multi-tenant-common-considerations.md) de este documento para obtener información adicional sobre el aprovisionamiento, la administración y el desaprovisionamiento de usuarios en este escenario.

## <a name="next-steps"></a>Pasos siguientes
[Introducción a la administración de usuarios multiinquilino](multi-tenant-user-management-introduction.md)

[Consideraciones comunes para varios inquilinos](multi-tenant-common-considerations.md)

[Soluciones comunes multiinquilino](multi-tenant-common-solutions.md)
