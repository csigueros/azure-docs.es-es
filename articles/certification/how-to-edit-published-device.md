---
title: Edición de la instancia publicada de Azure Certified Device
description: Una guía para editar la información del dispositivo después de haberlo certificado y publicado a través del programa Azure Certified Device.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 07/13/2021
ms.custom: template-how-to
ms.openlocfilehash: 82993fb11ad6f2d1fde3d997494e5fc9151673e5
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731619"
---
# <a name="edit-your-published-device"></a>Edición del dispositivo publicado

Una vez que el dispositivo se ha certificado y publicado en el catálogo de Azure Certified Device, es posible que tenga que actualizar sus detalles. Esto puede deberse a una actualización de la lista de distribuidores, a los cambios en las direcciones URL de la página de compra o a las actualizaciones de las especificaciones de hardware (por ejemplo, la versión del sistema operativo o una nueva incorporación de componentes). Es posible que también tenga que actualizar el dispositivo IoT Plug and Play desde lo que ha cargado originalmente al repositorio de modelos.


## <a name="prerequisites"></a>Requisitos previos

- Debe iniciar sesión y tener un proyecto **aprobado** para el dispositivo en el [portal de Azure Certified Device](https://certify.azure.com). Si no tiene un dispositivo certificado, vea este [tutorial](tutorial-01-creating-your-project.md) para comenzar.


## <a name="editing-your-published-project-information"></a>Edición de la información del proyecto publicado

En el resumen del proyecto, debe observar que el proyecto está en modo de solo lectura, ya que ya se ha revisado y aceptado. Para realizar cambios, tendrá que solicitar una edición del proyecto y hacer que el equipo de certificación de Azure vuelva a aprobar la actualización.

1. Haga clic en el botón `Request Metadata Edit` ubicado en la parte superior de la página.  

    ![Solicitar la actualización de los metadatos](./media/images/request-metadata-edit.png)

1. Confirme la notificación en la página en la que se le solicitará que envíe su producto para revisarlo después de editarlo.
    > [!NOTE]
    > Al confirmar esta edición, **no** se quita el dispositivo del catálogo de Azure Certified Device si ya se ha publicado. La versión anterior del producto permanecerá en el catálogo hasta que haya vuelto a publicar el dispositivo.
    > Tampoco tendrá que repetir la sección Conexión y prueba del portal.

1. Una vez que se confirma esta advertencia, se pueden editar los detalles del dispositivo. Asegúrese de dejar una nota en la sección `Comments for Reviewer` de `Device Details` de qué se ha cambiado.

    ![Nota de edición de metadatos](./media/images/edit-notes.png)

1. En la página de resumen del proyecto, haga clic en `Submit for review` para que el equipo de certificación de Azure vuelva a aprobar los cambios.
1. Después de revisar y aprobar los cambios, puede volver a publicarlos en el catálogo a través del portal (consulte nuestro [tutorial](./tutorial-04-publishing-your-device.md)).

## <a name="editing-your-iot-plug-and-play-device-model"></a>Edición del modelo de dispositivo IoT Plug and Play

Una vez que haya enviado el modelo de dispositivo al repositorio de modelos público, no se puede quitar. Si actualiza el modelo de dispositivo y quiere volver a vincular el dispositivo certificado al nuevo modelo, **tendrá que volver a certificar el dispositivo** como un proyecto nuevo. Si lo hace, deje una nota en la sección "Comentarios para el revisor" a fin de que el equipo de certificación pueda quitar la entrada anterior del dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora ha editado correctamente el dispositivo en el catálogo de Azure Certified Device. ¡Puede desproteger los cambios en el catálogo o certificar otro dispositivo!
- [Catálogo de Azure Certified Device](https://devicecatalog.azure.com/)
- [Introducción a la certificación de un dispositivo](./tutorial-01-creating-your-project.md)
