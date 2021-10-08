---
title: Retenciones legales para datos de blob inmutables
titleSuffix: Azure Storage
description: Una suspensión legal almacena los datos de blob en un formato WORM (escribir una vez, leer muchas) hasta que se borra explícitamente. Use una suspensión legal cuando se desconozca el período de tiempo durante el cual los datos deben mantenerse en un estado WORM.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 39108c5c5fd823e313ce616fbb4f6d6135e7d81e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645766"
---
# <a name="legal-holds-for-immutable-blob-data"></a>Retenciones legales para datos de blob inmutables

Una suspensión legal es una directiva de inmutabilidad temporal que se puede aplicar con fines de investigación jurídica o con directivas de protección generales. Una suspensión legal almacena los datos de blob en un formato WORM (escribir una vez, leer muchas) hasta que se borra explícitamente. Cuando hay en vigor una suspensión legal, los blobs se pueden crear y leer, pero no modificar ni eliminar. Use una suspensión legal cuando se desconozca el período de tiempo durante el cual los datos deben mantenerse en un estado WORM.

Para más información sobre las directivas de inmutabilidad en Blob Storage, consulte [Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md).

## <a name="legal-hold-scope"></a>Ámbito de la suspensión legal

Una directiva de suspensión legal se puede configurar en cualquiera de los ámbitos siguientes:

- Directiva a nivel de versión (versión preliminar): Se puede configurar una suspensión legal a nivel de versión de blob individual para la administración pormenorización de datos confidenciales.
- Directiva a nivel de contenedor: Una suspensión legal configurada a nivel de contenedor se aplica a todos los blobs de ese contenedor. Los blobs individuales no se pueden configurar con sus propias directivas de inmutabilidad.

### <a name="version-level-policy-scope-preview"></a>Ámbito de la directiva a nivel de versión (versión preliminar)

Para configurar una suspensión legal en una versión de blob, primero debe habilitar la inmutabilidad a nivel de versión en el contenedor primario. La inmutabilidad a nivel de versión no se puede deshabilitar después de habilitarla. Para más información, consulte [Habilitación de la compatibilidad con la inmutabilidad a nivel de versión en un contenedor](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container).

Una vez habilitada la inmutabilidad a nivel de versión para un contenedor, ya no se puede establecer una suspensión legal a nivel de contenedor. Las suspensiones legales deben aplicarse a versiones de blob individuales. Se puede configurar una suspensión legal para la versión actual o una versión anterior de un blob.

Las directivas de suspensión legal a nivel de versión requieren que el control de versiones de blobs esté habilitado para la cuenta de almacenamiento. Para obtener información sobre cómo habilitar el control de versiones de blobs, consulte [Habilitación y administración del control de versiones de blobs](versioning-enable.md). Tenga en cuenta que habilitar el control de versiones puede afectar a la facturación. Para más información, consulte la sección **Precios y facturación** de [Control de versiones de blobs](versioning-overview.md#pricing-and-billing).

Para más información sobre cómo habilitar una suspensión legal a nivel de versión, consulte [Configuración o eliminación de una suspensión legal](immutable-policy-configure-version-scope.md#configure-or-clear-a-legal-hold).

### <a name="container-level-scope"></a>Ámbito a nivel de contenedor

Al configurar una suspensión legal para un contenedor, esa suspensión se aplica a todos los objetos del contenedor. Cuando se borra la suspensión legal, los clientes pueden volver a escribir y eliminar objetos en el contenedor, a menos que también haya en vigor una directiva de retención con duración definida para el contenedor.

Cuando se aplica una suspensión legal a un contenedor, todos los blobs existentes pasan al estado WORM inmutable en menos de 30 segundos. Todos los nuevos blobs que se carguen en ese contenedor protegido mediante directiva también pasarán a un estado inmutable. Una vez que todos los blobs estén en un estado inmutable, no se permite ninguna operación de sobrescritura o eliminación en el contenedor inmutable. En el caso de una cuenta con un espacio de nombres jerárquico, no se puede cambiar el nombre de los blobs ni moverlos a un directorio diferente.

Para información sobre cómo configurar una suspensión legal con ámbito de nivel de contenedor, consulte [Configuración o eliminación de una suspensión legal](immutable-policy-configure-container-scope.md#configure-or-clear-a-legal-hold).

#### <a name="legal-hold-tags"></a>Etiquetas de suspensión legal

Una suspensión legal a nivel de contenedor debe estar asociada a una o varias etiquetas alfanuméricas definidas por el usuario que sirven como cadenas de identificador. Por ejemplo, una etiqueta puede incluir un identificador de caso o un nombre de evento.

#### <a name="audit-logging"></a>Registro de auditoría

Cada contenedor con una suspensión legal en vigor proporciona un registro de auditoría de directivas. El registro contiene el Id. de usuario, el tipo de comando, las marcas de tiempo y las etiquetas de suspensión legal. El registro de auditoría se conserva mientras dure la directiva, de acuerdo con las directrices de regulación SEC 17a-4(f).

El [registro de actividad de Azure](../../azure-monitor/essentials/platform-logs-overview.md) ofrece un registro más completo de todas las actividades del servicio de administración. Los [registros de recursos de Azure](../../azure-monitor/essentials/platform-logs-overview.md) conservan información sobre las operaciones de datos. Es responsabilidad del usuario almacenar dichos registros de forma persistente, ya que podría ser obligatorio por ley o por otros fines.

#### <a name="limits"></a>Límites

Los límites siguientes se aplican a las suspensiones legales a nivel de contenedor:

- En el caso de una cuenta de almacenamiento, el número máximo de contenedores con un valor de suspensión legal es 10 000.
- En un contenedor, el número máximo de etiquetas de suspensión legal es diez.
- La longitud mínima de una etiqueta de suspensión legal es de tres caracteres alfanuméricos. La longitud máxima es de 23 caracteres alfanuméricos.
- Para un contenedor, se conservan hasta diez registros de auditoría de directiva de suspensión legal durante la vigencia de la directiva.

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre la protección de datos](data-protection-overview.md)
- [Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md)
- [Directivas de retención con duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md)
- [Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)](immutable-policy-configure-version-scope.md)
- [Configuración de directivas de inmutabilidad para contenedores](immutable-policy-configure-container-scope.md)
