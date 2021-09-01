---
author: mattchenderson
ms.service: app-service-web
ms.topic: include
ms.date: 06/11/2021
ms.author: mahender
ms.openlocfilehash: 9b05a4d16cbf6d3b9677c894bffb6e47018b5cff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122261584"
---
Azure Blob Storage se puede configurar para [autorizar solicitudes con Azure AD](../articles/storage/blobs/authorize-access-azure-active-directory.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Esto significa que, en lugar de generar una clave SAS con una fecha de expiración, puede confiar en la [identidad administrada](../articles/app-service/overview-managed-identity.md) de la aplicación. De manera predeterminada, se usará la identidad asignada por el sistema de la aplicación. Si desea especificar una identidad asignada por el usuario, puede establecer la configuración de la aplicación `WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID` en el identificador de recurso de esa identidad. El valor también puede aceptar "SystemAssigned" como valor, aunque esto es lo mismo que omitir la configuración por completo.

Para habilitar la captura del paquete mediante la identidad:

1. Asegúrese de que el blob esté [configurado para acceso privado](../articles/storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

1. Conceda a la identidad el rol [Lector de datos de Storage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) con ámbito en el blob del paquete. Consulte [Asignación de un rol de Azure para acceder a datos de blobs](../articles/storage/blobs/assign-azure-role-data-access.md) para obtener información sobre la creación de la asignación de roles.

1. Establezca la configuración de la aplicación `WEBSITE_RUN_FROM_PACKAGE` en la dirección URL del blob del paquete. Probablemente tendrá el formato "https://{nombre-de-la-cuenta-de-almacenamiento}.blob.core.windows.net/{nombre-del-contenedor}/{ruta-de-acceso-al-paquete}" o similar.