---
title: Autorización del acceso a tablas mediante Active Directory (versión preliminar)
titleSuffix: Azure Storage
description: Autorice el acceso a tablas de Azure con Azure Active Directory (Azure AD) (versión preliminar). Asigne roles de Azure para los derechos de acceso. Acceda a los datos con una cuenta de Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3f0c18d7f9130c4ef50b085a4f8c1ec5c22ed6d3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360389"
---
# <a name="authorize-access-to-tables-using-azure-active-directory-preview"></a>Autorización del acceso a tablas mediante Azure Active Directory (versión preliminar)

Azure Storage admite el uso de Azure Active Directory (Azure AD) para autorizar solicitudes a datos de tablas (versión preliminar). Con Azure AD, puede usar el control de acceso basado en rol de Azure (Azure RBAC) para conceder permisos a una entidad de seguridad, que puede ser un usuario, un grupo o una entidad de servicio de aplicación. Azure AD autentica la entidad de seguridad para devolver un token de OAuth 2.0. Después, el token se puede usar para autorizar una solicitud en Table service.

La autorización de solicitudes en Azure Storage con Azure AD proporciona seguridad superior y facilidad de uso sobre la autorización de clave compartida. Microsoft recomienda usar la autorización de Azure AD con las aplicaciones de tabla cuando sea posible para garantizar el acceso con los privilegios mínimos necesarios.

La autorización con Azure AD está disponible para todas las cuentas de uso general en todas las regiones públicas y nubes nacionales. Solo las cuentas de almacenamiento creadas con el modelo de implementación de Azure Resource Manager admiten la autorización de Azure AD.

> [!IMPORTANT]
> La autorización con Azure AD para tablas se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="overview-of-azure-ad-for-tables"></a>Información general sobre Azure AD para tablas

Cuando una entidad de seguridad (un usuario, un grupo o una aplicación) intenta acceder a un recurso de tabla, la solicitud debe estar autorizada. Con Azure AD, el acceso a un recurso es un proceso de dos pasos. En primer lugar, se autentica la identidad de la entidad de seguridad y se devuelve un token de OAuth 2.0. Después, el token se pasa como parte de una solicitud a Table service y el servicio lo usa para autorizar el acceso al recurso especificado.

El paso de autenticación exige que una aplicación solicite un token de acceso de OAuth 2.0 en tiempo de ejecución. Si una aplicación se ejecuta desde una entidad de Azure como una máquina virtual de Azure, un conjunto de escalado de máquinas virtuales o una aplicación de Azure Functions, puede usar una [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md) para el acceso a las tablas. Para obtener información sobre cómo autorizar solicitudes realizadas por una identidad administrada, vea [Autorización del acceso a tablas con Azure Active Directory e identidades administradas para los recursos de Azure](../common/storage-auth-aad-msi.md).

El paso de autorización exige que se asignen uno o varios roles de Azure a la entidad de seguridad. Azure Storage proporciona roles de Azure que abarcan conjuntos comunes de permisos para datos de tablas. Los roles que se asignan a una entidad de seguridad determinan los permisos que tiene esa entidad de seguridad. Para más información sobre la asignación de roles de Azure para el acceso a tablas, vea [Asignación de un rol de Azure para acceder a datos de tablas](assign-azure-role-data-access.md).

Las aplicaciones nativas y las aplicaciones web que realizan solicitudes a Azure Table service también pueden autorizar el acceso con Azure AD. Para obtener información sobre cómo solicitar un token de acceso y usarlo para autorizar solicitudes, vea [Autorización del acceso a Azure Storage con Azure AD desde una aplicación de Azure Storage](../common/storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Asignación de roles de Azure para derechos de acceso

Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Azure Storage define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a los datos de tablas. También puede definir roles personalizados para el acceso a datos de tablas.

Cuando un rol de Azure se asigna a una entidad de seguridad de Azure AD, Azure concede a esa entidad de seguridad acceso a esos recursos. Una entidad de seguridad de Azure AD puede ser un usuario, un grupo, una entidad de servicio de aplicación o una [identidad de servicio administrada para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="resource-scope"></a>Ámbito de recursos

Antes de asignar un rol de Azure RBAC a una entidad de seguridad, determine el ámbito de acceso que debería tener la entidad de seguridad. Los procedimientos recomendados dictan que siempre es mejor conceder únicamente el ámbito más restringido posible. Los roles de Azure RBAC definidos en un ámbito más amplio los heredan los recursos que están debajo de ellos.

Puede limitar el acceso a los recursos de tabla de Azure en los niveles siguientes, empezando por el ámbito más reducido:

- **Una tabla individual**. En este ámbito, se aplica una asignación de roles a la tabla especificada.
- **La cuenta de almacenamiento.** En este ámbito, se aplica una asignación de roles a todas las tablas de la cuenta.
- **El grupo de recursos.** En este ámbito, se aplica una asignación de roles a todas las tablas de todas las cuentas de almacenamiento del grupo de recursos.
- **La suscripción.** En este ámbito, se aplica una asignación de roles a todas las tablas de todas las cuentas de almacenamiento de todos los grupos de recursos de la suscripción.
- **Un grupo de administración.** En este ámbito, se aplica una asignación de roles a todas las tablas de todas las cuentas de almacenamiento de todos los grupos de recursos de todas las suscripciones del grupo de administración.

Para obtener más información sobre el ámbito de las asignaciones de roles de RBAC de Azure, consulte [Información sobre el ámbito de RBAC de Azure](../../role-based-access-control/scope-overview.md).

### <a name="azure-built-in-roles-for-tables"></a>Roles integrados de Azure para tablas

Azure RBAC proporciona roles integrados para autorizar el acceso a datos de tabla con Azure AD y OAuth. Entre los roles integrados que proporcionan permisos a las tablas en Azure Storage están, por ejemplo, los siguientes:

- [Colaborador de datos de tabla de Storage](../../role-based-access-control/built-in-roles.md#storage-table-data-contributor): se usa para conceder permisos de lectura, escritura y eliminación a los recursos de Table Storage.
- [Lector de datos de tabla de Storage](../../role-based-access-control/built-in-roles.md#storage-table-data-reader): se usa para conceder permisos de solo lectura a los recursos de Table Storage.

Para obtener información sobre cómo asignar un rol integrado de Azure a una entidad de seguridad, vea [Asignación de un rol de Azure para acceder a datos de tablas](assign-azure-role-data-access.md). Para obtener información sobre cómo enumerar los roles RBAC de Azure y sus permisos, consulte [Enumeración de las definiciones de roles de Azure](../../role-based-access-control/role-definitions-list.md).

Para más información acerca de cómo se definen los roles integrados para Azure Storage, consulte [Descripción de definiciones de roles](../../role-based-access-control/role-definitions.md#control-and-data-actions). Para más información acerca de la creación de roles personalizados de Azure, consulte [Roles personalizados de Azure](../../role-based-access-control/custom-roles.md).

Solo los roles definidos explícitamente para el acceso a datos permiten a una entidad de seguridad acceder a los datos de tablas. Los roles integrados, como **Propietario**, **Colaborador** y **Colaborador de la cuenta de almacenamiento**, permiten que una entidad de seguridad administre una cuenta de almacenamiento, pero no proporcionan acceso a los datos de tablas dentro de esa cuenta a través de Azure AD. Sin embargo, si un rol incluye **Microsoft.Storage/storageAccounts/listKeys/action**, el usuario al que se haya asignado ese rol podrá acceder a los datos de la cuenta de almacenamiento mediante la autorización de clave compartida con las claves de acceso de la cuenta.

Para obtener información detallada sobre los roles integrados de Azure para Azure Storage para los servicios de datos y el servicio de administración, consulte la sección **Almacenamiento** en [Roles integrados de Azure para RBAC de Azure](../../role-based-access-control/built-in-roles.md#storage). Además, para obtener información sobre los diferentes tipos de roles que proporcionan permisos en Azure, consulte [Roles de administrador de suscripciones clásico, roles de Azure y roles de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Las asignaciones de roles de Azure pueden tardar hasta 30 minutos en propagarse.

### <a name="access-permissions-for-data-operations"></a>Permisos de acceso para operaciones de datos

Para obtener información sobre los permisos necesarios para llamar a operaciones concretas de Table service, vea [Permisos para llamar a operaciones de datos](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

## <a name="next-steps"></a>Pasos siguientes

- [Autorización del acceso a datos en Azure Storage](../common/authorize-data-access.md)
- [Asignación de un rol de Azure para acceder a datos de tabla](assign-azure-role-data-access.md)