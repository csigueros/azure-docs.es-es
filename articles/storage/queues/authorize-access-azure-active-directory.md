---
title: Autorización del acceso a colas con Active Directory
titleSuffix: Azure Storage
description: Autorice el acceso a colas de Azure con Azure Active Directory (Azure AD). Asigne roles de Azure para los derechos de acceso. Acceda a los datos con una cuenta de Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8e28f56d67b4e92541c636e3b575b2f3404e3140
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734053"
---
# <a name="authorize-access-to-queues-using-azure-active-directory"></a>Autorización del acceso a colas con Azure Active Directory

Azure Storage admite el uso de Azure Active Directory (Azure AD) para autorizar solicitudes a datos en cola. Con Azure AD, puede usar el control de acceso basado en rol de Azure (Azure RBAC) para conceder permisos a una entidad de seguridad, que puede ser un usuario, un grupo o una entidad de servicio de aplicación. Azure AD autentica la entidad de seguridad para devolver un token de OAuth 2.0. Después, el token se puede usar para autorizar una solicitud en Queue service.

La autorización de solicitudes en Azure Storage con Azure AD proporciona seguridad superior y facilidad de uso sobre la autorización de clave compartida. Microsoft recomienda usar la autorización de Azure AD con las aplicaciones de cola cuando sea posible para garantizar el acceso con privilegios mínimos necesarios.

La autorización con Azure AD está disponible para todas las cuentas de almacenamiento de uso general en todas las regiones públicas y nubes nacionales. Solo las cuentas de almacenamiento creadas con el modelo de implementación de Azure Resource Manager admiten la autorización de Azure AD.

## <a name="overview-of-azure-ad-for-queues"></a>Información general sobre Azure AD para colas

Cuando una entidad de seguridad (un usuario, un grupo o una aplicación) intenta acceder a un recurso de cola, la solicitud debe estar autorizada. Con Azure AD, el acceso a un recurso es un proceso de dos pasos. En primer lugar, se autentica la identidad de la entidad de seguridad y se devuelve un token de OAuth 2.0. Después, el token se pasa como parte de una solicitud a Queue service y el servicio lo usa para autorizar el acceso al recurso especificado.

El paso de autenticación exige que una aplicación solicite un token de acceso de OAuth 2.0 en tiempo de ejecución. Si una aplicación se ejecuta desde una entidad de Azure como una máquina virtual de Azure, un conjunto de escalado de máquinas virtuales o una aplicación de Azure Functions, puede usar una [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md) para el acceso a las colas. Para obtener información sobre cómo autorizar solicitudes realizadas por una identidad administrada, vea [Autorización del acceso a colas con Azure Active Directory e identidades administradas para los recursos de Azure](../common/storage-auth-aad-msi.md).

El paso de autorización exige que se asignen uno o varios roles de Azure a la entidad de seguridad. Azure Storage proporciona roles de Azure que abarcan conjuntos comunes de permisos para datos de cola. Los roles que se asignan a una entidad de seguridad determinan los permisos que tiene esa entidad de seguridad. Para obtener más información sobre la asignación de roles de Azure para el acceso a colas vea [Asignación de un rol de Azure para acceder a datos de cola](../queues/assign-azure-role-data-access.md).

Las aplicaciones nativas y las aplicaciones web que realizan solicitudes a Queue service de Azure también pueden autorizar el acceso con Azure AD. Para obtener información sobre cómo solicitar un token de acceso y usarlo para autorizar solicitudes, vea [Autorización del acceso a Azure Storage con Azure AD desde una aplicación de Azure Storage](../common/storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Asignación de roles de Azure para derechos de acceso

Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure (RBAC de Azure)](../../role-based-access-control/overview.md). Azure Storage define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a los datos de cola. También puede definir roles personalizados para el acceso a datos de cola.

Cuando un rol de Azure se asigna a una entidad de seguridad de Azure AD, Azure concede a esa entidad de seguridad acceso a esos recursos. Una entidad de seguridad de Azure AD puede ser un usuario, un grupo, una entidad de servicio de aplicación o una [identidad de servicio administrada para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="resource-scope"></a>Ámbito de recursos

Antes de asignar un rol de Azure RBAC a una entidad de seguridad, determine el ámbito de acceso que debería tener la entidad de seguridad. Los procedimientos recomendados dictan que siempre es mejor conceder únicamente el ámbito más restringido posible. Los roles de Azure RBAC definidos en un ámbito más amplio los heredan los recursos que están debajo de ellos.

Puede limitar el acceso a los recursos de cola de Azure en los niveles siguientes, empezando por el ámbito más reducido:

- **Una cola individual.** En este ámbito, se aplica una asignación de roles a los mensajes de la cola, así como las propiedades y los metadatos de la cola.
- **La cuenta de almacenamiento.** En este ámbito, una asignación de roles se aplica a todas las colas y sus mensajes.
- **El grupo de recursos.** En este ámbito, se aplica una asignación de roles a todas las colas de todas las cuentas de almacenamiento del grupo de recursos.
- **La suscripción.** En este ámbito, se aplica una asignación de roles a todas las colas de todas las cuentas de almacenamiento de todos los grupos de recursos de la suscripción.
- **Un grupo de administración.** En este ámbito, se aplica una asignación de roles a todas las colas de todas las cuentas de almacenamiento de todos los grupos de recursos de todas las suscripciones del grupo de administración.

Para obtener más información sobre el ámbito de las asignaciones de roles de RBAC de Azure, consulte [Información sobre el ámbito de RBAC de Azure](../../role-based-access-control/scope-overview.md).

### <a name="azure-built-in-roles-for-queues"></a>Roles integrados de Azure para colas

RBAC de Azure proporciona una serie de roles integrados para autorizar el acceso a datos de cola con Azure AD y OAuth. Entre los roles que proporcionan permisos a los recursos de datos en Azure Storage están, por ejemplo, los siguientes:

- [Colaborador de datos de la cola de Storage](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): se usa para conceder permisos de lectura, escritura y eliminación a las colas de Azure.
- [Lector de datos de la cola de Storage](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): se usa para conceder permisos de solo lectura a las colas de Azure.
- [Procesador de mensajes de datos de la cola de Storage](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): se usa para conceder permisos de inspección, recuperación y eliminación a los mensajes de las colas de Azure Storage.
- [Emisor de mensajes de datos de la cola de Storage](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): se usa para conceder permisos de adición a los mensajes de las colas de Azure Storage.

Para obtener información sobre cómo asignar un rol integrado de Azure a una entidad de seguridad, vea [Asignación de un rol de Azure para acceder a datos de cola](../queues/assign-azure-role-data-access.md). Para obtener información sobre cómo enumerar los roles RBAC de Azure y sus permisos, consulte [Enumeración de las definiciones de roles de Azure](../../role-based-access-control/role-definitions-list.md).

Para más información acerca de cómo se definen los roles integrados para Azure Storage, consulte [Descripción de definiciones de roles](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para más información acerca de la creación de roles personalizados de Azure, consulte [Roles personalizados de Azure](../../role-based-access-control/custom-roles.md).

Solo los roles definidos explícitamente para el acceso a datos permiten a una entidad de seguridad acceder a los datos de colas. Los roles integrados, como **Propietario**, **Colaborador** y **Colaborador de la cuenta de almacenamiento**, permiten que una entidad de seguridad administre una cuenta de almacenamiento, pero no proporcionan acceso a los datos de colas dentro de esa cuenta a través de Azure AD. Sin embargo, si un rol incluye **Microsoft.Storage/storageAccounts/listKeys/action**, el usuario al que se haya asignado ese rol podrá acceder a los datos de la cuenta de almacenamiento mediante la autorización de clave compartida con las claves de acceso de la cuenta. Para obtener más información, vea [Elección de la forma de autorizar el acceso a los datos de blob en Azure Portal](../../storage/queues/authorize-data-operations-portal.md).

Para obtener información detallada sobre los roles integrados de Azure para Azure Storage para los servicios de datos y el servicio de administración, consulte la sección **Almacenamiento** en [Roles integrados de Azure para RBAC de Azure](../../role-based-access-control/built-in-roles.md#storage). Además, para obtener información sobre los diferentes tipos de roles que proporcionan permisos en Azure, consulte [Roles de administrador de suscripciones clásico, roles de Azure y roles de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Las asignaciones de roles de Azure pueden tardar hasta 30 minutos en propagarse.

### <a name="access-permissions-for-data-operations"></a>Permisos de acceso para operaciones de datos

Para obtener información sobre los permisos necesarios para llamar a operaciones concretas de Queue service, vea [Permisos para llamar a operaciones de datos](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

## <a name="access-data-with-an-azure-ad-account"></a>Acceso a datos con una cuenta de Azure AD

El acceso a los datos de cola desde Azure Portal, PowerShell o la CLI de Azure se puede autorizar mediante la cuenta de Azure AD del usuario o las claves de acceso de cuenta (autorización de clave compartida).

### <a name="data-access-from-the-azure-portal"></a>Acceso a datos desde Azure Portal

Azure Portal puede usar la cuenta de Azure AD o las claves de acceso de cuenta para acceder a datos de cola en una cuenta de Azure Storage. El esquema de autorización que use Azure Portal depende de los roles de Azure que tenga asignados.

Si intenta acceder a datos de cola, Azure Portal primero comprueba si tiene asignado un rol de Azure con **Microsoft.Storage/storageAccounts/listkeys/action**. Si tiene un rol asignado con esta acción, Azure Portal usa la clave de cuenta para acceder a los datos de cola mediante la autorización de clave compartida. Si no tiene un rol asignado con esta acción, Azure Portal intenta acceder a los datos mediante la cuenta de Azure AD.

Para acceder a datos de cola desde Azure Portal con la cuenta de Azure AD, necesita permisos para acceder a datos de cola y, también, permisos para examinar los recursos de la cuenta de almacenamiento en Azure Portal. Los roles integrados que proporciona Azure Storage conceden acceso a recursos de cola, pero no conceden permisos a los recursos de la cuenta de almacenamiento. Por este motivo, el acceso al portal también requiere la asignación de un rol de Azure Resource Manager, como el rol [Lector](../../role-based-access-control/built-in-roles.md#reader), con ámbito limitado al nivel de la cuenta de almacenamiento o superior. El rol **Lector** concede los permisos más restringidos, pero otro rol de Azure Resource Manager que conceda acceso a los recursos de administración de la cuenta de almacenamiento también es aceptable. Para obtener más información sobre cómo asignar permisos a los usuarios para el acceso a los datos de Azure Portal con una cuenta de Azure AD, vea [Asignación de un rol de Azure para acceder a datos de cola](../queues/assign-azure-role-data-access.md).

En Azure Portal se indica qué esquema de autorización se usa al examinar una cola. Para obtener más información sobre el acceso a datos en el portal, vea [Elección de la forma de autorizar el acceso a los datos de cola en Azure Portal](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acceso a datos desde PowerShell o la CLI de Azure

PowerShell y la CLI de Azure admiten el inicio de sesión con credenciales de Azure AD. Después de iniciar sesión, la sesión se ejecuta con esas credenciales. Para obtener más información, vea uno de los siguientes artículos:

- [Distintas formas de autorizar el acceso para poner datos en cola con la CLI de Azure](authorize-data-operations-cli.md)
- [Ejecución de comandos de PowerShell con credenciales de Azure AD para acceder a los datos de cola](authorize-data-operations-powershell.md)

## <a name="next-steps"></a>Pasos siguientes

- [Autorización del acceso a datos en Azure Storage](../common/authorize-data-access.md)
- [Asignación de un rol de Azure para acceder a datos de cola](assign-azure-role-data-access.md)