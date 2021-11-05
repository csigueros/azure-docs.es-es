---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c1ab85b6da016b966bab7c66cd2a22f257d27d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068418"
---
Si planea agregar nuevos archivos al conjunto de datos, utilice la actualización incremental para agregar estos archivos al proyecto.   Cuando se habilita la **actualización incremental**, el conjunto de datos se comprueba periódicamente para agregar nuevos archivos a un proyecto, en función de la tasa de finalización del etiquetado.   La comprobación de los nuevos datos se detiene cuando el proyecto alcanza el número máximo de 500 000 archivos.

Para agregar más archivos al proyecto, use el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para cargarlas en la carpeta adecuada del almacenamiento de blobs. 

Active **Habilitar la actualización incremental** cuando desee que el proyecto supervise continuamente los nuevos datos en el almacén de datos. Estos datos se extraerán en el proyecto una vez al día cuando se habiliten. Después de agregar nuevos datos al almacén de datos, tendrá que esperar a que se muestren en el proyecto.  Puede ver una marca de tiempo de la última vez que se actualizaron los datos vez en la sección **Actualización incremental** de la pestaña **Detalles** del proyecto.
Anule la selección si no desea que los archivos nuevos del almacén de datos se agreguen a su proyecto.