---
title: Captura de una imagen de máquina virtual con el portal
description: Cree una imagen de una máquina virtual mediante Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: b9218f6eebedd8f94c06a664518fc718f0eb7d19
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992124"
---
# <a name="create-an-image-of-a-vm-in-the-portal"></a>Creación de una imagen de máquina virtual en el portal

Puede crearse una imagen de una máquina virtual y, posteriormente, usarla para crear varias máquinas virtuales.

Para las imágenes almacenadas en una instancia de Shared Image Gallery, puede usar máquinas virtuales que ya tengan cuentas creadas (especializadas) o bien generalizar la máquina virtual antes de crear la imagen para quitar cuentas de máquina y otra información específica de las máquinas. Para generalizar una máquina virtual, consulte [Generalización de una máquina virtual](generalize.md). Para más información, consulte [Imágenes generalizadas y especializadas](shared-image-galleries.md#generalized-and-specialized-images).


## <a name="capture-a-vm-in-the-portal"></a>Captura de una máquina virtual en el portal 

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **Máquinas virtuales**.

2. Seleccione la máquina virtual en la lista.

3. En la página **Máquina virtual** de la máquina virtual, en el menú superior, seleccione **Capturar**.

   Aparece la página **Crear una imagen**.

5. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre, o seleccione en la lista desplegable el grupo de recursos que desea utilizar. Si quiere usar una galería existente, seleccione el grupo de recursos para la galería que quiere usar.

1. Para crear la imagen en una galería, seleccione **Yes, share it to a gallery as an image version** (Sí, compartirla en una galería como una versión de la imagen).
    
   Para crear solo una imagen administrada, seleccione **No, capture only a managed image** (No, capturar solo una imagen administrada). La máquina virtual debe haberse generalizado para crear una imagen administrada. La única información necesaria que falta es un nombre para la imagen.

6. Si desea eliminar la máquina virtual de origen tras haberse creado la imagen, seleccione **Eliminar automáticamente esta máquina virtual después de crear la imagen**. Esto no es recomendable.

1. En **Gallery details** (Detalles de la galería), seleccione la galería o elija **Crear** para crear una.

1. En **Estado del sistema operativo**, seleccione el estado generalizado o especializado. Para más información, consulte [Imágenes generalizadas y especializadas](shared-image-galleries.md#generalized-and-specialized-images).
 
1. Seleccione una definición de imagen o la opción de **crear** y proporcione un nombre e información para una nueva [definición de imagen](shared-image-galleries.md#image-definitions).

1. Escriba un número de [versión de imagen](shared-image-galleries.md#image-versions). Si esta es la primera versión de la imagen, escriba *1.0.0*.

1. Si quiere que se incluya la versión cuando especifique *última* como versión de la imagen, deje la opción **Excluir de las últimas** sin seleccionar.

1. Seleccione una fecha de **Fin de vida**. Esta fecha se puede usar para realizar un seguimiento de cuándo es necesario retirar las imágenes más antiguas.

1. En [Replicación](shared-image-galleries.md#replication), seleccione un recuento de réplicas predeterminado y, a continuación, seleccione las regiones adicionales en las que quiera replicar la imagen.

8. Cuando haya terminado, seleccione **Revisar y crear**.

1. Una vez que se haya superado la validación, seleccione **Crear** para crear la imagen.



## <a name="next-steps"></a>Pasos siguientes

- [Información general de Shared Image Galleries](shared-image-galleries.md)  
