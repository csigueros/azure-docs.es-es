---
title: Guía sobre la eliminación temporal en el Explorador de Azure Storage | Microsoft Docs
description: Eliminación temporal en el Explorador de Azure Storage
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 0d097e769bfea8ff9c65921e8f0d851372187a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564911"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Eliminación temporal del Explorador de Azure Storage

La eliminación temporal ayuda a mitigar el impacto de una eliminación accidental de datos críticos. Esta guía le ayudará a comprender cómo puede aprovechar esta característica en el Explorador de Storage. Antes de continuar, se recomienda que lea más información sobre la [eliminación temporal de blobs](../blobs/soft-delete-blob-overview.md).

## <a name="configuring-delete-retention-policy"></a>Configuración de la eliminación de la directiva de retención

Puede configurar la directiva de retención de eliminación para cada cuenta de almacenamiento en el Explorador de Storage. Abra el menú contextual de cualquier nodo "Contenedores de blobs" en la cuenta de almacenamiento y elija **Configurar la directiva de eliminación temporal...** .

La configuración de una nueva directiva puede tardar hasta 30 segundos en surtir efecto. La eliminación de datos sin esperar a que la nueva directiva surta efecto puede dar lugar a un comportamiento inesperado. Explorador de Storage espera 30 segundos antes de notificar una directiva configurada correctamente en el registro de actividad.

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>Eliminación temporal con el espacio de nombres jerárquico habilitado

La característica de eliminación temporal presenta diferencias fundamentales entre los contenedores de blobs con o sin el espacio de nombres jerárquico (HNS) habilitado.

Los contenedores de blobs con HNS habilitado tienen directorios reales. Esos directorios también se pueden eliminar temporalmente. Cuando se elimina temporalmente un directorio real, todos los blobs o directorios activos que contiene serán inaccesibles. Estos blobs y directorios se recuperarán cuando el directorio se recupere y se descartarán cuando expire. Los blobs o directorios que ya se hayan eliminado temporalmente se conservarán tal y como están.

Los contenedores de blobs que no tienen HNS habilitado no permiten que coexistan blobs eliminados temporalmente y blobs activos con el mismo nombre. La carga de un blob con el mismo nombre que un blob eliminado temporalmente hará que el blob eliminado temporalmente se convierta en una instantánea del nuevo. En un contenedor de blobs con HNS habilitado, si hace lo mismo, el blob eliminado temporalmente coexistirá con el nuevo. Los contenedores de blobs con HNS habilitado también permiten la coexistencia de varios blobs eliminados temporalmente con el mismo nombre.

Cada blob o directorio eliminado temporalmente en un contenedor de blobs con HNS habilitado tiene una propiedad `DeletionID`. Esta propiedad distingue blobs o directorios con el mismo nombre. Los blobs eliminados temporalmente en contenedores de blobs que no tienen HNS habilitado no tienen una propiedad `DeletionID`.

Los contenedores de blobs que no tienen HNS habilitado también admiten el "control de versiones de blobs". Si el control de versiones de blobs está habilitado, cambia el comportamiento de determinadas operaciones, como la eliminación. Para más información, consulte [Guía de control de versiones de blobs del Explorador de Azure Storage](./storage-explorer-blob-versioning.md).

## <a name="view-soft-deleted-blobs"></a>Visualización de blobs eliminados temporalmente

En el Explorador de blobs, los blobs eliminados temporalmente se muestran en determinadas vistas.

En el caso de los contenedores de blobs que no tienen HNS habilitado, elija la vista "Blobs activos y blobs eliminados temporalmente" o "Todos los blobs y blobs sin una versión actual" para ver los blobs eliminados temporalmente.

En el caso de los contenedores de blobs con HNS habilitado, elija la vista "Blobs activos y eliminados temporalmente" o "Solo eliminados" para ver los blobs y directorios eliminados temporalmente.

## <a name="delete-blobs"></a>Eliminar blobs

Al eliminar blobs o directorios, Explorador de Storage comprueba la directiva de retención de eliminación actual de la cuenta de almacenamiento. A continuación, un cuadro de diálogo de confirmación le informa de lo que ocurrirá si continúa con la operación de eliminación. Si la eliminación temporal está deshabilitada, para habilitarla en el cuadro de diálogo de confirmación, seleccione el botón **Habilitar eliminación temporal**.

> [!WARNING]
> Puede que Explorador de Storage no detecte una nueva directiva de retención de eliminación si la acaba de guardar. Es muy recomendable esperar al menos 30 segundos para que la nueva directiva surta efecto antes de eliminar los datos.

## <a name="undelete-blobs"></a>Recuperación de blobs

Explorador de Storage puede recuperar blobs eliminados temporalmente de forma recursiva y en lotes.

Para recuperar blobs, seleccione los blobs eliminados temporalmente que desea recuperar y use la opción **Recuperar → Recuperar los elementos seleccionados** en la barra de herramientas o en el menú contextual.

También puede recuperar blobs de forma recursiva en un directorio. Si se incluye un directorio activo en la selección, Explorador de Storage recuperará todos los blobs o directorios eliminados temporalmente que contiene.

En los contenedores de blobs con HNS habilitado, la eliminación de un blob producirá un error si ya existe un blob activo con el mismo nombre.

> [!NOTE]
> Las instantáneas eliminadas temporalmente solo se pueden recuperar si se recupera el blob base. No hay ninguna manera de recuperar instantáneas individuales.

## <a name="undelete-blobs-by-date-range"></a>Recuperar blobs por intervalo de fechas

Explorador de Storage también permite recuperar blobs en función de su hora de eliminación.

Para recuperar blobs, seleccione los blobs eliminados temporalmente que desea recuperar y use la opción **Recuperar → Recuperar por fecha...** en la barra de herramientas o en el menú contextual.

**Recuperar por fecha...** funciona exactamente igual que **Recuperar los elementos seleccionados**, salvo que filtrará los blobs o directorios cuya hora de eliminación esté fuera del intervalo especificado.

## <a name="see-also"></a>Consulte también

- [Guía de control de versiones de blobs del Explorador de Azure Storage](./storage-explorer-blob-versioning.md)
- [Eliminación temporal para blobs](../blobs/soft-delete-blob-overview.md)
