---
title: Crear y ejecutar trabajos en la aplicación de Azure IoT Central | Microsoft Docs
description: Los trabajos de Azure IoT Central permiten funcionalidades de administración de dispositivos de forma masiva, como actualizar propiedades o ejecutar un comando.
ms.service: iot-central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/08/2021
ms.topic: how-to
ms.openlocfilehash: c24eac9d7446f983bd4dc1e8573d3c831c7ff7fb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442724"
---
# <a name="manage-devices-in-bulk-in-your-azure-iot-central-application"></a>Administración de dispositivos de manera masiva en la aplicación de Azure IoT Central

Puede usar Azure IoT Central para administrar los dispositivos conectados a gran escala mediante trabajos. Los trabajos le permiten realizar actualizaciones masivas de las propiedades de los dispositivos y la nube, además de ejecutar comandos. También puede usar archivo .csv para importar y exportar dispositivos de manera masiva. En este artículo se muestra cómo empezar a usar trabajos en su propia aplicación y cómo usar las características de importación y exportación.

## <a name="create-and-run-a-job"></a>Crear y ejecutar un trabajo

En el ejemplo siguiente se muestra cómo crear y ejecutar un trabajo para configurar el umbral ligero de un grupo de dispositivos de puerta de enlace logística. Use el Asistente para trabajos para crear y ejecutar trabajos. Puede guardar un trabajo para ejecutarlo más tarde.

1. En el panel izquierdo, seleccione **Trabajos**.

1. Seleccione **+ Nuevo trabajo**.

1. En la página **Configurar el trabajo**, escriba un nombre y una descripción para identificar el trabajo que va a crear.

1. Seleccione el grupo de dispositivos de destino al que desee que se aplique el trabajo. Puede ver el número de dispositivos a los que se aplicará la configuración del trabajo debajo de la selección de **Grupo de dispositivos**.

1. Elija **Propiedad de la nube**, **Propiedad** o **Comando** como el **Tipo de trabajo**:

    Para configurar un trabajo de **Propiedad**, seleccione una propiedad y establezca su nuevo valor. Para configurar un trabajo de **Comando**, seleccione el comando que desea ejecutar. Un trabajo de propiedad puede establecer varias propiedades.

    :::image type="content" source="media/howto-manage-devices-in-bulk/configure-job.png" alt-text="Captura de pantalla que muestra las selecciones para crear un trabajo de propiedad denominado Set Light Threshold":::.

    Seleccione **Guardar y salir** para agregar el trabajo a la lista de trabajos guardados en la página **Trabajos**. Más adelante puede volver a un trabajo desde la lista de trabajos guardados.

1. Seleccione **Siguiente** para ir a la página **Opciones de entrega**. La página **Opciones de entrega** permite establecer las opciones de entrega para este trabajo: **Lotes** y **Umbral de cancelación**.

    Los lotes permiten escalonar los trabajos para un gran número de dispositivos. El trabajo se divide en varios lotes, y cada lote contiene un subconjunto de dispositivos. Los lotes se ponen en cola y se ejecutan en secuencia.

    El umbral de cancelación permite cancelar automáticamente un trabajo si el número de errores supera el límite establecido. El umbral se puede aplicar a todos los dispositivos del trabajo o a lotes individuales.

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-delivery-options.png" alt-text="Captura de pantalla de la página de opciones de entrega del asistente para trabajos":::

1. Seleccione **Siguiente** para ir a la página **Programación**. La página **Programación** permite habilitar una programación para ejecutar el trabajo en el futuro:

    Elija una opción de periodicidad para la programación. Puede configurar un trabajo para que se ejecute:

    * Una sola vez
    * Diario
    * Semanal

    Defina una fecha y hora de inicio para un trabajo programado. La fecha y la hora son específicas de su zona horaria y no de la hora local del dispositivo.

    Para finalizar una programación periódica, elija:

    * **Este día** para establecer una fecha de finalización para la programación.
    * **Después** para establecer el número de veces que se debe ejecutar el trabajo.

    Los trabajos programados siempre se ejecutan en los dispositivos de un grupo de dispositivos, aunque la pertenencia al grupo cambie con el tiempo.

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule.png" alt-text="Captura de pantalla de la página de opciones de programación del asistente para trabajos":::

1. Seleccione **Siguiente** para ir a la página **Revisar**. En la página **Revisar** se muestran los detalles de configuración del trabajo. Seleccione **Programación** para programar el trabajo:

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule-review.png" alt-text="Captura de pantalla de la página de revisión del asistente para trabajos programados":::

1. En la página de detalles del trabajo se muestra información sobre los trabajos programados. Cuando se ejecute el trabajo programado, verá una lista de las instancias del trabajo. La ejecución del trabajo programado también forma parte de la lista de trabajos **Últimos 30 días**.

    En esta página, puede **Desprogramar** el trabajo o **Editar** el trabajo programado. Puede volver a un trabajo programado de la lista de trabajos programados.

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-schedule-details.png" alt-text="Captura de pantalla de la página de detalles de trabajos programados":::

1. En el asistente para trabajos, puede elegir no programar un trabajo y ejecutarlo inmediatamente. En la captura de pantalla siguiente se muestra un trabajo sin una programación que está listo para ejecutarse inmediatamente. Seleccione **Ejecutar** para ejecutar el trabajo:

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule-immediate.png" alt-text="Captura de pantalla de la página de revisión del Asistente para trabajos":::

1. Un trabajo pasa por las fases *pendiente*, *en ejecución* y *completado*. Los detalles de ejecución del trabajo contienen métricas de resultados, detalles de duración y una cuadrícula de lista de dispositivos.

    Una vez completado el trabajo, puede seleccionar **Registro de resultados** para descargar un archivo CSV de los detalles del trabajo, incluidos los dispositivos y sus valores de estado. Esta información puede ser útil para solucionar problemas.

    :::image type="content" source="media/howto-manage-devices-in-bulk/download-details.png" alt-text="Captura de pantalla que muestra el estado del dispositivo":::.

1. El trabajo aparece ahora en la lista **Últimos 30 días** de la página **Trabajos**. En esta página, se muestran los trabajos en ejecución actualmente y el historial de los trabajos ejecutados o guardados anteriormente.

    > [!NOTE]
    > Puede ver 30 días del historial de trabajos ejecutados anteriormente.

## <a name="manage-jobs"></a>Trabajos de administración

Para detener un trabajo en ejecución, ábralo y seleccione **Detener**. El estado del trabajo cambia para reflejar que el trabajo se ha detenido. En la sección **Resumen**, se muestran los dispositivos que se han completado, con errores o aún pendientes.

:::image type="content" source="media/howto-manage-devices-in-bulk/manage-job.png" alt-text="Captura de pantalla que muestra un trabajo en ejecución y el botón para detener un trabajo":::

Una vez que un trabajo se encuentra detenido, puede seleccionar **Continuar** para reanudar la ejecución del trabajo. El estado del trabajo cambia para reflejar que el trabajo está de nuevo en ejecución. La sección **Resumen** continúa actualizándose con el progreso más reciente.

:::image type="content" source="media/howto-manage-devices-in-bulk/stopped-job.png" alt-text="Captura de pantalla que muestra un trabajo detenido y el botón para continuar un trabajo":::

## <a name="copy-a-job"></a>Copia de un trabajo

Para copiar un trabajo existente, seleccione un trabajo ejecutado. Seleccione **Copiar** en la página de resultados del trabajo o en la página de detalles de los trabajos:

:::image type="content" source="media/howto-manage-devices-in-bulk/job-details-copy.png" alt-text="Captura de pantalla que muestra el botón Copiar":::

Se abrirá una copia de la configuración del trabajo para editarla y se anexará **Copia** al nombre del trabajo.

## <a name="view-job-status"></a>Ver estado del trabajo

Una vez creado un trabajo, la columna **Estado** se actualizará con el mensaje de estado más reciente del trabajo. En la tabla siguiente se muestran los valores posibles de *estado del trabajo*:

| Mensaje de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Este trabajo se ejecutó en todos los dispositivos.              |
| Con error               | Se produjo un error en este trabajo y no se ejecutó por completo en los dispositivos.  |
| Pending              | Este trabajo aún no ha comenzado a ejecutarse en los dispositivos.         |
| En ejecución              | Este trabajo se está ejecutando actualmente en los dispositivos.             |
| Detenido              | Un usuario ha detenido manualmente este trabajo.           |
| Canceled             | Este trabajo se ha cancelado porque se ha superado el umbral establecido en la página **Opciones de entrega**. |

El mensaje de estado va seguido de una descripción general de los dispositivos en el trabajo. En la tabla siguiente se muestran los valores posibles de *estado del dispositivo*:

| Mensaje de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Correcto            | El número de dispositivos en los que se ejecutó correctamente el trabajo.       |
| Con error               | El número de dispositivos en los que no se ejecutó correctamente el trabajo.       |

Para ver el estado del trabajo y todos los dispositivos afectados, abra el trabajo. Verá uno de los mensajes de estado siguientes junto a cada nombre de dispositivo:

| Mensaje de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | El trabajo se ejecutó en este dispositivo.                                     |
| Con error               | No se pudo ejecutar el trabajo en este dispositivo. El mensaje de error muestra más información.  |
| Pending              | Aún no se ejecutó el trabajo en este dispositivo.                                   |

Para descargar un archivo CSV que incluye los detalles del trabajo y la lista de dispositivos y sus valores de estado, seleccione **Registro de resultados**.

## <a name="filter-the-device-list"></a>Filtro de la lista de dispositivos

Puede filtrar la lista de dispositivos en la página **Detalles del trabajo** seleccionando el icono de filtro. Puede filtrar por los campos **Id. de dispositivo** o **Estado**:

:::image type="content" source="media/howto-manage-devices-in-bulk/filter.png" alt-text="Captura de pantalla que muestra las selecciones para filtrar una lista de dispositivos":::.

## <a name="customize-columns-in-the-device-list"></a>Personalización de las columnas en la lista de dispositivos

Puede agregar columnas a la lista de dispositivos; para ello, seleccione el icono de opciones de columna:

:::image type="content" source="media/howto-manage-devices-in-bulk/column-options.png" alt-text="Captura de pantalla que muestra el icono de las opciones de columna":::.

Use el cuadro de diálogo **Opciones de columna** para elegir las columnas de la lista de dispositivos. Seleccione las columnas que desea mostrar, seleccione la flecha derecha y después **Aceptar**. Para seleccionar todas las columnas disponibles, elija **Seleccionar todo**. Las columnas seleccionadas aparecen en la lista de dispositivos.

Las columnas seleccionadas se conservan en una sesión de usuario o en las sesiones de usuario que tienen acceso a la aplicación.

## <a name="rerun-jobs"></a>Repetición de la ejecución de los trabajos

Puede volver a ejecutar un trabajo que tenga dispositivos con errores. Seleccione **Volver a ejecutar en dispositivos con error**:

:::image type="content" source="media/howto-manage-devices-in-bulk/rerun.png" alt-text="Captura de pantalla que muestra el botón para volver a ejecutar un trabajo en los dispositivos con errores":::.

Escriba el nombre y la descripción del trabajo y, a continuación, seleccione **Volver a ejecutar el trabajo**. Se envía un nuevo trabajo para volver a intentar la acción en los dispositivos con errores.

> [!NOTE]
> No se pueden ejecutar más de cinco trabajos al mismo tiempo desde una aplicación de Azure IoT Central.
>
> Cuando se completa un trabajo y se elimina un dispositivo que se encuentra en la lista de dispositivos del trabajo, la entrada del dispositivo aparece como eliminada en el nombre del dispositivo. El vínculo de detalles del dispositivo no está disponible para el dispositivo eliminado.

## <a name="import-devices"></a>Importación de dispositivos

Para conectar un gran número de dispositivos a la aplicación, puede importar dispositivos en bloque desde un archivo CSV. Puede encontrar un archivo CSV de ejemplo en el [repositorio de ejemplos de Azure](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/bulk-upload-devices). El archivo CSV debe tener los encabezados de columna siguientes:

| Columna | Descripción |
| - | - |
| IOTC_DEVICEID | El identificador de dispositivo es un identificador único que el dispositivo usará para conectarse. Puede contener letras, números y el carácter `-` sin ningún espacio. La longitud máxima es de 128 caracteres. |
| IOTC_DEVICENAME | Opcional. El nombre del dispositivo es un nombre descriptivo que se mostrará en la aplicación. Si no se especifica, es el mismo que el identificador de dispositivo. La longitud máxima es de 148 caracteres. |

Para registrar dispositivos en la aplicación en bloque:

1. Elija **Dispositivos** en el panel izquierdo.

1. En el panel izquierdo, elija la plantilla de dispositivo para la que desea crear dispositivos en bloque.

    > [!NOTE]
    > Si aún no tiene una plantilla de dispositivo, puede importar dispositivos en **Todos los dispositivos** y registrarlos sin plantilla. Una vez que los dispositivos se han importado, puede migrarlos a una plantilla.

1. Seleccione **Import** (Importar).

    :::image type="content" source="media/howto-manage-devices-in-bulk/bulk-import-1.png" alt-text="Captura de pantalla que muestra la configuración de la acción de importación.":::

1. Seleccione el archivo CSV que tiene la lista de identificadores de dispositivo que desea importar.

1. La importación de dispositivos se inicia una vez que se ha cargado el archivo. En el panel Device Operations (Operaciones de dispositivo) puede realizar un seguimiento del estado de la importación. Este panel aparece automáticamente cuando se inicia la importación, o bien se puede acceder a él a través del icono de la campana de la esquina superior derecha.

1. Una vez que la importación finalizada, se muestra un mensaje de confirmación en el panel Device Operations (Operaciones de dispositivo).

    :::image type="content" source="media/howto-manage-devices-in-bulk/bulk-import-2.png" alt-text="Captura de pantalla que muestra la importación correcta.":::

Si se produce un error en la operación de importación de dispositivos, aparece un mensaje de error en el panel Device Operations (Operaciones de dispositivo). Se genera un archivo de registro descargable con todos los errores.

## <a name="export-devices"></a>Exportación de dispositivos

Para conectar un dispositivo real a IoT Central, necesita su cadena de conexión. Puede exportar los detalles del dispositivo de forma masiva para obtener la información que necesita para crear las cadenas de conexión del dispositivo. El proceso de exportación crea un archivo CSV con la identidad, el nombre y las claves de todos los dispositivos seleccionados.

Para realizar la exportación masiva de dispositivos desde la aplicación:

1. Elija **Dispositivos** en el panel izquierdo.

1. En el panel izquierdo, elija la plantilla de dispositivo desde la que desea exportar los dispositivos.

1. Seleccione los dispositivos que desea exportar y la acción **Exportar**.

    :::image type="content" source="media/howto-manage-devices-in-bulk/export-1.png" alt-text="Captura de pantalla que muestra la configuración de la acción de exportación.":::

1. Se inicia el proceso de exportación. Para realizar un seguimiento del estado, utilice el panel Device Operations (Operaciones de dispositivo).

1. Cuando la exportación finaliza, se muestra un mensaje de confirmación junto con un vínculo para descargar el archivo generado.

1. Seleccione el vínculo **Download File** (Descargar archivo) para descargar el archivo en una carpeta local del disco.

    ![Exportación correcta](./media/howto-manage-devices-in-bulk/export-2.png)

1. El archivo CSV exportado contiene las siguientes columnas: Id. de dispositivo, nombre del dispositivo, claves de dispositivo y huellas digitales de certificado X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Para más información sobre la conexión de dispositivos reales a la aplicación de IoT Central, consulte [Conectividad de dispositivos en Azure IoT Central](concepts-get-connected.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que aprendió a administrar dispositivos de manera masiva en la aplicación de Azure IoT Central, un paso siguiente que sugerimos es que obtenga más información sobre la [Edición de una plantilla de dispositivo](howto-edit-device-template.md).
