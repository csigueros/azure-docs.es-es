---
title: Restauración de recursos compartidos de archivos de Azure
description: Aprenda a usar Azure Portal para restaurar un recurso compartido de archivos completo o archivos específicos desde un punto de restauración creado por Azure Backup.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 464c9927f901a373310ad6d1c0812a5a5de1eaaa
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846847"
---
# <a name="restore-azure-file-shares"></a>Restauración de recursos compartidos de archivos de Azure

En este artículo se explica cómo usar Azure Portal para restaurar un recurso compartido de archivos completo o archivos específicos desde un punto de restauración creado por [Azure Backup](./backup-overview.md).

En este artículo, aprenderá a:

* Restauración de un recurso compartido de archivos de Azure completo.
* Restauración de archivos o carpetas individuales.
* Seguimiento del estado de la operación de restauración.

## <a name="steps-to-perform-a-restore-operation"></a>Pasos para realizar una operación de restauración

Para realizar una operación de restauración, siga estos pasos.

### <a name="select-the-file-share-to-restore"></a>Selección del recurso compartido de archivos que se va a restaurar

1. En [Azure Portal](https://portal.azure.com/), vaya al **Centro de copias de seguridad** y haga clic en **Restaurar**.

   :::image type="content" source="./media/restore-afs/backup-center-restore-inline.png" alt-text="Captura de pantalla que muestra cómo iniciar el proceso de restauración del recurso compartido de archivos de Azure." lightbox="./media/restore-afs/backup-center-restore-expanded.png":::

1. Seleccione **Azure Files (Azure Storage)** como tipo de origen de datos, elija el recurso compartido de archivos que desea restaurar y, a continuación, haga clic en **Continuar**.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-instance.png" alt-text="Captura de pantalla en la que se muestra la selección de Elementos de copia de seguridad.":::

### <a name="full-share-recovery"></a>Recuperación del recurso compartido completo

Puede usar esta opción para restaurar el recurso compartido de archivos completo en la ubicación original o en otra alternativa.

1. Después de seleccionar **Continuar** en el paso anterior, se abre el panel **Restaurar**. Para seleccionar el punto de restauración que quiere usar para realizar la operación de restauración, elija el texto del vínculo **Seleccionar** debajo del cuadro de texto **Punto de restauración**.

    ![Seleccionar el punto de restauración mediante la opción Seleccionar](./media/restore-afs/select-restore-point.png)

1. El panel de contexto **Seleccionar punto de restauración** se abre a la derecha y muestra los puntos de restauración disponibles para el recurso compartido de archivos seleccionado. Seleccione el punto de restauración que desee usar para la operación de restauración y seleccione **Aceptar**.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-restore-point-inline.png" alt-text="Captura de pantalla que muestra la selección del punto de restauración." lightbox="./media/restore-afs/azure-file-share-select-restore-point-expanded.png":::

    >[!NOTE]
    >De forma predeterminada, el panel **Seleccionar punto de restauración** muestra los puntos de restauración de los últimos 30 días. Si desea examinar los puntos de restauración que se crearon durante un período de tiempo específico, proporcione el intervalo. Para ello, elija la **Fecha de inicio** y la **Fecha de finalización** y seleccione el botón **Actualizar**.

1. El siguiente paso consiste en elegir la **ubicación de restauración**. En la sección **Destino de la recuperación**, especifique dónde o cómo restaurar los datos. Seleccione una de las dos opciones siguientes mediante el botón de alternancia:

    * **Ubicación original**: restaure el recurso compartido de archivos completo en la misma ubicación que el origen inicial.
    * **Ubicación alternativa**: restaure el recurso compartido de archivos completo en una ubicación alternativa y mantenga el recurso compartido de archivos original como está.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Restauración en la ubicación original (recurso compartido completo)

1. Seleccione **Ubicación original** como **Destino de recuperación** y seleccione si prefiere omitir o sobrescribir cuando haya conflictos; para ello, elija la opción adecuada de la lista desplegable **En caso de conflictos**.

1. Seleccione **Restaurar** para iniciar la operación de restauración.

   :::image type="content" source="./media/restore-afs/azure-file-share-original-location-recovery.png" alt-text="Captura de pantalla que muestra la selección de Restaurar para iniciar.":::

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Restauración en una ubicación alternativa (recurso compartido completo)

1. Seleccione **Ubicación alternativa** como **Destino de recuperación**.
1. Seleccione la cuenta de almacenamiento de destino en la que desea restaurar el contenido del que se ha realizado una copia de seguridad, en la lista desplegable **Cuenta de almacenamiento**.
1. En la lista desplegable **Seleccionar recurso compartido de archivos**, se muestran los recursos compartidos de archivos presentes en la cuenta de almacenamiento seleccionada en el paso 2. Seleccione el recurso compartido de archivos en el que desea restaurar el contenido de la copia de seguridad.
1. En el cuadro **Nombre de la carpeta**, especifique el nombre de la carpeta que desea crear en el recurso compartido de archivos de destino con el contenido restaurado.
1. Seleccione si desea omitir o sobrescribir si hay conflictos.
1. Después de escribir los valores adecuados en todos los cuadros, seleccione **Restaurar** para iniciar la operación de restauración.

   :::image type="content" source="./media/restore-afs/azure-file-share-alternate-location-recovery.png" alt-text="Captura de pantalla que muestra la selección de Ubicación alternativa.":::

### <a name="item-level-recovery"></a>Recuperación a nivel de elemento

Puede usar esta opción para restaurar archivos o carpetas en la ubicación original o en otra alternativa.

1. Vaya al **Centro de copias de seguridad** y seleccione **Instancias de copia de seguridad** en el menú, con el tipo de origen de datos **Azure Storage (Azure Files)** seleccionado.
1. Seleccione el recurso compartido de archivos para el que desea realizar una recuperación de nivel de elemento.

   El menú del elemento de copia de seguridad aparece con la opción **Recuperación de archivos**.

    ![Selección de Recuperación de archivos](./media/restore-afs/file-recovery.png)

1. Después de seleccionar **Recuperación de archivos**, se abre el panel **Restaurar**. Para elegir el punto de restauración que quiere usar para realizar la operación de restauración, seleccione el texto del vínculo **Seleccionar** debajo del cuadro de texto **Punto de restauración**.

    ![Seleccionar el punto de restauración mediante el vínculo Seleccionar](./media/restore-afs/select-restore-point.png)

1. El panel de contexto **Seleccionar punto de restauración** se abre a la derecha y muestra los puntos de restauración disponibles para el recurso compartido de archivos seleccionado. Seleccione el punto de restauración que desee usar para la operación de restauración y seleccione **Aceptar**.

    ![Seleccionar punto de restauración](./media/restore-afs/restore-point.png)

1. El siguiente paso consiste en elegir la **ubicación de restauración**. En la sección **Destino de la recuperación**, especifique dónde o cómo restaurar los datos. Seleccione una de las dos opciones siguientes mediante el botón de alternancia:

    * **Ubicación original**: restaure los archivos o carpetas seleccionados en el mismo recurso compartido de archivos que el origen inicial.
    * **Ubicación alternativa**: restaure los archivos o carpetas seleccionados en una ubicación alternativa y mantenga el contenido del recurso compartido de archivos original como está.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Restauración en la ubicación original (recuperación de nivel de elemento)

1. Seleccione **Ubicación original** como **Destino de recuperación** y seleccione si prefiere omitir o sobrescribir cuando haya conflictos. Para ello, elija la opción adecuada de la lista desplegable **En caso de conflictos**.

    ![Ubicación original de la recuperación en el nivel de elementos](./media/restore-afs/original-location-item-level.png)

1. Para elegir los archivos o las carpetas que quiere restaurar, seleccione el botón **Agregar archivo**. Se abrirá un panel de contexto a la derecha y mostrará el contenido del punto de recuperación del recurso compartido de archivos que seleccionó para restaurar.

    ![Selección de Agregar archivo](./media/restore-afs/add-file.png)

1. Active la casilla correspondiente al archivo o carpeta que quiere restaurar y elija **Seleccionar**.

    ![Selección de un archivo o una carpeta](./media/restore-afs/select-file-folder.png)

1. Repita los pasos del 2 al 4 para seleccionar varios archivos o carpetas para la restauración.
1. Después de seleccionar todos los elementos que quiere restaurar, seleccione **Restaurar** para iniciar la operación de restauración.

    ![Seleccionar Restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Restauración en una ubicación alternativa (recuperación de nivel de elemento)

1. Seleccione **Ubicación alternativa** como **Destino de recuperación**.
1. Seleccione la cuenta de almacenamiento de destino en la que desea restaurar el contenido del que se ha realizado una copia de seguridad, en la lista desplegable **Cuenta de almacenamiento**.
1. En la lista desplegable **Seleccionar recurso compartido de archivos**, se muestran los recursos compartidos de archivos presentes en la cuenta de almacenamiento seleccionada en el paso 2. Seleccione el recurso compartido de archivos en el que desea restaurar el contenido de la copia de seguridad.
1. En el cuadro **Nombre de la carpeta**, especifique el nombre de la carpeta que desea crear en el recurso compartido de archivos de destino con el contenido restaurado.
1. Seleccione si desea omitir o sobrescribir si hay conflictos.
1. Para elegir los archivos o las carpetas que quiere restaurar, seleccione el botón **Agregar archivo**. Se abrirá un panel de contexto a la derecha y mostrará el contenido del punto de recuperación del recurso compartido de archivos que seleccionó para restaurar.

    ![Selección de elementos para restaurar en una ubicación alternativa](./media/restore-afs/restore-to-alternate-location.png)

1. Active la casilla correspondiente al archivo o carpeta que quiere restaurar y elija **Seleccionar**.

    ![Selección del destino de la recuperación](./media/restore-afs/recovery-destination.png)

1. Repita los pasos del 6 al 8 para seleccionar varios archivos o carpetas para la restauración.
1. Después de seleccionar todos los elementos que quiere restaurar, seleccione **Restaurar** para iniciar la operación de restauración.

    ![Seleccione Aceptar después de seleccionar todos los archivos.](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Seguimiento de una operación de restauración

Una vez que se desencadene la operación de restauración, el servicio de copia de seguridad crea un trabajo para realizar su seguimiento. Azure Backup muestra las notificaciones sobre el trabajo en el portal. Para ver las operaciones del trabajo, seleccione el hipervínculo notificaciones.

![Seleccionar el hipervínculo de notificaciones](./media/restore-afs/notifications-link.png)

También puede supervisar el progreso de la restauración desde el almacén de Recovery Services:

1. Vaya al **Centro de copias de seguridad** y haga clic en **Trabajos de copia de seguridad** en el menú.
1. Filtre por los trabajos para el tipo de origen de datos y el estado del trabajo necesarios.

   :::image type="content" source="./media/restore-afs/backup-center-jobs-inline.png" alt-text="Captura de pantalla que muestra la selección de Trabajos de copia de seguridad." lightbox="./media/restore-afs/backup-center-jobs-expanded.png":::

1. Seleccione el nombre de la carga de trabajo correspondiente a su recurso compartido de archivos para ver más detalles sobre la restauración, como **Datos transferidos** o **Número de archivos restaurados**.

    ![Consulte los detalles restaurados](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre cómo [administrar copias de seguridad de recursos compartidos de archivos de Azure](manage-afs-backup.md).
