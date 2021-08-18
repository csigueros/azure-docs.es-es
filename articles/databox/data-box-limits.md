---
title: Límites de Azure Data Box | Microsoft Docs
description: Describe los límites del sistema y los tamaños recomendados de las conexiones y componentes de la matriz virtual de Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 820daf5400d60df85973d45840bd7a4a1c553198
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463947"
---
# <a name="azure-data-box-limits"></a>Límites de Azure Data Box

Tenga en cuenta estos límites al implementar y usar Microsoft Azure Data Box. En la tabla siguiente se describen los límites de Data Box.

## <a name="data-box-service-limits"></a>Límites de servicio de Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Límites de Data Box

- Data Box puede almacenar un máximo de 500 millones de archivos de importación y exportación en total.
- Data Box admite un máximo de 512 contenedores o recursos compartidos en la nube. Los directorios de nivel superior dentro del recurso compartido de usuario se convierten en contenedores o en recursos compartidos de archivos de Azure en la nube. 
- La capacidad de uso de Data Box puede ser inferior a 80 TB debido al consumo de espacio de metadatos de ReFS.
- Data Box admite un máximo de 10 conexiones de cliente a la vez en un recurso compartido de NFS.

## <a name="azure-storage-limits"></a>Límites de almacenamiento de Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Advertencias al cargar los datos


### <a name="for-import-order"></a>Para pedidos de importación

Las características de los pedidos de importación de Data Box incluyen:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>Para pedidos de exportación

Las características de los pedidos de exportación de Data Box incluyen:

- Data Box es un dispositivo basado en Windows y no admite nombres de archivo que distingan mayúsculas de minúsculas. Por ejemplo, si tiene dos archivos en Azure con nombres que solo difieran en el uso de mayúsculas y minúsculas, No use Data Box para exportarlos, ya que se sobrescribirán en el dispositivo.
- Si tiene etiquetas duplicadas en archivos de entrada o etiquetas que hacen referencia a los mismos datos, la exportación de Data Box podría omitir o sobrescribir los archivos. El número de archivos y el tamaño de los datos que se muestran en Azure Portal pueden diferir del tamaño real de los datos del dispositivo. 
- Data Box exporta los datos al sistema basado en Windows mediante SMB y está limitado por las limitaciones de SMB relativas a los archivos y las carpetas. Los archivos y las carpetas con nombres incompatibles no se exportan.
- Hay una correspondencia 1:1 entre el prefijo y el contenedor.
- El tamaño máximo del nombre de archivo es de 1024 caracteres. Los nombres de archivo que superan esta longitud no se exportan. 
- Se exportan los prefijos duplicados del archivo *xml* (cargados durante la creación del pedido). No se omiten los prefijos duplicados.
- Los blobs en páginas y los nombres de contenedor distinguen mayúsculas de minúsculas. Si hay discrepancias en el uso de mayúsculas, no se encontrarán ni el blob ni el contenedor.
 

## <a name="azure-storage-account-size-limits"></a>Límites de tamaño de cuenta de almacenamiento de Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Límites de tamaño de objeto de Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenciones de nomenclatura de archivos, blobs en páginas y blobs en bloques de Azure

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
