---
title: Solución de problemas de importación y exportación en Azure Import/Export | Microsoft Docs
description: Obtenga información sobre cómo controlar problemas comunes al usar Azure Import/Export.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 16b805f909ad06397894c5c405027986ff5fd939
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709350"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Solución de problemas de Azure Import/Export
En este artículo se describe cómo solucionar problemas comunes al importar y exportar datos en Azure Import/Export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Hubo un error en una sesión de copia. ¿Qué debo hacer?  

Cuando se produce un error en una sesión de copia, hay dos opciones:  
* Si el error puede reintentarse, por ejemplo, si el recurso compartido de red estuvo sin conexión durante un breve período y ahora vuelve a tener conexión, puede reanudar la sesión de copia.
* Si el error puede reintentarse, por ejemplo, si especificó el directorio de archivos de origen incorrecto en los parámetros de la línea de comandos, debe anular la sesión de copia.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>No puedo reanudar o anular una sesión de copia.

Si la sesión de copia es la primera sesión de copia de una unidad, entonces el mensaje de error debe indicar: "La primera sesión de copia se no se puede reanudar o anular". En este caso, puede eliminar el archivo de diario antiguo y volver a ejecutar el comando.  

Si una sesión de copia no es la primera para una unidad, esta siempre se puede reanudar o anular.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Perdí el archivo de diario. Aun así, ¿puedo crear el trabajo?

El archivo de diario de una unidad contiene información de sesión completa de la copia de datos. Cuando se agregan archivos a la unidad, el archivo de diario se usa para crear un trabajo de importación. Si pierde el archivo de diario, tendrá que rehacer todas las sesiones de copia de la unidad.

## <a name="next-steps"></a>Pasos siguientes

<!--* [Set up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md)-->
* [Preparación de unidades de disco duro para un trabajo de importación](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Revisión del estado del trabajo con archivos de registro de copia](storage-import-export-tool-reviewing-job-status-v1.md)
<!--* [Repair an import job](storage-import-export-tool-repairing-an-import-job-v1.md)-->
<!--* [Repair an export job](storage-import-export-tool-repairing-an-export-job-v1.md)-->