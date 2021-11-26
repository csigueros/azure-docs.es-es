---
title: Autorización de operaciones de datos
titleSuffix: Azure Storage
description: Obtenga información sobre las distintas maneras de autorizar el acceso a los datos en Azure Storage. Azure Storage admite la autorización con Azure Active Directory, la autorización de clave compartida o las firmas de acceso compartido (SAS). También admite el acceso anónimo a blobs.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 678dd934bfdece9a957595bbfb16a4e7c8e8d3de
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720404"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Autorización del acceso a datos en Azure Storage

Cada vez que accede a datos de la cuenta de almacenamiento, la aplicación cliente realiza una solicitud a través de HTTP/HTTPS a Azure Storage. De forma predeterminada, todos los recursos de Azure Storage están protegidos y todas las solicitudes a un recurso seguro se deben autorizar. La autorización garantiza que la aplicación cliente tenga los permisos adecuados para acceder a los datos de la cuenta de almacenamiento.

En la tabla siguiente se describen las opciones que ofrece Azure Storage para autorizar el acceso a los datos:

| Artefacto de Azure | Clave compartida (clave de cuenta de almacenamiento) | Firma de acceso compartido (SAS) | Azure Active Directory (Azure AD) | Active Directory Domain Services local | Acceso de lectura anónimo |
|--|--|--|--|--|--|
| Azure Blobs | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | [Compatible](../blobs/authorize-access-azure-active-directory.md) | No compatible | [Compatible](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | No compatible | [Admitido, solo con AAD Domain Services](../files/storage-files-active-directory-overview.md) | [Admitido, las credenciales deben sincronizarse con Azure AD](../files/storage-files-active-directory-overview.md) | No compatible |
| Azure Files (REST) | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | No compatible | No compatible | No compatible |
| Colas de Azure | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | [Compatible](../queues/authorize-access-azure-active-directory.md) | No compatible | No compatible |
| Azure Tables | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | [Compatible](../tables/authorize-access-azure-active-directory.md) (versión preliminar) | No compatible | No compatible |

Cada opción de autorización se describe brevemente a continuación:

- **Integración de Azure Active Directory (Azure AD)** para autorizar solicitudes a recursos de blob, cola y tabla. Microsoft recomienda usar las credenciales de Azure AD para autorizar las solicitudes de datos siempre que sea posible para mayor seguridad y facilidad de uso. Para obtener más información sobre la integración de Azure AD, consulte los artículos sobre los recursos de [tabla](../blobs/authorize-access-azure-active-directory.md), [cola](../queues/authorize-access-azure-active-directory.md) o [blob](../tables/authorize-access-azure-active-directory.md).

    Puede usar el control de acceso basado en rol de Azure (RBAC de Azure) para administrar los permisos de una entidad de seguridad para los recursos de blob, cola y tabla de una cuenta de almacenamiento. Además, puede usar el control de acceso basado en atributos (ABAC) de Azure para agregar condiciones a las asignaciones de roles de Azure para los recursos de blob. Para más información acerca de RBAC, consulte [¿Qué es el control de acceso basado en rol (RBAC) de Azure?](../../role-based-access-control/overview.md) Para obtener más información acerca de ABAC, consulte [¿Qué es el control de acceso basado en atributos de Azure (ABAC de Azure)? (versión preliminar)](../../role-based-access-control/conditions-overview.md).

- **Autenticación de Azure Active Directory Domain Services (Azure DS)** para Azure Files. Azure Files admite la autorización basada en identidad sobre Bloque de mensajes del servidor(SMB) mediante Azure AD DS. Puede usar Azure RBAC para el control específico de acceso de los clientes a los recursos de Azure Files en una cuenta de almacenamiento. Para obtener más información acerca de la autenticación de Azure Files mediante servicios de dominio, consulte la [información general](../files/storage-files-active-directory-overview.md).

- **Autenticación de Active Directory Domain Services (AD DS o AD DS local)**  para Azure Files. Azure Files admite la autorización basada en identidad sobre SMB mediante AD DS. El entorno de AD DS se puede hospedar en máquinas locales o en VM de Azure. El acceso de SMB a Files se admite mediante el uso de las credenciales de AD DS de las máquinas unidas a un dominio, independientemente de que sea local o en Azure. Puede usar una combinación de Azure RBAC para el control de acceso a nivel de recurso compartido y listas de control de acceso discrecional de NTFS para el cumplimiento de los permisos a nivel de archivo/directorio. Para obtener más información acerca de la autenticación de Azure Files mediante servicios de dominio, consulte la [información general](../files/storage-files-active-directory-overview.md).

- **Autorización con clave compartida** para blobs, archivos, colas y tablas. Los clientes con clave compartida pasan un encabezado con cada solicitud que está firmado con la clave de acceso de la cuenta de almacenamiento. Para más información, consulte el artículo sobre la [Autorización con clave compartida](/rest/api/storageservices/authorize-with-shared-key/).

    Puede no permitir la autorización de clave compartida para una cuenta de almacenamiento. Si no se permite la autorización de clave compartida, los clientes deben usar Azure AD para autorizar solicitudes de datos en esa cuenta de almacenamiento. Para obtener más información, consulte [Impedir la autorización con clave compartida para una cuenta de Azure Storage](shared-key-authorization-prevent.md).

- **Firmas de acceso compartido** para blobs, archivos, colas y tablas. Las firmas de acceso compartido (SAS) proporcionan acceso delegado limitado a recursos de una cuenta de almacenamiento. Agregar restricciones al periodo de validez de la firma o a los permisos que concede proporciona flexibilidad para administrar el acceso. Para obtener más información, consulte [Uso de firmas de acceso compartido (SAS)](storage-sas-overview.md).

- **Acceso de lectura público y anónimo** para contenedores y blobs. Cuando se configura el acceso anónimo, los clientes pueden leer datos de blob sin autorización. Para más información, consulte [Administración del acceso de lectura anónimo a contenedores y blobs](../blobs/anonymous-read-access-configure.md).

    Puede deshabilitar el acceso de lectura público anónimo para una cuenta de almacenamiento. Cuando no se permite el acceso de lectura público anónimo, los usuarios no pueden configurar contenedores para habilitar el acceso anónimo y todas las solicitudes se deben autorizar. Para más información, consulte el artículo en el que se explica cómo [impedir el acceso de lectura público anónimo a contenedores y blobs](../blobs/anonymous-read-access-prevent.md).

## <a name="next-steps"></a>Pasos siguientes

- Autorice el acceso con Azure Active Directory a recursos de [tabla](../blobs/authorize-access-azure-active-directory.md), [cola](../queues/authorize-access-azure-active-directory.md) o [blob](../tables/authorize-access-azure-active-directory.md).
- [Autorización con clave compartida](/rest/api/storageservices/authorize-with-shared-key/)
- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](storage-sas-overview.md) (Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido [SAS])
        