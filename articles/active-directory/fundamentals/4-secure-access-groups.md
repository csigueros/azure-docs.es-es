---
title: Protección del acceso externo mediante grupos en Azure Active Directory y Microsoft 365
description: Azure Active Directory y los grupos de Microsoft 365 se pueden usar para reforzar la seguridad cuando los usuarios externos acceden a los recursos.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2500d1872c2c70c8d39afa0b564c35a8bb9bcb9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747873"
---
# <a name="securing-external-access-with-groups"></a>Protección del acceso externo mediante grupos 

Los grupos son una parte esencial de cualquier estrategia de control de acceso. Los grupos de seguridad de Azure Active Directory (Azure AD) y los grupos de Microsoft 365 se pueden usar como base para proteger el acceso a los recursos.

Los grupos son la mejor alternativa para usar como base con los siguientes mecanismos de control de acceso:

* Directivas de acceso condicional

* Paquete de acceso de administración de derechos 

* Acceso a recursos de Microsoft 365, Microsoft Teams y sitios de SharePoint

Los grupos tienen los siguientes roles:

* Propietarios: los propietarios de grupos administran la configuración del grupo y la pertenencia al mismo.

* Miembros: los miembros que heredan los permisos y el acceso que se han asignado al grupo.

* Invitados: los invitados son miembros de fuera de la organización. 

## <a name="determine-your-group-strategy"></a>Determinación de la estrategia de grupo

A medida que desarrolle su estrategia de grupo para proteger el acceso externo a los recursos, tenga en cuenta cuál es su [posición de seguridad deseada](1-secure-access-posture.md) para determinar los siguientes aspectos.

* **¿Quién debería poder crear grupos?** ¿Desea que solo los administradores creen grupos o prefiere que los empleados o los usuarios externos puedan crearlos también?

   * *Cualquier miembro del inquilino puede crear grupos de seguridad de Azure AD de forma predeterminada*. 

      * Es posible [restringir el acceso al portal para los usuarios que no sean administradores](../develop/howto-restrict-your-app-to-a-set-of-users.md) y deshabilitar la posibilidad de crear grupos en [PowerShell](../enterprise-users/groups-troubleshooting.md). 

      * También se puede [configurar la administración de grupos de autoservicio en Azure Active Directory](../enterprise-users/groups-self-service-management.md). 

   * *De forma predeterminada, todos los usuarios pueden crear grupos de Microsoft 365 y los grupos están abiertos para que se unan todos los usuarios (internos y externos) del inquilino*. 

      * La [creación de grupos de Microsoft 365 puede restringirse](/microsoft-365/solutions/manage-creation-of-groups) a los miembros de un grupo de seguridad determinado. Para configurar esta opción, use Windows PowerShell. 

* **¿Quién debería poder invitar a los usuarios a grupos?** ¿Deberían todos los miembros del grupo poder agregar a otros miembros o solo los propietarios de grupos?

* **¿A quién se puede agregar a los grupos?** Los usuarios externos se pueden agregar a los grupos de forma predeterminada. 

### <a name="assign-users-to-groups"></a>Asignar usuarios a grupos

Los usuarios se pueden asignar a grupos de forma manual en función de los atributos de usuario del objeto de usuario, o bien con arreglo a otros criterios. Los usuarios solo se pueden asignar a los grupos de forma dinámica según sus atributos.

Por ejemplo, puede asignar usuarios a grupos en función de los siguientes criterios:

* departamento o puesto de trabajo específicos

* organización asociada a la que pertenecen (asignación manual o a través de organizaciones conectadas)

* tipo de usuario (miembro o invitado)

* participación en un proyecto específico (asignación manual)

* ubicación

Los grupos dinámicos pueden contener usuarios o dispositivos, pero no ambos. Agregue consultas basadas en atributos de usuario para asignar usuarios al grupo dinámico. El ejemplo siguiente muestra consultas que agregan usuarios al grupo si son miembros (no invitados) del departamento financiero.

![Captura de pantalla que muestra la configuración de reglas de pertenencia dinámica.](media/secure-external-access/4-dynamic-membership-rules.png)

Para obtener más información sobre los grupos dinámicos, consulte [Creación o actualización de un grupo dinámico en Azure Active Directory](../enterprise-users/groups-create-rule.md).

### <a name="do-not-use-groups-for-multiple-purposes"></a>Se recomienda no usar los grupos para varios propósitos

Al usar grupos con fines de seguridad o de acceso a los recursos, es importante que cumplan una única función. Si se usa un grupo para otorgar acceso a los recursos, no debería utilizarse con ningún otro propósito. Si un grupo se usa para fines genéricos, como definir la ubicación o la pertenencia a un equipo, no se debería usar también para proteger el acceso. 

Se recomienda adoptar una convención de nomenclatura que permita identificar claramente la finalidad de los grupos de seguridad. Por ejemplo:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Tipos de grupos

Tanto los grupos de seguridad de Azure AD como los grupos de Microsoft 365 se crean en el portal de Azure AD o en el portal de administración de M365. Ambos tipos se pueden usar como base para proteger el acceso externo:

|Consideraciones | Grupos de seguridad de Azure AD (manuales y dinámicos)| Grupos de Microsoft 365 |
| - | - | - |
| ¿Qué puede contener el grupo?| Usuarios<br>Grupos<br>Principios de servicio<br>Dispositivos| Solo usuarios |
| ¿Dónde se crea el grupo?| Portal de Azure AD<br>Portal de Microsoft 365 (si se va a habilitar para el correo)<br>PowerShell<br>Microsoft Graph<br>Portal del usuario final| Portal de Microsoft 365<br>Portal de Azure AD<br>PowerShell<br>Microsoft Graph<br>Aplicaciones de Microsoft 365 |
| ¿Quién los puede crea de forma predeterminada?| Administradores <br>Usuarios finales| Administradores<br>Usuarios finales |
| ¿A quién se puede agregar de forma predeterminada?| Usuarios internos (miembros)| Miembros de inquilino e invitados de cualquier organización |
| ¿A qué proporcionan acceso?| Solo a los recursos que tiene asignados.| A todos los recursos relacionados con el grupo:<br>Buzón de correo, sitio, equipo, chats y otros recursos del grupo de Microsoft 365<br>Cualquier otro recurso al que se agregue el grupo |
| Se puede usar con| Acceso condicional<br>Administración de derechos<br>Licencias de grupo| Acceso condicional<br>Administración de derechos<br>Etiquetas de confidencialidad |



Use los grupos de Microsoft 365 para crear y administrar un conjunto de recursos Microsoft 365 (por ejemplo, Teams, junto con su contenido y sitios asociados). Son una excelente alternativa para las iniciativas basadas en proyectos. 

 

## <a name="azure-ad-security-groups"></a>Grupos de seguridad de Azure AD 

Los [grupos de seguridad de Azure AD](./active-directory-manage-groups.md) pueden contener usuarios o dispositivos y se pueden usar para administrar el acceso a: 

* Recursos de Azure, como aplicaciones de Microsoft 365, aplicaciones personalizadas y aplicaciones de software como servicio (SaaS) como ServiceNow de Dropbox.

* Datos y suscripciones de Azure.

* Servicios de Azure.

Los grupos de seguridad de Azure AD también se pueden usar para:

* Asignar licencias a servicios como Microsoft 365, Dynamics 365 y Enterprise Mobility y Security. Para obtener más información, consulte las [licencias basadas en grupos](./active-directory-licensing-whatis-azure-portal.md).

* Asignar permisos elevados. Para obtener más información, consulte [Uso de grupos para administrar asignaciones de roles en Azure Active Directory](../roles/groups-concept.md). 

Para crear un grupo en [Azure Portal](./active-directory-groups-create-azure-portal.md), vaya a Azure Active Directory y, a continuación, a Grupos. También puede crear grupos de seguridad de Azure AD mediante [cmdlets de PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md). 

> [!NOTE]
> Es posible usar un grupo de seguridad para asignar hasta 1500 aplicaciones, pero no más. 

![Captura de pantalla que muestra cómo crear un grupo de seguridad.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Para crear un grupo de seguridad habilitado para el correo electrónico, vaya al [Centro de administración de Microsoft 365](https://admin.microsoft.com/)** . No podrá crearlo en el portal de Azure AD. 
<br>Es necesario habilitar el grupo de seguridad para el correo en el momento de crearlo. No se puede hacer posteriormente.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Organizaciones híbridas y grupos de seguridad de Azure AD

Las organizaciones híbridas tienen una infraestructura local y una infraestructura en la nube de Azure AD. Muchas organizaciones híbridas que usan Active Directory crean sus grupos de seguridad locales y los sincronizan con la nube. Con este método, solo se pueden agregar usuarios del entorno local a los grupos de seguridad.

**Proteja su infraestructura local contra los riesgos, ya que una vulneración de seguridad del entorno local podría aprovecharse para acceder al inquilino de Microsoft 365 de su empresa**. Consulte el artículo sobre [cómo proteger Microsoft 365 contra ataques al entorno local](./protect-m365-from-on-premises-attacks.md) para obtener orientación.

## <a name="microsoft-365-groups"></a>Grupos de Microsoft 365

Los [grupos de Microsoft 365](/microsoft-365/admin/create-groups/office-365-groups) son un servicio de pertenencia básico que regula todo el acceso a través de Microsoft 365. Se pueden crear desde [Azure Portal](https://portal.azure.com/) o el [portal de Microsoft 365](https://admin.microsoft.com/). Al crear un grupo de Microsoft 365, concede acceso a un grupo de recursos que se usan con fines de colaboración. Consulte [Información general de los grupos de Microsoft 365 para administradores](/microsoft-365/admin/create-groups/office-365-groups) y obtenga una lista completa de estos recursos.

Los grupos de Microsoft 365 tienen las siguientes características para sus roles:

* **Propietarios**. Los propietarios de grupos pueden agregar o quitar miembros y tener permisos únicos, como la posibilidad de eliminar conversaciones de la bandeja de entrada compartida o cambiar la configuración del grupo. Los propietarios de grupos pueden cambiar el nombre del grupo, actualizar la descripción o la imagen, etc.

* **Miembros**. Los miembros pueden acceder a todos los recursos del grupo, pero no pueden cambiar la configuración del grupo. De forma predeterminada, los miembros del grupo pueden agregar invitados al grupo, aunque el propietario puede [controlar esa configuración](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Invitados**. Los invitados de grupos son miembros que no pertenecen a su organización. Los invitados tienen algunas restricciones, de forma predeterminada, en cuanto a la funcionalidad de Teams.

 

### <a name="microsoft-365-group-settings"></a>Configuración de grupos de Microsoft 365

Durante la configuración, especifique un alias de correo electrónico, una opción de privacidad y si desea habilitar el grupo para equipos. 

![Captura de pantalla que muestra cómo editar la configuración de un grupo de Microsoft 365.](media/secure-external-access/4-edit-group-settings.png)

Después de la instalación, agregue miembros y configure las opciones para el uso del correo electrónico, etc.

### <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre cómo proteger el acceso externo a los recursos. Se recomienda realizar las acciones en el orden indicado.

1. [Determinación de la posición de seguridad deseada para el acceso externo](1-secure-access-posture.md)

2. [Detección del estado actual](2-secure-access-current-state.md)

3. [Creación de un plan de gobernanza](3-secure-access-plan.md)

4. [Uso de grupos con fines de seguridad](4-secure-access-groups.md) (usted está aquí).

5. [Transición a Azure AD B2B](5-secure-access-b2b.md)

6. [Acceso seguro mediante la administración de derechos](6-secure-access-entitlement-managment.md)

7. [Acceso seguro mediante directivas de acceso condicional](7-secure-access-conditional-access.md)

8. [Acceso seguro mediante etiquetas de confidencialidad](8-secure-access-sensitivity-labels.md)

9. [Acceso seguro a Microsoft Teams, OneDrive y SharePoint](9-secure-access-teams-sharepoint.md)