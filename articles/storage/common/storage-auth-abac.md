---
title: Autorización del acceso a blobs mediante las condiciones de asignación de roles de Azure (versión preliminar)
titleSuffix: Azure Storage
description: Autorice el acceso a blobs de Azure mediante las condiciones de asignación de roles de Azure y el control de acceso basado en atributos (ABAC) de Azure. Defina condiciones en las asignaciones de roles mediante atributos de almacenamiento.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 8e424bd03e69affebac6f9bb614d2449e6ec45c0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070829"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Autorización del acceso a blobs mediante las condiciones de asignación de roles de Azure (versión preliminar)

> [!IMPORTANT]
> Las condiciones de asignación de roles de Azure y Azure ABAC se encuentran actualmente en versión preliminar.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

El control de acceso basado en atributos (ABAC) es una estrategia de autorización que define los niveles de acceso en función de atributos asociados a entidades de seguridad, recursos, solicitudes y entorno. El control de acceso basado en atributos (ABAC) de Azure se basa en el control de acceso basado en roles (RBAC) de Azure mediante la adición de [condiciones a las asignaciones de roles de Azure](../../role-based-access-control/conditions-overview.md) en el sistema de administración de identidad y acceso (IAM) existente. Esta versión preliminar incluye compatibilidad con las condiciones de asignación de roles de blobs y Data Lake Storage Gen2, y le permite crear condiciones de asignación de roles basadas en atributos de recursos y de solicitudes.

## <a name="overview-of-conditions-in-azure-storage"></a>Introducción a las condiciones de Azure Storage

Azure Storage permite el [uso de Azure Active Directory](storage-auth-aad.md) para autorizar solicitudes a Blob Storage y Queue Storage. Azure AD autoriza derechos de acceso a recursos protegidos mediante Azure RBAC. Azure Storage define un conjunto de [roles integrados](../../role-based-access-control/built-in-roles.md#storage) de Azure que engloban los conjuntos comunes de permisos que se usan para acceder a los datos de los blobs y de las colas. También puede definir roles personalizados con un conjunto de permisos seleccionado. Azure Storage admite asignaciones de roles para cuentas de almacenamiento o contenedores de blobs.

Sin embargo, en algunos casos, es posible que tenga que habilitar un acceso más específico a los recursos de Storage o simplificar los cientos de asignaciones de roles para un recurso de almacenamiento. Para lograr estos objetivos, puede configurar [condiciones en las asignaciones de roles](../../role-based-access-control/conditions-overview.md) de [DataActions](../../role-based-access-control/role-definitions.md#dataactions). Puede usar condiciones con un [rol personalizado](../../role-based-access-control/custom-roles.md) o seleccionar roles integrados. Tenga en cuenta que no se admiten condiciones para las [acciones](../../role-based-access-control/role-definitions.md#actions) de administración mediante el [proveedor de recursos de almacenamiento](/rest/api/storagerp).

Los blobs admiten las condiciones de Azure Storage. Puede usar condiciones con cuentas que tengan habilitada la característica de [espacio de nombres jerárquico](../blobs/data-lake-storage-namespace.md) (HNS). Las condiciones no se admiten actualmente en Files, Queues y Tables.

## <a name="supported-attributes-and-operations"></a>Atributos y operaciones admitidos

En esta versión preliminar, puede agregar condiciones a roles integrados o roles personalizados. El uso de roles personalizados permite conceder solo los permisos o las acciones de datos esenciales a los usuarios. Los roles integrados admitidos en esta versión preliminar incluyen [Lector de datos de Storage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader), [Colaborador de datos de Storage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) y [Propietario de datos de Storage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner).

Si trabaja con condiciones basadas en [etiquetas de índice de blobs](../blobs/storage-manage-find-blobs.md), debe usar el rol *Propietario de datos de Storage Blob*, ya que en este rol están incluidos los permisos para las operaciones de etiquetas.

> [!NOTE]
> Las etiquetas de índice de blobs no se admiten en cuentas de almacenamiento de Data Lake Storage Gen2, las cuales usan un espacio de nombres jerárquico. No debe crear condiciones de asignación de roles mediante etiquetas de índice en cuentas de almacenamiento que tengan habilitada HNS.

El [formato de las condiciones de asignación de roles de Azure](../../role-based-access-control/conditions-format.md) permite el uso de atributos `@Resource` o `@Request` en las condiciones. Un atributo `@Resource` hace referencia a un atributo existente de un recurso de almacenamiento al que se accede, como una cuenta de almacenamiento, un contenedor o un blob. Un atributo `@Request` hace referencia a un atributo incluido en una solicitud de operación de almacenamiento.

Para obtener la lista completa de los atributos admitidos para cada DataAction, consulte [Acciones y atributos para las condiciones de asignación de roles de Azure en Azure Storage (versión preliminar)](storage-auth-abac-attributes.md).

## <a name="see-also"></a>Consulte también

- [Aspectos de seguridad para las condiciones de asignación de roles de Azure en Azure Storage (versión preliminar)](storage-auth-abac-security.md)
- [Acciones y atributos para las condiciones de asignación de roles de Azure en Azure Storage (versión preliminar)](storage-auth-abac-attributes.md)
- [¿Qué es el control de acceso basado en atributos de Azure (ABAC de Azure) (versión preliminar)?](../../role-based-access-control/conditions-overview.md)