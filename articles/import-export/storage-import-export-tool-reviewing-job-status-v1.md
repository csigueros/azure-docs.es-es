---
title: Revise los registros de copia de importaciones y exportaciones en Azure Import/Export | Microsoft Docs
description: Obtenga información sobre cómo revisar los registros de error y copia de importaciones y exportaciones para problemas de copia y carga de datos.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03f4cf19922f808decfd84fe0538930dee807b5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709254"
---
# <a name="review-copy-logs-from-imports-and-exports-via-azure-importexport"></a>Revise los registros de copia de importaciones y exportaciones a través de Azure Import/Export
Cuando el servicio Microsoft Azure Import/Export procesa las unidades que están asociadas a un trabajo de importación o exportación, escribe archivos de registro de copia en la cuenta de almacenamiento usada para la importación o exportación de blobs. 

El archivo de registro contiene el estado detallado de cada archivo que se importó o exportó. 

El servicio devuelve la dirección URL de cada archivo de registro de copia cuando se consulta el estado de un trabajo completado. Para obtener más información, consulte [Trabajo Get](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>URL de ejemplo

Las siguientes son direcciones URL de ejemplo de los archivos de registro de copia de un trabajo de importación con dos unidades:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 <!--See [Import/Export service Log File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-log) for the format of copy logs and the full list of status codes. ARCHIVED-->  

## <a name="next-steps"></a>Pasos siguientes

<!--* [Setting Up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md) ARCHIVED-->
 * [Preparación de unidades de disco duro para un trabajo de importación](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
<!--* [Repairing an import job](./storage-import-export-tool-repairing-an-import-job-v1.md)-->  
<!--* [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md)-->