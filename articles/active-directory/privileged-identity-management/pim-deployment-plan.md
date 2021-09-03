---
title: ¿Tiene pensado realizar una implementación de Privileged Identity Management? -Azure AD | Microsoft Docs
description: Obtenga información sobre cómo implementar Privileged Identity Management (PIM) en su organización de Azure AD.
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: martinco
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: baselden
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99bdfeff59f26f59c9d64bcca9226d9b1f70ec1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749079"
---
# <a name="plan-a-privileged-identity-management-deployment"></a>Planeación de una implementación de Privileged Identity Management

**Privileged Identity Management (PIM)** proporciona una activación de rol basada en tiempo y en aprobación para mitigar los riesgos de tener unos permisos de acceso excesivos, innecesarios o mal utilizados en los recursos importantes. Estos recursos incluyen los de Azure Active Directory (Azure AD), Azure y los de otros servicios de Microsoft Online Services, como Microsoft 365 o Microsoft Intune. También puede usar PIM con aplicaciones de servicio (SaaS).

PIM permite admitir un conjunto específico de acciones en un ámbito determinado. Características clave:

* Conceda privilegios de acceso **Just-In-Time** a los recursos

* Asigne la **idoneidad para pertenecer** a grupos de acceso con privilegios o poseerlos

* Asigne acceso **limitado** a los recursos con fechas de inicio y fin

* Requiera **aprobación** para activar los roles con privilegios

* Aplique la **autenticación multifactor** para activar cualquier rol

* Use la **justificación** para comprender por qué se activan los usuarios

* Obtenga **notificaciones** cuando se activan los roles con privilegios

* Lleve a cabo **revisiones de acceso** para asegurarse de que los usuarios siguen necesitando roles

* Descargue el **historial de auditoría** para la auditoría interna o externa

Para sacar el máximo partido de este plan de implementación, es importante que obtenga información general de [Qué es Privileged Identity Management](pim-configure.md).

## <a name="understand-pim"></a>Descripción de PIM

Los conceptos de PIM de esta sección le ayudarán a comprender los requisitos de Privileged Identity de su organización.

### <a name="what-can-you-manage-in-pim"></a>Qué puede administrar en PIM

En la actualidad, puede usar PIM con:

* **Roles de Azure AD**: en ocasiones llamados roles de directorio, los roles de Azure AD incluyen roles integrados y personalizados para administrar Azure AD y otros servicios en línea de Microsoft 365.

* **Roles de Azure**: roles de control de acceso basado en rol (RBAC) de Azure que conceden acceso a grupos de administración, suscripciones, grupos de recursos y recursos.

* **Grupos de acceso con privilegios**: para configurar el acceso Just-In-Time al rol de propietario o miembro de un grupo de seguridad de Azure AD. Los grupos de acceso con privilegios no solo le ofrecen un método alternativo para configurar PIM para roles de Azure AD y Azure, también le permiten configurar PIM para otros permisos en servicios en línea de Microsoft como Intune, Azure Key Vault y Azure Information Protection. 

Puede asignar lo siguiente a estos roles o grupos: 

* **Usuarios**: para obtener acceso Just-In-Time a los roles de Azure AD, los roles de Azure y los grupos de acceso con privilegios. 

* **Grupos**: cualquier miembro de un grupo para obtener acceso Just-In-Time a los roles de Azure AD y los roles de Azure. En el caso de los roles de Azure AD, el grupo debe ser un grupo en la nube recién creado marcado como asignable a un rol, mientras que en el caso de los roles de Azure, el grupo puede ser cualquier grupo de seguridad de Azure AD. No se recomienda asignar ni anidar un grupo a grupos de acceso con privilegios. 

> [!NOTE] 
>No puede asignar entidades de servicio como aptas para los roles de Azure AD, los roles de Azure y los grupos de acceso con privilegios, pero puede conceder una asignación activa de tiempo limitado a los tres.

### <a name="principle-of-least-privilege"></a>Principio de privilegio mínimo

Asigne a los usuarios el rol con los [privilegios mínimos necesarios para realizar sus tareas](../roles/delegate-by-task.md). Esta práctica minimiza el número de administradores globales, ya que en su lugar utiliza roles de administrador específicos para determinados escenarios.

> [!NOTE] 
> Microsoft tiene muy pocos administradores globales. Obtenga más información sobre [cómo usa Microsoft la característica Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

### <a name="type-of-assignments"></a>Tipo de asignaciones 

Hay dos tipos de asignación: **válido** y **activo**. Si un usuario es apto para un rol, eso significa que puede activarlo cuando necesite para realizar tareas con privilegios. 

Ahora también puede establecer una hora de inicio y finalización para cada tipo de asignación. Esta adición proporciona cuatro tipos de asignaciones posibles:

* Válido permanente

* Activo permanente

* Válido temporal, con las fechas de inicio y finalización especificadas para la asignación

* Activo temporal, con las fechas de inicio y finalización especificadas para la asignación

En caso de que expire el rol, puede **ampliar** o **renovar** estas asignaciones. 

**Recomendamos** tener cero asignaciones activas permanentemente para roles aparte de las [dos cuentas de acceso para escenarios de máxima emergencia](../roles/security-emergency-access.md) recomendadas, que deben tener el rol de administrador global permanente. 

## <a name="plan-the-project"></a>Planeamiento del proyecto

Cuando fracasan los proyectos tecnológicos, normalmente se debe a expectativas incorrectas relacionadas con el impacto, los resultados y las responsabilidades. Para evitar estos problemas, [asegúrese de que involucra a las partes interesadas adecuadas](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) y que estas conocen bien sus roles.

### <a name="plan-a-pilot"></a>Planeamiento de un piloto

En cada fase de la implementación, asegúrese de que está evaluando que los resultados son los esperados. Consulte [Procedimientos recomendados para un piloto](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot).

* Empiece por un conjunto de usuarios pequeño (grupo piloto) y verifique que PIM se comporta según lo previsto.

* Compruebe si toda la configuración que estableció para los roles o grupos de acceso con privilegios funciona correctamente. 

* Revierta a producción solo tras realizarse pruebas exhaustivas. 

### <a name="plan-communications"></a>Planeamiento de las comunicaciones

La comunicación es fundamental para el éxito de cualquier servicio nuevo. Comunique de forma proactiva a los usuarios cómo y cuándo va a cambiar su experiencia, y cómo obtener soporte técnico si tienen cualquier problema.

Dedique tiempo al equipo de soporte técnico de TI interno para ofrecerle orientación sobre el flujo de trabajo de PIM. Debe proporcionarle los documentos apropiados y su información de contacto.

## <a name="plan-testing-and-rollback"></a>Planeamiento de pruebas y reversión

> [!NOTE] 
> En el caso de los roles de Azure AD, las organizaciones suelen probar e implementar los administradores globales primero, mientras que en el caso de los recursos de Azure, normalmente prueban PIM en una suscripción de Azure cada vez. 

### <a name="plan-testing"></a>Planeamiento de pruebas

Cree usuarios de prueba para verificar que la configuración de PIM funciona según lo previsto antes de que afecte a usuarios reales y pueda impedir su acceso a aplicaciones y recursos. Cree un plan de pruebas para tener una comparación entre los resultados previstos y los reales. 

En la tabla siguiente se muestra un caso de prueba de ejemplo: 

| Role| Comportamiento esperado durante la activación| Resultados reales |
| --- | --- | --- |
|Administrador global| <li> Requerir MFA <br><li>  Requerir aprobación <br><li>  El aprobador recibe la notificación y puede aprobar <br><li>  El rol expira después del tiempo preestablecido|

En el caso del rol de recursos de Azure y de Azure AD, asegúrese de tener representados usuarios que asumirán esos roles. Además, tenga en cuenta los siguientes roles al probar PIM en el entorno de ensayo:

| Roles| Roles de Azure AD| Roles de recursos de Azure| Grupos de acceso con privilegios |
| --- | --- | --- |--- |
| Miembro de un grupo| | | x |
| Miembros de un rol| x| x|  |
| Propietario del servicio de TI| x| | x |
| Propietario de recurso o suscripción| | x| x |
| Propietario de un grupo de acceso con privilegios| | | x |

### <a name="plan-rollback"></a>Planeamiento de la reversión

Si PIM no funciona según lo previsto en el entorno de producción, puede cambiar la asignación de roles de válida a activa una vez más. Para cada rol que haya configurado, seleccione los puntos suspensivos (...) correspondientes a todos los usuarios cuyo tipo de asignación sea **válido**. Después, puede seleccionar la opción **Activar** para volver atrás y hacer que la asignación de roles sea **activa**.

## <a name="plan-and-implement-pim-for-azure-ad-roles"></a>Planeamiento e implementación de PIM para los roles de Azure AD

Siga estas tareas para preparar PIM para administrar roles de Azure AD. 

### <a name="discover-and-mitigate-privileged-roles"></a>Detección y mitigación de roles con privilegios

Especifique quién tiene roles con privilegios en su organización. Revise los usuarios asignados, identifique a los administradores que ya no necesiten el rol y quítelos de sus asignaciones. 

Puede usar las [revisiones de acceso de roles de Azure AD](pim-how-to-start-security-review.md) para automatizar la detección, revisión y aprobación o eliminación de asignaciones.

### <a name="determine-roles-to-be-managed-by-pim"></a>Determinación de los roles que va a administrar PIM

Priorice la protección de los roles de Azure AD que tienen la mayoría de permisos. También es importante tener en cuenta qué datos y permisos son más sensibles para su organización. 

En primer lugar, asegúrese de que todos los roles de administrador global y de seguridad se administran mediante PIM, ya que son los usuarios que pueden hacer causar más daños al vulnerar su seguridad. A continuación, considere más roles que tendrían que administrarse y que podrían ser vulnerables a ataques.

### <a name="configure-pim-settings-for-azure-ad-roles"></a>Configuración de las opciones de PIM para roles de Azure AD

[Haga un borrador de las opciones de PIM y configúrelas](pim-how-to-change-default-settings.md) para cada rol de Azure AD con privilegios que use su organización. 

En la siguiente tabla se muestra la configuración de ejemplo:

| Role| Requerir MFA| Notification| Vale de incidente| Requerir aprobación| Aprobador| Duración de la activación| Administrador permanente |
| --- | --- | --- |--- |--- |--- |--- |--- |
| Administrador global| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Otro administrador global| 1 hora| Cuentas de acceso de emergencia |
| Administración de Exchange| :heavy_check_mark:| :heavy_check_mark:| :x:| :x:| None| 2 horas| None |
| Administrador del departamento de soporte técnico| :x:| :x:| :heavy_check_mark:| :x:| None| 8 horas| None |


### <a name="assign-and-activate-azure-ad-roles"></a>Asignación y activación de roles de Azure AD 

En el caso de los roles de Azure AD en PIM, los usuarios que tengan el rol Administrador de roles con privilegios o el rol Administrador global son los únicos que pueden administrar las asignaciones de otros administradores. Los administradores globales, administradores de seguridad, lectores globales y lectores de seguridad pueden ver también las asignaciones a roles de Azure AD en PIM. 

Siga las instrucciones del vínculo que se incluye a continuación:

1.[Proporcione asignaciones válidas](pim-how-to-add-role-to-user.md).

2.[Permita que los usuarios elegibles activen su rol de Azure AD cuando sea necesario](pim-how-to-activate-role.md).

Cuando la expiración del rol esté cerca, use [PIM para ampliar o renovar los roles](pim-resource-roles-renew-extend.md). Ambas acciones iniciadas por el usuario requieren una aprobación de un administrador global o un administrador de roles con privilegios.  Ambas acciones iniciadas por el usuario requieren una aprobación de un administrador global o un administrador de roles con privilegios. 

Cuando estos eventos importantes se producen en los roles de Azure AD, PIM [envía notificaciones por correo electrónico y correos electrónicos de resumen semanales](pim-email-notifications.md) a administradores con privilegios dependiendo de la configuración de roles, eventos y notificaciones. Estos mensajes de correo electrónico también podrían incluir vínculos a tareas pertinentes, tales como la activación o renovación de un rol. 

> [!NOTE] 
>También puede realizar estas tareas de PIM [mediante las API de Microsoft Graph para roles de Azure AD](pim-apis.md). 

### <a name="approve-or-deny-pim-activation-requests"></a>Aprobación o denegación de solicitudes de activación de PIM 

Un aprobador delegado recibe una notificación por correo electrónico cuando una solicitud está pendiente de aprobación. Siga estos pasos para [aprobar o denegar solicitudes para activar un rol de recursos de Azure](pim-resource-roles-approval-workflow.md).

### <a name="view-audit-history-for-azure-ad-roles"></a>Visualización del historial de auditoría de los roles de Azure AD

[Vea el historial de auditoría de todas las asignaciones de roles y activaciones](pim-how-to-use-audit-log.md) en los últimos 30 días de los roles de Azure AD. Puede acceder a los registros de auditoría si es un administrador global o un administrador de roles con privilegios. 

**Recomendamos** tener al menos un administrador que lea todos los eventos de auditoría cada semana y los exporte una vez al mes.

### <a name="security-alerts-for-azure-ad-roles"></a>Alertas de seguridad para roles de Azure AD

[Configure alertas de seguridad para los roles de Azure AD](pim-how-to-configure-security-alerts.md) que desencadenarán una alerta en caso de actividad sospechosa o no segura.

## <a name="plan-and-implement-pim-for-azure-resource-roles"></a>Planeamiento e implementación de PIM para roles de recursos de Azure

Siga estas tareas para preparar PIM para administrar roles de recursos de Azure.

### <a name="discover-and-mitigate-privileged-roles"></a>Detección y mitigación de roles con privilegios

Minimice las asignaciones de administrador de acceso de usuario y propietario conectadas a cada suscripción o recurso y quite las asignaciones innecesarias.

Como administrador global puede [elevar el privilegio de acceso para administrar todas las suscripciones de Azure](../../role-based-access-control/elevate-access-global-admin.md). A continuación, puede encontrar a cada propietario de la suscripción y trabajar con él para quitar las asignaciones innecesarias dentro de sus suscripciones.

Use las [revisiones de acceso para recursos de Azure](pim-resource-roles-start-access-review.md) a fin de auditar y quitar las asignaciones de roles innecesarias. 

### <a name="determine-roles-to-be-managed-by-pim"></a>Determinación de los roles que va a administrar PIM

Al decidir qué asignaciones de roles deben administrarse mediante PIM para recursos de Azure, primero debe identificar los [grupos de administración](../../governance/management-groups/overview.md), las suscripciones, los grupos de recursos y los recursos que son más vitales para su organización. Considere la posibilidad de usar grupos de administración para organizar todos sus recursos dentro de su organización.

**Recomendamos** administrar todos los roles de administrador de acceso de usuario y propietario de la suscripción mediante PIM. 

Trabaje con los propietarios de la suscripción para documentar los recursos administrados por cada suscripción y clasificar el nivel de riesgo de cada recurso si se pone en riesgo. Priorice la administración de recursos con PIM según el nivel de riesgo. Esto también incluye los recursos personalizados asociados a la suscripción.

**También recomendamos** colaborar con los propietarios de las suscripciones y recursos de servicios críticos para configurar el flujo de trabajo de PIM de todos los roles dentro de suscripciones o recursos confidenciales.

En el caso de las suscripciones o los recursos que no son tan críticos, no necesitará configurar PIM para todos los roles. Sin embargo, todavía debe proteger los roles de administrador de acceso de usuario y propietario con PIM.

### <a name="configure-pim-settings-for-azure-resource-roles"></a>Configuración de las opciones de PIM para roles de recursos de Azure

[Haga un borrador de las opciones](pim-resource-roles-configure-role-settings.md) para los roles de recursos de Azure que tiene previsto proteger con PIM. 

En la siguiente tabla se muestra la configuración de ejemplo:

| Role| Requerir MFA| Notification| Requerir aprobación| Aprobador| Duración de la activación| Administrador activo| Expiración de rol activo| Expiración de rol válido|
| --- | --- | --- |--- |--- |--- |--- |---|---|
| Propietario de las suscripciones críticas| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Otros propietarios de la suscripción| 1 hora| None| N/D| 3 meses |
| Administrador de acceso de usuario de suscripciones menos críticas| :heavy_check_mark:| :heavy_check_mark:| :x:| None| 1 hora| None| N/D| 3 meses |

### <a name="assign-and-activate-azure-resource-role"></a>Asignación y activación de roles de recursos de Azure

En el caso de los roles de recursos de Azure en PIM, solo un administrador de acceso de usuario o propietario puede administrar las asignaciones de otros administradores. De forma predeterminada, los usuarios que son administradores de roles con privilegios, administradores de seguridad o lectores de seguridad no tienen acceso para ver asignaciones a roles de recursos de Azure.

Siga las instrucciones del vínculo que se incluye a continuación:

1.[Proporcione asignaciones válidas](pim-resource-roles-assign-roles.md).

2.[Permita que los usuarios elegibles activen sus roles de Azure cuando sea necesario](pim-resource-roles-activate-your-roles.md).

Cuando la expiración de la asignación de roles con privilegios esté cerca, [use PIM para ampliar o renovar los roles](pim-resource-roles-renew-extend.md). Ambas acciones iniciadas por el usuario requieren una aprobación del administrador de acceso de usuario o propietario del recurso. 

Cuando estos eventos importantes se producen en los roles de recursos de Azure, PIM envía [notificaciones por correo electrónico](pim-email-notifications.md) a administradores de acceso de usuario y propietarios. Estos mensajes de correo electrónico también podrían incluir vínculos a tareas pertinentes, tales como la activación o renovación de un rol.

>[!NOTE]
>También puede realizar estas tareas de PIM [mediante las API de Microsoft Azure Resource Manager para roles de recursos de Azure](pim-apis.md). 

### <a name="approve-or-deny-pim-activation-requests"></a>Aprobación o denegación de solicitudes de activación de PIM

[Aprobación o denegación de solicitudes de activación para roles de Azure AD](azure-ad-pim-approval-workflow.md): un aprobador delegado recibe una notificación por correo electrónico cuando una solicitud está pendiente de aprobación.

### <a name="view-audit-history-for-azure-resource-roles"></a>Visualización del historial de auditoría para los roles de recursos de Azure

[Vea el historial de auditoría de todas las asignaciones y activaciones](azure-pim-resource-rbac.md) en los últimos 30 días de los roles de recursos de Azure.

### <a name="security-alerts-for-azure-resource-roles"></a>Alertas de seguridad para roles de recursos de Azure

[Configure alertas de seguridad para los roles de recursos de Azure](pim-resource-roles-configure-alerts.md) que desencadenarán una alerta en caso de actividad sospechosa o no segura.

## <a name="plan-and-implement-pim-for-privileged-access-groups"></a>Planeamiento e implementación de PIM para grupos de acceso con privilegios

Siga estas tareas para preparar PIM para administrar grupos de acceso con privilegios.

### <a name="discover-privileged-access-groups"></a>Detección de grupos de acceso con privilegios

Puede darse el caso de que una persona tenga cinco o seis asignaciones elegibles a los roles de Azure AD mediante PIM. Tendrá que activar cada rol individualmente, lo que puede reducir la productividad. Peor aún, también puede tener decenas o cientos de recursos de Azure asignados, lo que agrava el problema.

En este caso, debe usar grupos de acceso con privilegios. Cree un grupo de acceso con privilegios y concédale acceso activo permanente a varios roles. Consulte el artículo sobre las [funcionalidades de administración de grupos de acceso con privilegios](groups-features.md).

Para administrar un grupo al que se pueden asignar roles de Azure AD como grupo de acceso con privilegios, [incorpórelo a la administración de PIM](groups-discover-groups.md).

### <a name="configure-pim-settings-for-privileged-access-groups"></a>Configuración de las opciones de PIM para grupos de acceso con privilegios

[Haga un borrador de las opciones y configúrelas](groups-role-settings.md) para los grupos de acceso con privilegios que tiene previsto proteger con PIM.

En la siguiente tabla se muestra la configuración de ejemplo:

| Role| Requerir MFA| Notification| Requerir aprobación| Aprobador| Duración de la activación| Administrador activo| Expiración de rol activo| Expiración de rol válido |
| --- | --- | --- |--- |--- |--- |--- |---|---|
| Propietario| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Otros propietarios del recurso| 1 hora| None| N/D| 3 meses |
| Miembro| :heavy_check_mark:| :heavy_check_mark:| :x:| None| 5 horas| None| N/D| 3 meses |

### <a name="assign-eligibility-for-privileged-access-groups"></a>Asignación de la elegibilidad para grupos de acceso con privilegios

Puede [asignar la elegibilidad a los miembros o propietarios de los grupos de acceso con privilegios.](groups-assign-member-owner.md) Con solo una activación, tendrá acceso a todos los recursos vinculados. 

>[!NOTE] 
>Puede asignar el grupo con privilegios a uno o varios roles de recursos de Azure y Azure AD de la misma manera que asigna roles a los usuarios. Se puede crear un máximo de 250 grupos a los que se puedan asignar roles en una sola organización de Azure AD (inquilino).

![Asignación de la elegibilidad para grupos de acceso con privilegios](media/pim-deployment-plan/privileged-access-groups.png)


Cuando la expiración de la asignación de grupos con privilegios esté cerca, use [PIM para ampliar o renovar la asignación de grupos](groups-renew-extend.md). Necesitará una aprobación del propietario del grupo.

### <a name="approve-or-deny-pim-activation-request"></a>Aprobación o denegación de la solicitud de activación de PIM

Configure propietarios o miembros de un grupo de acceso con privilegios para requerir la aprobación de la activación y elegir usuarios o grupos de la organización de Azure AD como aprobadores delegados. Se recomienda seleccionar dos o más aprobadores para cada grupo a fin de reducir la carga de trabajo del administrador de roles con privilegios. 

[Aprobación o denegación de solicitudes de activación de rol para grupos de acceso con privilegios](groups-approval-workflow.md). Como un aprobador delegado, recibirá notificaciones por correo electrónico cuando una solicitud está pendiente de aprobación.

### <a name="view-audit-history-for-privileged-access-groups"></a>Visualización del historial de auditoría para los grupos de acceso con privilegios

[Vea el historial de auditoría de todas las asignaciones y activaciones](groups-audit.md) en los últimos 30 días de los grupos de acceso con privilegios.

## <a name="next-steps"></a>Pasos siguientes

* Si hay problemas relacionados con PIM, consulte el artículo sobre cómo [solucionar un problema con PIM](pim-troubleshoot.md).

* [Implementación de otras características de identidad](../fundamentals/active-directory-deployment-plans.md)

 

