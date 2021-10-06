---
title: Administración de directivas de copia de seguridad de la serie StorSimple 8000 | Microsoft Docs
description: Explica cómo se puede usar el servicio StorSimple Device Manager para crear y administrar copias de seguridad manuales, programaciones de copia de seguridad y retención de copia de seguridad en un dispositivo de la serie StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/15/2021
ms.author: alkohli
ms.openlocfilehash: 2fde4e0784e81c2127dd8097495b48ce506059bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589385"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Uso del servicio StorSimple Device Manager de Azure Portal para administrar directivas de copia de seguridad


## <a name="overview"></a>Información general

Este tutorial explica cómo utilizar la hoja **Directivas de copia de seguridad** del servicio StorSimple Device Manager para controlar los procesos de copia de seguridad y la retención de copias de seguridad de los volúmenes de StorSimple. También describe cómo realizar copias de seguridad manuales.

Cuando realice una copia de un volumen, puede elegir crear una instantánea local o una instantánea de nube. Si está realizando una copia de seguridad de un volumen anclado localmente, se recomienda que especifique una instantánea de nube. La realización de un gran número de instantáneas de un volumen anclado localmente junto con un conjunto de datos que tenga una gran cantidad actividad dará como resultado una situación en la que podría quedarse rápidamente sin espacio local. Si elige realizar instantáneas locales, se recomienda que realice menos instantáneas diarias para realizar una copia de seguridad del estado más reciente, conservarlas durante un día y eliminarlas.

Al realizar una instantánea en la nube de un volumen anclado localmente, se copian solo los datos modificados en la nube, donde se desduplican y se comprimen.

## <a name="the-backup-policy-blade"></a>Hoja Directiva de copia de seguridad

La hoja **Directiva de copia de seguridad** del dispositivo StorSimple le permite administrar las directivas de copia de seguridad y programar instantáneas locales y en la nube. Las directivas de copia de seguridad se usan para configurar las programaciones de copia de seguridad y la retención de copias de seguridad de una colección de volúmenes. Las directivas de copia de seguridad le permiten tomar una instantánea de varios volúmenes simultáneamente. Esto significa que las copias de seguridad creadas por una directiva de copia de seguridad serán copias preparadas para bloqueos.

La lista tabular de directivas de copia de seguridad también permite filtrar las directivas de copia de seguridad existentes por uno, o varios, de los siguientes campos:

* **Nombre de la directiva**: el nombre asociado a la directiva. Los distintos tipos de directivas incluyen:

  * Directivas programadas, que las crea explícitamente el usuario.
  * Directivas importadas, que se crearon originalmente en el Administrador de instantáneas de StorSimple. Dichas directivas tienen una etiqueta que describe el host de Administrador de instantáneas StorSimple del que se importaron las directivas.

  > [!NOTE]
  > La directivas de copia de seguridad automáticas o predeterminadas han dejado de habilitarse en el momento de la creación del volumen.

* **Última copia de seguridad correcta**: la fecha y la hora de la última copia de seguridad correcta realizada con esta directiva.

* **Siguiente copia de seguridad**: la fecha y la hora de la siguiente copia de seguridad programada iniciada por esta directiva.

* **Volúmenes**: los volúmenes asociados a la directiva. Cuando se crean copias de seguridad se agrupan todos los volúmenes asociados a una directiva de copia de seguridad.

* **Programaciones**: el número de programaciones asociadas a la directiva de copia de seguridad.

Estas son las operaciones utilizadas con frecuencia que se pueden realizar con las directivas de copia de seguridad:

* Agregar una directiva de copia de seguridad
* Incorporación o modificación de una programación
* Incorporación o eliminación de un volumen
* Eliminación de una directiva de copia de seguridad
* Creación de una copia de seguridad manual

## <a name="add-a-backup-policy"></a>Agregar una directiva de copia de seguridad

Agregue una directiva de copia de seguridad para programar automáticamente sus copias de seguridad. Cuando crea un volumen por primera vez, no hay ninguna directiva de copia de seguridad predeterminada asociada a este. Debe agregar y asignar una directiva de copia de seguridad para proteger los datos del volumen.

Realice los pasos siguientes en Azure Portal para agregar una directiva de copia de seguridad para el dispositivo StorSimple. Después de agregar la directiva, puede definir una programación (vea [Incorporación o modificación de una programación](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Incorporación o modificación de una programación

Es posible agregar o modificar una programación adjunta a una directiva de copia de seguridad de un dispositivo StorSimple. Siga estos pasos en Azure Portal para agregar o modificar una programación.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]

## <a name="disable-a-schedule"></a>Deshabilitación de una programación

Use el procedimiento siguiente si necesita deshabilitar una directiva de reserva. Por ejemplo, puede que le interese deshabilitar una programación que ha alcanzado el máximo de 64 copias de seguridad y, después, agregar una nueva programación para realizar más copias de seguridad.

Para deshabilitar una directiva de copia de seguridad, siga estos pasos:

1.  Vaya al dispositivo StorSimple y haga clic en **Directivas de copia de seguridad**.

1.  Explore en profundidad desde la directiva de copia de seguridad hasta la programación que quiera deshabilitar:

    1. Haga clic en la directiva de copia de seguridad para abrir las **Programaciones** de la directiva. 

    1. Vuelva a hacer clic en la directiva para abrir el cuadro de diálogo **Programaciones**.

    1. Haga clic en la programación que quiera deshabilitar para abrir **Configurar programación**. En el campo **Estado**, seleccione **Deshabilitado**.

  [ ![Ilustración en la que se muestran los pasos para deshabilitar una programación para una directiva de copia de seguridad en un dispositivo StorSimple. Cada paso está numerado, con la etiqueta de pantalla y el elemento sobre el que se actúa resaltado.](./media/storsimple-8000-manage-backup-policies-u2/modify-schedule-illustration.png) ](./media/storsimple-8000-manage-backup-policies-u2/modify-schedule-illustration.png#lightbox)

## <a name="add-or-remove-a-volume"></a>Incorporación o eliminación de un volumen

Puede agregar o quitar un volumen asignado a una directiva de copia de seguridad en el dispositivo StorSimple. Siga los pasos siguientes en Azure Portal para agregar o quitar un volumen.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Eliminación de una directiva de copia de seguridad

Realice los pasos siguientes en Azure Portal para eliminar una directiva de copia de seguridad del dispositivo StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Creación de una copia de seguridad manual

Siga estos pasos en Azure Portal para crear una copia de seguridad a petición (manual) de un único volumen.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el [uso del servicio Administrador de dispositivos de StorSimple para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

