---
title: Autorización de operaciones de datos
titleSuffix: Azure Storage
description: Más información sobre las distintas maneras de autorizar el acceso al Azure Storage, incluido Azure Active Directory, la autorización con clave compartida o las firmas de acceso compartido (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9896cdaf82ca508c5e6a1a444564403ef0f7e1eb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765460"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Autorización del acceso a datos en Azure Storage

Cada vez que accede a datos de la cuenta de almacenamiento, el cliente realiza una solicitud a través de HTTP/HTTPS a Azure Storage. Todas las solicitudes a un recurso seguro deben estar autorizadas para que el servicio garantice que el cliente tiene los permisos necesarios para acceder a los datos.

En la tabla siguiente se describen las opciones que ofrece Azure Storage para autorizar el acceso a los recursos:

| Artefacto de Azure | Clave compartida (clave de cuenta de almacenamiento) | Firma de acceso compartido (SAS) | Azure Active Directory (Azure AD) | Active Directory Domain Services local | Acceso de lectura anónimo |
|--|--|--|--|--|--|
| Azure Blobs | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | [Compatible](authorize-data-access.md) | No compatible | [Compatible](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | No compatible | [Admitido, solo con AAD Domain Services](../files/storage-files-active-directory-overview.md) | [Admitido, las credenciales deben sincronizarse con Azure AD](../files/storage-files-active-directory-overview.md) | No compatible |
| Azure Files (REST) | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | No compatible | No compatible | No compatible |
| Colas de Azure | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | [Compatible](authorize-data-access.md) | No compatible | No compatible |
| Azure Tables | [Compatible](/rest/api/storageservices/authorize-with-shared-key/) | [Compatible](storage-sas-overview.md) | [Compatible](../tables/authorize-access-azure-active-directory.md) (versión preliminar) | No compatible | No compatible |

Cada opción de autorización se describe brevemente a continuación:

- **Integración de Azure Active Directory (Azure AD)** con blobs, colas y tablas. Azure proporciona control de acceso basado en rol de Azure (Azure RBAC) para controlar el acceso de un cliente a los recursos de una cuenta de almacenamiento. Microsoft recomienda usar Azure AD siempre que sea posible por su mayor seguridad y facilidad de uso. Para más información sobre la integración de Azure AD, consulte [Autorización del acceso a datos en Azure Storage](authorize-data-access.md).

- **Autenticación de Azure Active Directory Domain Services (Azure DS)** para Azure Files. Azure Files admite la autorización basada en identidad sobre Bloque de mensajes del servidor(SMB) mediante Azure AD DS. Puede usar Azure RBAC para el control específico de acceso de los clientes a los recursos de Azure Files en una cuenta de almacenamiento. Para más información acerca de la autenticación de Azure Files mediante servicios de dominio, consulte la [introducción](../files/storage-files-active-directory-overview.md).

- **Autenticación de Active Directory Domain Services (AD DS o AD DS local)**  para Azure Files. Azure Files admite la autorización basada en identidad sobre SMB mediante AD DS. El entorno de AD DS se puede hospedar en máquinas locales o en VM de Azure. El acceso de SMB a Files se admite mediante el uso de las credenciales de AD DS de las máquinas unidas a un dominio, independientemente de que sea local o en Azure. Puede usar una combinación de Azure RBAC para el control de acceso a nivel de recurso compartido y listas de control de acceso discrecional de NTFS para el cumplimiento de los permisos a nivel de archivo/directorio. Para más información acerca de la autenticación de Azure Files mediante servicios de dominio, consulte la [introducción](../files/storage-files-active-directory-overview.md).

- **Autorización con clave compartida** para blobs, archivos, colas y tablas. Los clientes con clave compartida pasan un encabezado con cada solicitud que está firmado con la clave de acceso de la cuenta de almacenamiento. Para más información, consulte el artículo sobre la [Autorización con clave compartida](/rest/api/storageservices/authorize-with-shared-key/).

- **Firmas de acceso compartido** para blobs, archivos, colas y tablas. Las firmas de acceso compartido (SAS) proporcionan acceso delegado limitado a recursos de una cuenta de almacenamiento. Agregar restricciones al periodo de validez de la firma o a los permisos que concede proporciona flexibilidad para administrar el acceso. Para obtener más información, consulte [Uso de firmas de acceso compartido (SAS)](storage-sas-overview.md).
- **Acceso de lectura público y anónimo** para contenedores y blobs. No se necesita autorización. Para más información, consulte [Administración del acceso de lectura anónimo a contenedores y blobs](../blobs/anonymous-read-access-configure.md).  

## <a name="next-steps"></a>Pasos siguientes

- [Autorización del acceso a datos en Azure Storage](authorize-data-access.md)
- [Autorización con clave compartida](/rest/api/storageservices/authorize-with-shared-key/)
- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](storage-sas-overview.md) (Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido [SAS])
