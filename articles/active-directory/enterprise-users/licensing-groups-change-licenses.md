---
title: 'Cambio de planes de licencias para usuarios y grupos: Azure AD | Microsoft Docs'
description: Migración de los usuarios dentro de un grupo a planes de servicio distintos mediante el uso de licencias de grupo en Azure Active Directory
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4631abf9d56f2201ec60750765cd5ef4558d58c1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985872"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Cambio de las asignaciones de licencia de un usuario o grupo en Azure Active Directory

En este artículo se describe cómo trasladar usuarios y grupos entre planes de licencia de servicio en Azure Active Directory (Azure AD). El enfoque de Azure AD tiene como objetivo asegurarse de que no haya ninguna pérdida de servicio ni de datos durante el cambio de la licencia. Los usuarios deben cambiar entre los servicios sin problemas. Los pasos de la asignación del plan de licencia de este artículo describen el cambio de un usuario o grupo en Office 365 E1 a Office 365 E3, pero se aplican a todos los planes de licencia. Al actualizar las asignaciones de licencia de un usuario o grupo, las supresiones de las asignaciones de licencia y las nuevas asignaciones se realizan simultáneamente; de este modo los usuarios no pierden el acceso a sus servicios durante los cambios de licencia ni se producen conflictos de licencia entre planes.

## <a name="before-you-begin"></a>Antes de empezar

Antes de actualizar las asignaciones de licencia, es importante comprobar que se cumplen ciertas suposiciones para todos los usuarios o grupos que se van a actualizar. Si las suposiciones no se cumplen para todos los usuarios de un grupo, se puede producir un error en la migración de alguno de ellos. Como resultado, algunos de los usuarios podrían perder el acceso a servicios o datos. Asegúrese de que:

- Los usuarios tienen el plan de licencia actual (en este caso, Office 365 E1) que se asigna a un grupo y hereda el usuario, y no uno asignado directamente.

- Dispone de suficientes licencias para el plan de licencia que va a asignar. Si no tiene suficientes licencias, es posible que no se asigne el nuevo plan a algunos usuarios. Puede comprobar el número de licencias disponibles.

- Los usuarios no tienen otras licencias de servicio asignadas que puedan entrar en conflicto con la licencia deseada o impedir la eliminación de la licencia actual. Por ejemplo, una licencia de un servicio como Workplace Analytics o Project Online, que tienen una dependencia sobre otros servicios.

- Si administra grupos locales y los sincroniza en Azure AD a través de Azure AD Connect, agrega o quita los usuarios mediante su sistema local. Puede que los cambios tarden algún tiempo en sincronizarse con Azure AD para seleccionarlos en las licencias de grupo.

- Si utiliza la pertenencia dinámica a grupos de Azure AD, puede agregar o quitar usuarios cambiando sus atributos, pero el proceso de actualización de las asignaciones de licencia sigue siendo el mismo.

## <a name="change-user-license-assignments"></a>Cambio de las asignaciones de licencia de usuario

Si ve que algunas casillas de la página **Update license assignments** (Actualizar las asignaciones de licencia) no están disponibles, significa que los servicios no se pueden cambiar porque se heredan de una licencia de grupo.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta de administrador de licencias en la organización de Azure AD.
1. Seleccione **Azure Active Directory** > **Usuarios** y abra la página **Perfil** de un usuario.
1. Seleccione **Licencias**.
1. Seleccione **Asignaciones** para editar la asignación de licencias del usuario o grupo. En esta página **Asignaciones** es donde puede resolver los conflictos de asignación de licencias.
1. Seleccione la casilla Office 365 E3 y asegúrese de seleccionar al menos todos los servicios E1 asignados al usuario.
1. Desactive la casilla de Office 365 E1.

    ![Página de asignaciones de licencia para un usuario que muestra Office 365 E1 desactivado y Office 365 E3 seleccionado](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Seleccione **Guardar**.

Azure AD aplica las nuevas licencias y quita las licencias antiguas simultáneamente, para ofrecer continuidad del servicio.

## <a name="change-group-license-assignments"></a>Cambio de las asignaciones de licencia de grupo

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta de administrador de licencias en la organización de Azure AD.
1. Seleccione **Azure Active Directory** > **Grupos** y abra la página **Información general** de un grupo.
1. Seleccione **Licencias**.
1. Seleccione el comando **Asignaciones** para editar la asignación de licencias del usuario o grupo.
1. Seleccione la casilla de Office 365 E3. Para mantener la continuidad del servicio, asegúrese de seleccionar todos los servicios de E1 que ya están asignados al usuario.
1. Desactive la casilla de Office 365 E1.

    ![Selección del comando Asignaciones en la página Licencias de un usuario o grupo](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Seleccione **Guardar**.

Para proporcionar continuidad del servicio, Azure AD aplica las nuevas licencias y quita las licencias antiguas simultáneamente en todos los usuarios del grupo.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre otros escenarios de administración de licencias a través de grupos en los artículos siguientes:

- [Asignación de licencias a un grupo en Azure Active Directory](licensing-groups-assign.md)
- [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](licensing-groups-resolve-problems.md)
- [Migración de usuarios individuales con licencia a licencias de grupo en Azure Active Directory](licensing-groups-migrate-users.md)
- [Escenarios adicionales de licencias de grupo de Azure Active Directory](licensing-group-advanced.md)
- [Ejemplos de PowerShell para licencias de grupo en Azure Active Directory](licensing-ps-examples.md)
