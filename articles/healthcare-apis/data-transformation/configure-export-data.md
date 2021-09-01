---
title: Definición de la configuración de exportación en el servicio de FHIR
description: En este artículo se describe cómo definir la configuración de exportación en el servicio de FHIR
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: 327a36714b822669cbc41fc8b8fb195bed4872ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780534"
---
# <a name="configure-export-settings-and-set-up-a-storage-account"></a>Definición de la configuración de exportación y de la configuración de una cuenta de almacenamiento

El servicio de FHIR admite el comando $export, que le permite exportar los datos de la cuenta de servicio de FHIR a una cuenta de almacenamiento.

Para configurar la exportación en el servicio de FHIR es necesario realizar tres pasos:

1. Habilitación de la identidad administrada en el servicio de FHIR.
2. Creación de una cuenta de Azure Storage (si no se ha hecho antes) y asignación de permisos para el servicio de FHIR a la cuenta de almacenamiento.
3. Selección de la cuenta de almacenamiento en el servicio de FHIR como cuenta de almacenamiento de exportación.

## <a name="enabling-managed-identity-on-fhir-service"></a>Habilitación de la identidad administrada en el servicio de FHIR.

El primer paso para configurar el servicio de FHIR para la exportación consiste en habilitar la identidad administrada en todo el sistema en el servicio. Para más información sobre las identidades administradas en Azure, consulte [¿Qué son las identidades administradas para recursos de Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

Para ello, vaya al servicio de FHIR y seleccione **Identidad**. Al cambiar el estado a **Activado**, se habilitará la identidad administrada en el servicio de FHIR.

![Habilitación de una entidad administrada](media/export-data/fhir-mi-enabled.png)

Ahora, puede avanzar hacia el siguiente paso y crear una cuenta de almacenamiento y asignar permiso a nuestro servicio.

## <a name="adding-permission-to-storage-account"></a>Concesión de permisos a la cuenta de almacenamiento

El siguiente paso de la exportación de datos es asignar el permiso para que el servicio de FHIR escriba en la cuenta de almacenamiento.

Después de crear una cuenta de almacenamiento, vaya a **Control de acceso (IAM)** en la cuenta de almacenamiento y seleccione **Agregar asignación de roles**. 

Para más información sobre la asignación de roles en Azure Portal, consulte [Roles integrados de Azure](../../role-based-access-control/role-assignments-portal.md).

Aquí agregará el rol [Colaborador de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a nuestro nombre de servicio y, a continuación, seleccionará **Guardar**.

![Página Agregar asignación de roles](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

Ahora, está a punto para seleccionar la cuenta de almacenamiento en el servicio de FHIR como cuenta de almacenamiento predeterminada para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selección de la cuenta de almacenamiento para el comando $export

El último paso consiste en asignar la cuenta de almacenamiento de Azure en la que el servicio de FHIR exportará los datos. Para ello, vaya a **Integración** en el servicio de FHIR y seleccione la cuenta de almacenamiento.

![Almacenamiento de las exportaciones de FHIR](media/export-data/fhir-export-storage.png)

Una vez completado este último paso, ya está a punto para exportar los datos mediante el comando $export.

> [!Note]
> Solo las cuentas de almacenamiento de la misma suscripción que el servicio FHIR pueden registrarse como destino para operaciones $export.