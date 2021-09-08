---
title: 'Configuración de la separación de tareas de un paquete de acceso en la administración de derechos de Azure AD: Azure Active Directory'
description: Aprenda a configurar el cumplimiento de las solicitudes de un paquete de acceso en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/2/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 171901de8f9037ee6e77f83138ec7a3065c2d71f
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730176"
---
# <a name="configure-separation-of-duties-checks-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Configuración de la comprobación de separación de tareas de un paquete de acceso en la administración de derechos de Azure AD (versión preliminar)

En la administración de derechos de Azure AD puede configurar varias directivas con diferentes configuraciones para cada comunidad de usuarios que necesitará acceso mediante un paquete de acceso.  Por ejemplo, es posible que los empleados solo necesiten la aprobación del administrador para obtener acceso a determinadas aplicaciones, pero los invitados procedentes de otras organizaciones pueden requerir un patrocinador y un jefe de departamento del equipo de recursos para obtener la aprobación. En las directivas para los usuarios que ya están en el directorio, puede especificar el grupo determinado de usuarios para el que se puede solicitar acceso. Pero puede que tenga un requisito para evitar que los usuarios obtengan acceso excesivo.  Para cumplir este requisito, querrá restringir aún más quién puede solicitar acceso en función del acceso que ya tenga el solicitante.

Con la configuración de separación de tareas en un paquete de acceso, puede configurar que un usuario que sea miembro de un grupo o que ya tenga una asignación a un paquete de acceso no pueda solicitar un paquete de acceso adicional.

![Experiencia myaccess para intentar solicitar acceso incompatible](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## <a name="scenarios-for-separation-of-duties-checks"></a>Escenarios de separación de comprobaciones de obligaciones

Por ejemplo, tiene un paquete de acceso, *Campaña de marketing*, al que pueden solicitar acceso personas de la organización y de otras organizaciones para trabajar con el departamento de marketing de la organización mientras esa campaña está en marcha. Puesto que los empleados del departamento de marketing ya deben tener acceso a ese material de campaña de marketing, no quiere que los empleados del departamento de marketing soliciten acceso a ese paquete de acceso.  O bien, es posible que ya tenga un grupo dinámico, *Empleados del departamento de marketing*, en el que están todos los empleados de marketing. Podría indicar que el paquete de acceso no es compatible con la pertenencia a ese grupo dinámico. Entonces, si un empleado del departamento de marketing busca un paquete de acceso para solicitarlo, no podría solicitar acceso al paquete de acceso de la *Campaña de marketing*.

Del mismo modo, puede tener una aplicación con dos roles (**Ventas del Oeste** y **Ventas del Este**) y quiere asegurarse de que un usuario solo puede tener un territorio de ventas a la vez.  Si tiene dos paquetes de acceso, el paquete de acceso **Territorio Oeste** que proporciona el rol **Ventas del Oeste** y el paquete de acceso **Territorio Este** que proporciona el rol **Ventas del Este**, puede configurar
 - el paquete de acceso **Territorio Oeste** para que no sea compatible con el paquete **Territorio Este**, y
 - el paquete de acceso **Territorio Este** para que no sea compatible con el paquete **Territorio Oeste**.

Si ha estado usando Microsoft Identity Manager u otros sistemas de administración de identidades locales para automatizar el acceso a las aplicaciones locales, también puede integrar estos sistemas con la administración de derechos de Azure AD.  Si va a controlar el acceso a las aplicaciones integradas de Azure AD con la administración de derechos y quiere evitar que los usuarios tengan acceso incompatible, puede configurar que un paquete de acceso no sea compatible con un grupo. Podría ser un grupo que el sistema de administración de identidades local envía a Azure AD mediante Azure AD Connect. Esta comprobación garantiza que un usuario no podrá solicitar un paquete de acceso si ese paquete de acceso daría un acceso que no es compatible con el que el usuario tiene en las aplicaciones locales.

## <a name="prerequisites"></a>Prerrequisitos

Para usar la administración de derechos de Azure AD y asignar usuario a los paquetes de acceso, debe tener una de las licencias siguientes:

- Azure AD Premium P2
- Licencia de Enterprise Mobility + Security (EMS) E5

## <a name="configure-another-access-package-or-group-membership-as-incompatible-for-requesting-access-to-an-access-package"></a>Configuración de otro paquete de acceso o pertenencia a grupos como incompatible para solicitar acceso a un paquete de acceso

**Requisitos previos de rol**: Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo o Administrador de paquetes de acceso.

Siga estos pasos para cambiar la lista de grupos incompatibles u otros paquetes de acceso para un paquete de acceso existente:

1.  Inicie sesión en [Azure Portal](https://portal.azure.com).

1.  Haga clic en **Azure Active Directory** y, luego, haga clic en **Gobernanza de identidades**.

1.  En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso que los usuarios solicitarán.

1.  En el menú de la izquierda, haga clic en **Separation of duties (preview)** [Separación de tareas (versión preliminar)].

1.  Si quiere evitar que los usuarios que ya tienen otra asignación de paquete de acceso soliciten este paquete de acceso, haga clic en **Agregar paquete de acceso** y seleccione el paquete de acceso que el usuario ya tiene asignado.


    ![configuración de paquetes de acceso incompatibles](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  Si quiere evitar que los usuarios que tienen una pertenencia a un grupo existente soliciten este paquete de acceso, haga clic en **Agregar grupo** y seleccione el grupo al que ya pertenece el usuario.

### <a name="configure-incompatible-access-packages-programmatically"></a>Configuración de paquetes de acceso incompatibles mediante programación

También puede configurar los grupos y otros paquetes de acceso que no son compatibles con el paquete de acceso mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para agregar, eliminar y enumerar los grupos incompatibles y los paquetes de acceso [de un paquete de acceso](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true).


## <a name="view-other-access-packages-that-are-configured-as-incompatible-with-this-one"></a>Visualización de otros paquetes de acceso configurados como incompatibles con este paquete de acceso

**Requisitos previos de rol**: Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo o Administrador de paquetes de acceso.

Siga estos pasos para ver la lista de otros paquetes de acceso que han indicado que no son compatibles con un paquete de acceso existente:

1.  Inicie sesión en [Azure Portal](https://portal.azure.com).

1.  Haga clic en **Azure Active Directory** y, luego, haga clic en **Gobernanza de identidades**.

1.  En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1.  En el menú de la izquierda, haga clic en **Separation of duties (preview)** [Separación de tareas (versión preliminar)].

1. Haga clic en **Incompatible With** (Incompatible con).

## <a name="monitor-and-report-on-access-assignments"></a>Supervisión e informes de asignaciones de acceso

Puede usar libros de Azure Monitor para obtener información sobre cómo los usuarios han estado recibiendo el acceso.

1. Configuración de Azure AD para [enviar eventos de auditoría a Azure Monitor](entitlement-management-logs-and-reporting.md).

1. El libro denominado *Actividad del paquete de acceso* muestra cada evento relacionado con un paquete de acceso determinado.

    ![Ver eventos de paquete de acceso](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. Para ver si se han realizado cambios en las asignaciones de roles de aplicación para una aplicación que no se crearon debido a las asignaciones de paquetes de acceso, puede seleccionar el libro denominado *Actividad de asignación de roles de aplicación*.  Si selecciona la opción para omitir la actividad de derechos, solo se muestran los cambios en los roles de aplicación que no se realizaron mediante la administración de derechos. Por ejemplo, vería una fila si un administrador global hubiera asignado directamente un usuario a un rol de aplicación.

    ![Visualización de asignaciones de roles de la aplicación](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## <a name="next-steps"></a>Pasos siguientes

- [Visualización, incorporación y eliminación de asignaciones en un paquete de acceso](entitlement-management-access-package-assignments.md)
- [Visualización de informes y registros](entitlement-management-reports.md)
