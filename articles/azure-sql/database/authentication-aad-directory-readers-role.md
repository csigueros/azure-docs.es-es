---
title: Rol Lectores de directorios en Azure Active Directory para Azure SQL
description: Obtenga información sobre el rol del lector de directorios en Azure AD para Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/30/2021
ms.openlocfilehash: 738338c4f3931e97965e697ca1bf316018a495c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735590"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Rol Lectores de directorios en Azure Active Directory para Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory (Azure AD) ha presentado el [uso de grupos de Azure AD para administrar las asignaciones de roles](../../active-directory/roles/groups-concept.md). Esto permite asignar roles de Azure AD a los grupos.

Al habilitar una [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para Azure SQL Database, Instancia administrada de SQL de Azure o Azure Synapse Analytics, el rol [**Lectores de directorios**](../../active-directory/roles/permissions-reference.md#directory-readers) de Azure AD debe estar asignado a la identidad para permitir el acceso de lectura a [Azure AD Graph API](/graph/migrate-azure-ad-graph-planning-checklist). La identidad administrada de SQL Database y Azure Synapse se conoce como identidad del servidor. La identidad administrada de la Instancia administrada de SQL se conoce como identidad de la instancia administrada y se asigna automáticamente cuando se crea la instancia. Para más información sobre cómo asignar una identidad del servidor a SQL Database o Azure Synapse, consulte [Habilitación de entidades de servicio para crear usuarios de Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

El rol **Lectores de directorios** es necesario para:

- Creación de un administrador de Azure AD para la Instancia administrada de SQL de Azure
- Suplantación de usuarios de Azure AD en Azure SQL
- Migración de usuarios de SQL Server que utilizan autenticación de Windows a la Instancia administrada de SQL con autenticación de Azure AD (mediante el comando [ALTER USER (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration))
- Cambio del administrador de Azure AD en la Instancia administrada de SQL
- Habilitación de [entidades de servicio (aplicaciones)](authentication-aad-service-principal.md) para crear usuarios de Azure AD en Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Asignación del rol Lectores de directorios

Para asignar el rol [**Lectores de directorios**](../../active-directory/roles/permissions-reference.md#directory-readers) a una identidad, se necesita un usuario con permisos [Administrador global](../../active-directory/roles/permissions-reference.md#global-administrator) o [Administrador de roles con privilegios](../../active-directory/roles/permissions-reference.md#privileged-role-administrator). Es posible que los usuarios que administren o implementen SQL Database, Instancia administrada de SQL o Azure Synapse no tengan acceso a estos roles con privilegios elevados. Esto suele provocar complicaciones para los usuarios que crean recursos de Azure SQL no planeados o necesitan ayuda de miembros que tengan roles con privilegios elevados, que a menudo son inaccesibles en organizaciones de gran tamaño.

En el caso de Instancia administrada de SQL, el rol **Lectores de directorios** debe estar asignado a la identidad de la instancia administrada si desea [configurar un administrador de Azure AD para la instancia administrada](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

No es necesario asignar el rol **Lectores de directorio** a la identidad del servidor para SQL Database o Azure Synapse al configurar un administrador de Azure AD para el servidor lógico. Sin embargo, para habilitar la creación de un objeto de Azure AD en SQL Database o Azure Synapse en nombre de una aplicación de Azure AD, se requiere el rol **Lectores de directorio**. Si el rol no se asigna a la identidad del servidor lógico de SQL, se producirá un error al crear los usuarios de Azure AD en Azure SQL. Para más información, consulte [Entidad de servicio de Azure Active Directory con Azure SQL](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Concesión del rol Lectores de directorios a un grupo de Azure AD

Ya puede tener un [administrador global](../../active-directory/roles/permissions-reference.md#global-administrator) o un [administrador de roles con privilegios](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) para crear un grupo de Azure AD y asignar el permiso [**Lectores de directorios**](../../active-directory/roles/permissions-reference.md#directory-readers) al grupo. Esto permitirá el acceso a Azure AD Graph API a los miembros de este grupo. Además, los usuarios de Azure AD que sean propietarios de este grupo pueden asignar nuevos miembros a este grupo, incluidas las identidades de los servidores lógicos de Azure SQL.

Esta solución todavía requiere un usuario con privilegios elevados (Administrador global o Administrador de roles con privilegios) para crear un grupo y asignar usuarios como una actividad de una sola vez, pero los propietarios de los grupos de Azure AD podrán asignar miembros adicionales más adelante. Esto elimina la necesidad de implicar a un usuario con privilegios elevados en el futuro para configurar todas las bases de datos SQL, las instancias administradas de SQL o los servidores de Azure Synapse en sus inquilinos de Azure AD.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Asignación del rol Lectores de directorios a un grupo de Azure AD y administración de las asignaciones de roles](authentication-aad-directory-readers-role-tutorial.md)