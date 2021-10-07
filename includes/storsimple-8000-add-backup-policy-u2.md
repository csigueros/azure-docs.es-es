---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 09/14/2021
ms.author: alkohli
ms.openlocfilehash: 88ef9534c1a0048ef113ce0666ddcdd47171c234
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589390"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Para agregar una directiva de copia de seguridad de StorSimple

1. Vaya al dispositivo StorSimple y haga clic en **Directiva de copia de seguridad**.

2. En la hoja **Directiva de copia de seguridad**, haga clic en **+ Agregar directiva** en la barra de comandos.
   
    ![Agregar una directiva de copia de seguridad](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. En la hoja **Crear directiva de copia de seguridad**, realice los siguientes pasos:
   
   1. El campo **Seleccionar dispositivo** se rellena automáticamente según el dispositivo seleccionado.
   
   2. Especifique un **Nombre de directiva** de copia de seguridad que tenga entre 3 y 150 caracteres. Una vez creada la directiva, no se puede cambiar el nombre de la misma.
       
   3. Para asignar volúmenes a esta directiva de copia de seguridad, seleccione **Agregar volúmenes** y, en la lista tabular de volúmenes, haga clic en las casillas para asignar uno o más volúmenes.

       ![Incorporación de una directiva de copia de seguridad 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Para definir una programación para esta directiva de copia de seguridad, haga clic en la **primera programación** y luego modifique los parámetros siguientes:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Incorporación de una directiva de copia de seguridad 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. En **Tipo de instantánea**, seleccione **Nube** o **Local**.

       2. Indique la frecuencia de las copias de seguridad (especifique un número y elija **Días** o **Semanas** en la lista desplegable).

       3. Especifique una programación de retención.

       4. Escriba una fecha y hora para que comience la directiva de copia de seguridad.

       5. Haga clic en **Aceptar** para definir la programación.
       
       > [!NOTE]
       > Cuando llega a las 64 copias de seguridad de una programación y quiere conservarlas, puede [deshabilitar la programación](..\articles\storsimple\storsimple-8000-manage-backup-policies-u2.md#disable-a-schedule) y agregar una nueva que tenga una retención máxima de 64 copias de seguridad. Esta solución alternativa funcionará hasta que alcance el límite de 256 copias de seguridad por volumen. En ese momento, deberá eliminar las copias de seguridad anteriores para poder realizar otras nuevas.

   5. Haga clic en **Crear** para crear una directiva de copia de seguridad.
   
   6. Cuando la directiva de copia de seguridad se haya creado, recibirá una notificación. La directiva recién agregada se muestra en la vista tabular en la hoja **Directiva de copia de seguridad**.

       ![Incorporación de una directiva de copia de seguridad 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
