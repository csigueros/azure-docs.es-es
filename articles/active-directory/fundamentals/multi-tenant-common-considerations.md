---
title: Consideraciones comunes para la administración de usuarios multiinquilino en Azure Active Directory
description: Conozca consideraciones de diseño habituales para el acceso de los usuarios entre inquilinos de Azure Active Directory con cuentas de invitado.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a93eb869cd3135f548331a82de9593497829c0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269875"
---
# <a name="common-considerations-for-multi-tenant-user-management"></a>Consideraciones comunes para la administración de usuarios multiinquilino

Hay muchas consideraciones que son relevantes para más de un patrón de colaboración. 

## <a name="directory-object-considerations"></a>Consideraciones sobre objetos de directorio

Puede usar la consola para crear manualmente una invitación para una cuenta de usuario invitado. Al hacerlo, el objeto de usuario se crea con el tipo de usuario *Invitado*. El uso de otras técnicas para crear invitaciones permite establecer el tipo de usuario en algo distinto a una cuenta de invitado. Por ejemplo, cuando usa la API, puede especificar si es una cuenta de miembro o una cuenta de invitado.  

* Algunos de los [límites de la funcionalidad de invitado se pueden quitar](../external-identities/user-properties.md#remove-guest-user-limitations).

* [Puede convertir cuentas de invitado en el tipo de usuario Miembro](../external-identities/user-properties.md#can-azure-ad-b2b-users-be-added-as-members-instead-of-guests).

> **IMPORTANTE:** Si convierte una cuenta de invitado en una cuenta de usuario, puede haber problemas con la forma en la que Exchange Online controla las cuentas B2B. No puede habilitar para correo electrónico cuentas invitadas como miembros invitados. Para habilitar una cuenta de miembro invitado para correo electrónico, el mejor enfoque es:
>* Invitar a los usuarios entre organizaciones como cuentas de invitado.
>* Mostrar las cuentas en la LGD.
>* Establecer UserType en Member.

Con este enfoque, las cuentas se muestran como MailUser en Exchange Online.

### <a name="issues-with-using-mail-contact-objects-instead-of-external-users-or-members"></a>Problemas al usar objetos de contacto de correo en lugar de usuarios o miembros externos

Puede representar usuarios de otro inquilino usando una sincronización de LGD tradicional. Si realiza una sincronización de LGD en lugar de usar la Colaboración B2B de Azure AD, se crea un objeto de contacto de correo. 

* Un objeto de contacto de correo y un usuario invitado habilitado para correo (miembro o invitado) no pueden coexistir en el mismo inquilino con la misma dirección de correo electrónico. 

* Si hay un objeto de contacto de correo para la misma dirección de correo electrónico que el usuario invitado, se crea el usuario invitado, pero NO se habilita para correo electrónico. 

* Si ya existe el usuario invitado habilitado para correo con la misma dirección de correo, el intento de crear un objeto de contacto de correo produce una excepción.

A continuación, se muestran los resultados de varios estados de objeto de contacto de correo y usuario invitado.

| Estado actual| Escenario de aprovisionamiento| Resultado efectivo |
| - |-|-|
| Ninguno| Invitar a un miembro B2B| Usuario miembro no habilitado para correo. Vea la nota importante anterior |
| Ninguno| Invitar a un invitado B2B| Usuario invitado habilitado para correo |
| Existe un objeto mail-contact| Invitar a un miembro B2B| Error: Conflicto de direcciones de proxy |
| Existe un objeto mail-contact| Invitar a un invitado B2B| Contacto de correo y usuario invitado no habilitado para correo. Vea la nota importante anterior |
| Usuario invitado B2B habilitado para correo| Crear objeto de contacto de correo| Error |
| Existe un usuario miembro B2B habilitado para correo| Crear contacto de correo| Error |


**Microsoft no recomienda la sincronización de LGD tradicional.** En su lugar, utilice la Colaboración B2B de Azure AD para crear:

* Usuarios invitados externos que habilita para mostrarlos en la lista LGD.

* Usuarios miembro externos que se muestran en la lista LGD de forma predeterminada, pero no están habilitados para correo.

Algunas organizaciones usan el objeto de contacto de correo para mostrar usuarios en la lista LGD. Este método integra una lista LGD sin proporcionar otros permisos, ya que los contactos de correo no son entidades de seguridad. 

Una forma mejor de conseguir este objetivo es:
* Invitar a usuarios externos
* Mostrarlos desde la lista LGD.
* Deshabilitarlos [bloqueándolos para que no puedan iniciar sesión](/powershell/module/azuread/set-azureaduser&preserve-view=true).

Un objeto de contacto de correo no se puede convertir en un objeto de usuario. Por tanto, no se pueden transferir las propiedades asociadas a un objeto de contacto de correo. Por ejemplo, la pertenencia a grupos y el acceso a otros recursos no se transfieren.  

El uso de un objeto de contacto de correo para representar a un usuario conlleva las siguientes dificultades.

* **Grupos de Office 365:** los grupos de Office 365 admiten directivas que controlen los tipos de usuarios que pueden ser miembros de grupos e interactuar con el contenido asociado a los grupos. Por ejemplo, puede que un grupo no permita que se unan cuentas de invitado. Estas directivas no pueden controlar los objetos de contacto de correo.

* **Administración de grupos de autoservicio (SSGM) de Azure AD:** los objetos de contacto de correo no pueden ser miembros de grupos que usen la característica SSGM. Es posible que se necesiten herramientas adicionales para administrar grupos con destinatarios representados como contactos en lugar de objetos de usuario.

* **Revisiones de acceso de Azure AD Identity Governance:** la característica Revisiones de acceso se puede usar para revisar y atestar la pertenencia a grupos de Office 365. Las revisiones de acceso se basan en objetos de usuario. Los miembros representados por objetos de contacto de correo están fuera del ámbito de las revisiones de acceso.

* **Administración de derechos de Azure AD Identity Governance:** cuando se usa la administración de derechos para habilitar las solicitudes de acceso de autoservicio para usuarios externos en el portal de administración de derechos de la compañía, se crea un objeto de usuario en el momento de la solicitud. No se admiten objetos de contacto de correo. 

## <a name="azure-ad-conditional-access-considerations"></a>Consideraciones sobre el acceso condicional de Azure AD

El estado del usuario, el dispositivo o la red en el inquilino principal del usuario no se transmite al inquilino del recurso. Por tanto, es posible que una cuenta de usuario invitado no cumpla las directivas de acceso condicional que utilizan los controles siguientes.

* **Requerir autenticación multifactor:** los usuarios invitados deben registrarse o responder a la autenticación MFA en el inquilino del recurso, incluso si ya se realizó satisfactoriamente la autenticación MFA en el inquilino principal, lo que da lugar a varios desafíos MFA. Además, si necesitan restablecer sus pruebas de MFA, puede que no sean conscientes de los varios registros de pruebas de MFA entre inquilinos. Este desconocimiento puede dar lugar a que el usuario deba ponerse en contacto con un administrador del inquilino principal, el inquilino de recursos o ambos.

* **Requerir que el dispositivo esté marcado como compatible:** la identidad del dispositivo no está registrada en el inquilino del recurso, por tanto, se bloquea el acceso del usuario invitado a los recursos que requieran este control.

* **Requerir dispositivo unido a Azure AD híbrido:** la identidad del dispositivo no está registrada en el inquilino del recurso (o una instancia local de Active Directory está conectada al inquilino del recurso), por tanto, se bloquea el acceso del usuario invitado a los recursos que requieran este control.

* **Requerir aplicación cliente aprobada o Requerir la directiva de protección de aplicaciones:** los usuarios invitados externos no pueden aplicar la directiva de administración de aplicaciones móviles (MAM) de Intune del inquilino del recurso, porque también requiere el registro del dispositivo. La directiva de acceso condicional del inquilino del recurso que usa este control no permite que la protección MAM del inquilino principal cumpla la directiva. Los usuarios invitados externos deben excluirse de todas las directivas de acceso condicional basadas en MAM. 

Además, aunque se pueden usar las siguientes condiciones de acceso condicional, debe tener en cuenta las posibles ramificaciones.

* **Riesgo de inicio de sesión y riesgo de usuario:** el riesgo de inicio de sesión y el riesgo de usuario vienen determinados, en parte, por el comportamiento del usuario en el inquilino principal. Los datos y la puntuación de riesgo se almacenan en el inquilino principal.  
‎Si las directivas del inquilino del recurso bloquean a un usuario invitado, es posible que un administrador del inquilino del recurso no pueda habilitar el acceso. Para obtener más información, consulte [Protección de identidades y usuarios de Colaboración B2B](../identity-protection/concept-identity-protection-b2b.md).

* **Ubicaciones:** las ubicaciones con nombre que se definen en el inquilino del recurso se usan para determinar el ámbito de la directiva. Las ubicaciones de confianza administradas en el inquilino principal no se evalúan en el ámbito de la directiva. En algunos escenarios, es posible que las organizaciones quieran compartir ubicaciones de confianza entre inquilinos. Para compartir ubicaciones de confianza, las ubicaciones deben definirse en cada inquilino donde estén definidos los recursos y las directivas de acceso condicional.

## <a name="other-access-control-considerations"></a>Otras consideraciones sobre el control de acceso

Algunas consideraciones adicionales para configurar el control de acceso.
* Defina [directivas de control de acceso](../external-identities/conditional-access.md) para controlar el acceso a los recursos.
* Diseñe directivas de acceso condicional teniendo en cuenta a los usuarios invitados. 
* Cree directivas específicamente para los usuarios invitados. 
* Si su organización utiliza la condición [Todos los usuarios] en la directiva de acceso condicional, esta directiva afecta a los usuarios invitados, porque los usuarios [Invitado] están dentro del ámbito de [Todos los usuarios].
* Cree directivas de acceso condicional dedicadas para las cuentas [Invitado]. 

Para obtener información sobre cómo proteger los grupos dinámicos que usan la expresión [Todos los usuarios], vea [Grupos dinámicos y colaboración B2B de Azure Active Directory](../external-identities/use-dynamic-groups.md).

### <a name="require-user-assignment"></a>Requerimiento de la asignación de usuario

Si una aplicación tiene la propiedad [¿Asignación de usuarios?] establecida en [No], los usuarios invitados pueden acceder a la aplicación. Los administradores de aplicaciones deben conocer el impacto del control de acceso, especialmente si la aplicación contiene información confidencial. Para obtener más información, vea [Restricción de la aplicación de Azure AD a un conjunto de usuarios de un inquilino de Azure AD](../develop/howto-restrict-your-app-to-a-set-of-users.md). 

### <a name="terms-and-conditions"></a>Términos y condiciones

[Términos de uso de Azure Active Directory:](../conditional-access/terms-of-use.md) esta característica proporciona un método sencillo que las organizaciones pueden usar para presentar información a los usuarios finales. Puede usar términos de uso para exigir a los usuarios invitados que acepten los términos de uso antes de acceder a los recursos.

### <a name="licensing-considerations-for-guest-users-with-azure-ad-premium-features"></a>Consideraciones sobre las licencias para usuarios invitados con las características de Azure AD Premium

Los precios de Azure AD External Identities (usuario invitado) se basan en los usuarios activos mensuales (MAU). Los usuarios activos son el número de usuarios únicos con actividad de autenticación en un mes natural. La facturación de MAU le ayuda a reducir los costos al ofrecer un nivel gratis y precios flexibles y predecibles. Además, los primeros 50 000 MAU de cada mes son gratuitos para las características Premium P1 y Premium P2. Las características Premium incluyen las directivas de acceso condicional y Azure MFA para los usuarios invitados.

Para obtener más información, consulte [Modelo de facturación para Azure AD for External Identities](../external-identities/external-identities-pricing.md).

## <a name="office-365-considerations"></a>Consideraciones sobre Office 365

La siguiente información trata sobre Office 365 en el contexto de los escenarios de este documento. Puede consultar información detallada en [Colaboración entre inquilinos de Microsoft 365](/office365/enterprise/office-365-inter-tenant-collaboration).

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

Exchange Online limita cierta funcionalidad para los usuarios invitados. Estos límites se pueden reducir creando miembros externos en lugar de invitados externos. Sin embargo, en este momento no está disponible la siguiente funcionalidad para los usuarios externos. 

* Se puede asignar una licencia de Exchange Online a un usuario invitado. Sin embargo, no se puede emitir un token de Exchange Online para él. El resultado es que no puede acceder al recurso.

   * Los usuarios invitados no pueden usar buzones de Exchange Online compartidos o delegados en el inquilino del recurso.

   * Se puede asignar un usuario invitado a un buzón compartido, pero no puede acceder a él.

* Los usuarios invitados no deben estar ocultos para poder incluirlos en la lista LGD. De forma predeterminada, están ocultos.

   * Los usuarios invitados ocultos se crean en el momento de la invitación. La creación es independiente de que el usuario haya canjeado la invitación o no. Por tanto, si todos los usuarios invitados están ocultos, la lista incluye objetos de usuarios invitados que no han canjeado una invitación. En función del escenario, puede querer que aparezcan o no los objetos.

   * Los usuarios invitados se pueden mostrar con [Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) solamente. Puede ejecutar el cmdlet [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) de PowerShell para establecer la propiedad HiddenFromAddressListsEnabled con el valor $false.  
‎  
`‎Set-MailUser [GuestUserUPN] -HiddenFromAddressListsEnabled:$false` ‎  
‎Donde [GuestUserUPN] es el valor calculado de UserPrincipalName. Ejemplo:  
‎  
`‎Set-MailUser guestuser1_contoso.com#EXT#@fabricam.onmicrosoft.com -HiddenFromAddressListsEnabled:$false`

* Las actualizaciones de propiedades específicas de Exchange, como PrimarySmtpAddress, ExternalEmailAddress, EmailAddresses y MailTip, solo pueden establecerse con [Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true). El Centro de administración de Exchange Online no permite modificar los atributos en la GUI. 

Como ya se ha indicado, puede usar el cmdlet [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) de PowerShell para las propiedades específicas del correo. Con el cmdlet [Set-User](/powershell/module/exchange/set-user?view=exchange-ps&preserve-view=true) de PowerShell puede modificar muchas más propiedades de usuario. La mayoría de las propiedades se pueden modificar también con las API de Azure AD Graph.

### <a name="microsoft-sharepoint-online"></a>Microsoft SharePoint Online

SharePoint Online tiene sus propios permisos específicos del servicio en función de si el usuario es un miembro invitado en el inquilino de Azure Active Directory. 

Para obtener más información, vea [Uso compartido externo de Microsoft 365 y colaboración B2B de Azure Active Directory (Azure AD)](../external-identities/o365-external-user.md).

Después de habilitar el uso compartido externo en SharePoint Online, la funcionalidad de búsqueda de usuarios invitados en el selector de personas de SharePoint Online está desactivada de manera predeterminada. Esta configuración prohíbe que los usuarios invitados puedan detectarse cuando están ocultos en la lista LGD de Exchange Online. Puede hacer que los usuarios invitados estén visibles de dos maneras (que no son mutuamente excluyentes):

* Puede habilitar la búsqueda de usuarios invitados de varias formas:
   * Modificar el valor "ShowPeoplePickerSuggestionsForGuestUsers" en el inquilino y en la colección del sitio. 
   * Establecer la característica con los cmdlets [Set-SPOTenant](/powershell/module/sharepoint-online/Set-SPOTenant?view=sharepoint-ps&preserve-view=true) y [Set-SPOSite](/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps&preserve-view=true) de [SharePoint Online PowerShell](/powershell/sharepoint/sharepoint-online/connect-sharepoint-online?view=sharepoint-ps&preserve-view=true).  
‎

* Los usuarios invitados que están visibles en la lista LGD de Exchange Online también están visibles en el selector de usuarios de SharePoint Online. Las cuentas están visibles independientemente de la configuración de "ShowPeoplePickerSuggestionsForGuestUsers".

### <a name="microsoft-teams"></a>Microsoft Teams

Microsoft Teams tiene características para limitar el acceso en función del tipo de usuario. Los cambios en el tipo de usuario pueden afectar al acceso al contenido y a las características disponibles. 

* El mecanismo de "cambio de inquilino" para Microsoft Teams puede requerir que los usuarios cambien manualmente el contexto de su cliente de Teams cuando trabajen en esta plataforma fuera de su inquilino principal.

* Puede permitir que usuarios de Teams de otro dominio externo completo busquen, llamen, chateen y configuren reuniones con sus usuarios mediante la característica de federación de Teams. Para obtener más información, vea [Administrar el acceso externo en Microsoft Teams](/microsoftteams/manage-external-access). 

 

### <a name="licensing-considerations-for-guest-users-in-teams"></a>Consideraciones sobre las licencias para usuarios invitados en Teams

Cuando se utiliza Azure B2B con cargas de trabajo de Office 365, deben tenerse en cuenta algunos aspectos clave. Hay instancias en las que las cuentas de invitado no tienen la misma experiencia que una cuenta de miembro. 

**Grupos de Microsoft**: vea [Agregar invitados a grupos de Office 365](https://support.office.com/article/adding-guests-to-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) para conocer mejor la experiencia de la cuenta de invitado en grupos de Microsoft. 

**Microsoft Teams**: vea [Funcionalidades del propietario, miembro e invitado del equipo en Teams](https://support.office.com/article/team-owner-member-and-guest-capabilities-in-teams-d03fdf5b-1a6e-48e4-8e07-b13e1350ec7b?ui=en-US&rs=en-US&ad=US) para conocer mejor la experiencia de la cuenta de invitado en Microsoft Teams. 

Puede habilitar una experiencia de fidelidad completa en Teams usando miembros externos B2B. Office 365 aclaró recientemente su directiva de licencias para las organizaciones multiinquilino.

* Los usuarios con licencia en su inquilino principal pueden acceder a los recursos de otro inquilino que esté dentro de la misma entidad jurídica. El acceso se concede con la opción **Miembros externos**, sin cuotas de licencia adicionales. Esta configuración se aplica a SharePoint, OneDrive para la Empresa, Teams y grupos. 

   * Actualmente, los ingenieros está trabajando para automatizar la comprobación del estado de las licencias de un usuario en su inquilino principal y permitirle participar como miembro sin necesidad de asignar ni configurar licencias adicionales. Sin embargo, para los clientes que deseen usar miembros externos ahora, hay una solución alternativa para las licencias que requiere que el ejecutivo de cuentas trabaje con Microsoft Business Desk. 

   * Desde ahora hasta que esté disponible la solución de licencias automatizada, los clientes pueden usar una *licencia de prueba de Teams*. Esta licencia se le puede asignar a cada usuario del inquilino externo. Tiene una duración de un año y permite todas las cargas de trabajo enumeradas anteriormente.

   * Para los clientes que deseen convertir invitados B2B en miembros B2B, hay varios problemas conocidos con Microsoft Teams, como la incapacidad de crear nuevos canales y la capacidad de agregar aplicaciones a un equipo. 

* Las características de **Identity Governance** (administración de derechos, revisiones de acceso) pueden requerir otras licencias para los usuarios invitados o los miembros externos. Póngase en contacto con el equipo de su cuenta o con Business Desk para obtener una respuesta adecuada para su organización.

**Otros productos** (como Dynamics CRM) pueden requerir licencias en todos los inquilinos donde esté representado un usuario. Póngase en contacto con el equipo de su cuenta para obtener una respuesta adecuada para su organización.

## <a name="next-steps"></a>Pasos siguientes
[Introducción a la administración de usuarios multiinquilino](multi-tenant-user-management-introduction.md)

[Escenarios de administración de usuarios multiinquilino](multi-tenant-user-management-scenarios.md)

[Soluciones comunes para la administración de usuarios multiinquilino](multi-tenant-common-solutions.md)