---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7f4a2e5a7186032995ee277dfb9f2c226853163b
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573992"
---
## <a name="create-an-image-gallery"></a>Creación de una galería de imágenes

Una galería de imágenes es el recurso principal que se usa para habilitar el uso compartido de imágenes. Los caracteres permitidos para el nombre de la Galería son letras mayúsculas o minúsculas, números y puntos. El nombre de la galería no puede contener guiones.  Los nombres de las galerías deben ser únicos dentro de su suscripción. 

En el ejemplo siguiente se crea una galería denominada *myGallery* en el grupo de recursos *myGalleryRG*.

1. Inicie sesión en Azure Portal en https://portal.azure.com.
1. Use el tipo **Galería de imágenes compartidas** en el cuadro de búsqueda y seleccione **Galería de imágenes compartidas** en los resultados.
1. En la página **Galería de imágenes compartidas**, haga clic en **Agregar**.
1. En la página **Create shared image gallery** (Crear galería de imágenes compartidas), seleccione la suscripción correcta.
1. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba *myGalleryRG* como nombre.
1. En **Nombre**, escriba *myGallery* como nombre de la galería.
1. En **Región**, deje el valor predeterminado.
1. Puede escribir una descripción corta de la galería, como *Mi galería de imágenes para prueba.* Después, haga clic en **Revisar y crear**.
1. Una vez pasada la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.


## <a name="create-an-image-definition"></a>Creación de la definición de una imagen 

Las definiciones de imagen crean una agrupación lógica de imágenes. Estas se usan para administrar la información sobre las versiones de la imagen que se crean dentro de ellas. 

Los nombres de las definiciones de imagen pueden estar formados por letras mayúsculas o minúsculas, números, puntos y guiones. Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](../articles/virtual-machines/shared-image-galleries.md#image-definitions).

Cree la definición de imagen de la galería dentro de la galería. 

1. En la página de la nueva galería de imágenes, seleccione **Add a new image definition** (Agregar una nueva definición de imagen) en la parte superior de la página. 
1. En **Agregar nueva definición de imagen a la galería de imágenes compartidas**, en **Región**, seleccione *Este de EE. UU.* .
1. En **Nombre de definición de la imagen**, escriba un nombre como *myImageDefinition*.
1. En **Sistema operativo**, seleccione la opción correcta en función de la VM de origen.  
1. En **Generación de VM**, seleccione la opción en función de la VM de origen. En la mayoría de los casos, será *Gen 1*. Para obtener más información, consulte [Compatibilidad para máquinas virtuales de generación 2 en Azure](../articles/virtual-machines/generation-2.md).
1. En **Estado del sistema operativo**, seleccione la opción en función de la VM de origen. Para más información, consulte [Generalizada o Especializada](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).
1. En **Publicador**, escriba *myPublisher*. 
1. En **Oferta**, escriba *myOffer*.
1. En **SKU**, escriba *mySKU*.
1. Cuando termine, seleccione **Revisar y crear**.
1. Después de que la definición de imagen pasa la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.


## <a name="create-an-image-version"></a>Creación de la versión de una imagen

 Al elegir las regiones de destino de la replicación, recuerde que también debe incluir la región de *origen* como destino de la replicación.

Los pasos para crear una versión de la imagen son ligeramente diferentes, en función de si el origen es una imagen generalizada o una instantánea de una VM especializada. 


1. En la página de la definición de imagen, seleccione **Agregar versión** en la parte superior de la página.
1. En **Región**, seleccione la región donde quiera que se cree la imagen.
1. En **Número de versión**, escriba un número como *1.0.0*. El nombre de la versión de la imagen debe seguir el formato *principal*. *secundaria*. *revisión* con números enteros. 
1. En **Imagen de origen**, seleccione la imagen administrada de origen en la lista desplegable. Consulte la tabla siguiente para obtener detalles específicos de cada tipo de origen.

    | Origen | Otros campos |
    |---|---|
    | Discos o instantáneas | - En **Disco del SO**, seleccione el disco o la instantánea correspondiente en la lista desplegable. <br> - Para agregar un disco de datos, escriba el número de unidad lógica (LUN) y, a continuación, seleccione el disco de datos correspondiente en la lista desplegable. |
    | Versión de la imagen | - Seleccione la **galería de origen** en la lista desplegable. <br> - Seleccione la definición de imagen correcta en la lista desplegable. <br>- Seleccione la versión de la imagen existente que quiere usar en la lista desplegable. |
    | Imagen administrada | Seleccione la **imagen de origen** en la lista desplegable. <br>La imagen administrada debe estar en la misma región que eligió en **Detalles de la instancia**.
    | VHD en una cuenta de almacenamiento | Seleccione **Examinar** para elegir la cuenta de almacenamiento para el VHD. |

1. En **Excluir de las últimas**, deje el valor predeterminado de *No*.
1. En **End of life date** (Fecha del final de la duración), seleccione una fecha del calendario que sea un par de meses en el futuro.
1. En la pestaña **Replicación**, seleccione el tipo de almacenamiento en la lista desplegable.
1. Establezca el valor de **Número de réplicas predeterminado**; puede reemplazarlo para cada una de las regiones que agregue. 
1. Debe replicar en la región de origen, por lo que la primera réplica de la lista será en la región donde creó la imagen. Para agregar más réplicas, seleccione la región en la lista desplegable y ajuste el número de réplicas según sea necesario.
1. Cuando haya terminado, seleccione **Revisar y crear**. Azure validará la configuración.
1. Una vez que la versión de la imagen supere la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.

La imagen puede tardar un rato en replicarse en todas las regiones de destino.

También puede capturar una máquina virtual existente como imagen desde el portal. Para obtener más información, consulte [Creación de una imagen de una máquina virtual en el portal](../articles/virtual-machines/capture-image-portal.md).

## <a name="share-the-gallery"></a>Uso compartido de la galería

Se recomienda compartir el acceso en el nivel de la galería de imágenes. Los siguientes pasos le guían por el uso compartido de la galería que acaba de crear.

1. En la página de la nueva galería de imágenes, en el menú de la izquierda, seleccione **Control de acceso (IAM)** . 
1. En **Agregar una asignación de roles**, seleccione **Agregar**. Se abre el panel **Agregar una asignación de roles**. 
1. En **Rol**, seleccione **Lector**.
1. En **Asignar acceso a**, deje el valor predeterminado de **Usuario, grupo o entidad de servicio de Azure AD**.
1. En **Seleccionar**, escriba la dirección de correo electrónico de la persona a la que quiere invitar.
1. Si el usuario está fuera de su organización, verá el mensaje **This user will be sent an email that enables them to collaborate with Microsoft** (A este usuario se le enviará un correo electrónico que le permite colaborar con Microsoft). Seleccione el usuario con la dirección de correo electrónico y, luego, haga clic en **Guardar**.

Si el usuario está fuera de su organización, recibirá una invitación por correo electrónico para unirse a ella. Debe aceptarla para ver la galería y todas las definiciones y versiones de imágenes de su lista de recursos.
