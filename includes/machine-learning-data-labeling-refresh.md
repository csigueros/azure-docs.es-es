---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 2a1c7c65721483e851a7686c38736f0664b965ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367576"
---
Si planea agregar nuevos archivos al conjunto de datos, utilice la actualización incremental para agregar estos archivos al proyecto.   Cuando la opción de **actualización incremental**  está habilitada, se comprueba periódicamente si hay nuevas imágenes el conjunto de datos que puedan agregarse a un proyecto en función de la tasa de finalización de la etiqueta.   La comprobación de los nuevos datos se detiene cuando el proyecto alcanza el número máximo de 500 000 archivos.

Para agregar más archivos al proyecto, use el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para cargarlas en la carpeta adecuada del almacenamiento de blobs. 

Active **Habilitar la actualización incremental** cuando desee que el proyecto supervise continuamente los nuevos datos en el almacén de datos. Estos datos se extraerán en el proyecto una vez al día cuando se habiliten. Después de agregar nuevos datos al almacén de datos, tendrá que esperar a que se muestren en el proyecto.  Puede ver una marca de tiempo de la última vez que se actualizaron los datos vez en la sección **Actualización incremental** de la pestaña **Detalles** del proyecto.
Anule la selección si no desea que los archivos nuevos del almacén de datos se agreguen a su proyecto.