---
title: Administración de los destinos de almacenamiento en Azure HPC Cache
description: Cómo suspender, quitar, forzar la eliminación y vaciar destinos de almacenamiento de Azure HPC Cache y cómo comprender el estado del destino de almacenamiento
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: v-erkel
ms.openlocfilehash: 5b6127a43ebd93b89ea3a648533c2b739fc58691
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274325"
---
# <a name="view-and-manage-storage-targets"></a>Visualización y administración de destinos de almacenamiento

La página de configuración de destinos de almacenamiento muestra información sobre cada destino de almacenamiento para HPC Cache y proporciona opciones para administrar destinos de almacenamiento individuales.

> [!TIP]
> Las instrucciones para enumerar los destinos de almacenamiento con la CLI de Azure se incluyen en el artículo [Adición de destinos de almacenamiento](hpc-cache-add-storage.md#view-storage-targets). Es posible que otras acciones enumeradas aquí aún no estén disponibles en la CLI de Azure.

![Captura de pantalla de la página Configuración > Destinos de almacenamiento en Azure Portal. Hay varios destinos de almacenamiento en la lista, y los encabezados de columna muestran Nombre, Tipo, Estado, Estado de aprovisionamiento, Dirección/contenedor y Modelo de uso para cada uno.](media/storage-targets-list-states.png)

## <a name="manage-storage-targets"></a>Administración de destinos de almacenamiento

Puede realizar acciones de administración en destinos de almacenamiento individuales. Estas acciones complementan las opciones de nivel de caché que se abordan en [Administración de la memoria caché](hpc-cache-manage.md).

Estos controles pueden ayudarle a recuperarse de una situación inesperada (como un destino de almacenamiento que no responde) y también le ofrece la capacidad de invalidar algunas acciones de caché automáticas (como escribir archivos modificados en el sistema de almacenamiento a largo plazo).

Abra la página **Destinos de almacenamiento** en Azure Portal. Haga clic en la imagen **...** en el extremo derecho de la lista de destinos de almacenamiento para abrir la lista de tareas.

![Captura de pantalla de la página de destinos de almacenamiento en Azure Portal, con el cursor sobre el menú expuesto haciendo clic en el símbolo de tres puntos (...) situado en el extremo derecho de la fila del destino de almacenamiento de la lista.](media/storage-target-manage-options.png)

Estas opciones están disponibles:

* **Vaciar**: escriba todos los cambios almacenados en caché en el almacenamiento de back-end.
* **Suspender**: detenga temporalmente el destino de almacenamiento para que no atienda las solicitudes.
* **Reanudar**: vuelva a poner un destino de almacenamiento suspendido en servicio.
* **Forzar eliminación**: elimine un destino de almacenamiento y omita algunos pasos de seguridad (**forzar la eliminación puede provocar la pérdida de datos**).
* **Eliminar**: elimine permanente un destino de almacenamiento.

Algunos destinos de almacenamiento también tienen la opción **Actualizar DNS** en este menú, que actualiza la dirección IP de destino de almacenamiento desde un servidor DNS personalizado. Esta configuración no es habitual.

Lea el resto de este artículo para obtener más información sobre estas opciones.

### <a name="write-cached-files-to-the-storage-target"></a>Escritura de archivos almacenados en caché en el destino de almacenamiento

La opción **Vaciar** indica a la memoria caché que copie inmediatamente los archivos modificados almacenados en la memoria caché en el sistema de almacenamiento back-end. Por ejemplo, si las máquinas cliente actualizan un archivo determinado varias veces, se mantiene en la memoria caché para un acceso más rápido y no se escribe en el sistema de almacenamiento a largo plazo durante un período que va de varios minutos a más de una hora.

La acción **Vaciar** indica a la memoria caché que escriba todos los archivos en el sistema de almacenamiento.

La memoria caché no aceptará solicitudes de los clientes para los archivos en este destino de almacenamiento hasta que se complete el vaciado.

Puede usar esta opción para asegurarse de que el almacenamiento de back-end se rellena antes de realizar una copia de seguridad, o para cualquier situación en la que desee asegurarse de que el almacenamiento de back-end tiene actualizaciones recientes.

Esta opción se aplica principalmente a los modelos de uso que incluyen el almacenamiento en caché de escritura. Obtenga más información sobre el almacenamiento en caché de lectura y escritura en [Descripción de los modelos de uso de caché](cache-usage-models.md).

### <a name="suspend-a-storage-target"></a>Suspensión de un destino de almacenamiento

La característica de suspensión deshabilita el acceso de cliente a un destino de almacenamiento, pero no quita permanentemente el destino de almacenamiento de la memoria caché. Puede usar esta opción si necesita deshabilitar un sistema de almacenamiento back-end para mantenimiento, reparación o reemplazo.

### <a name="put-a-suspended-storage-target-back-in-service"></a>Volver a poner un destino de almacenamiento suspendido en servicio

Use **Reanudar** para cancelar la suspensión de un destino de almacenamiento.

### <a name="force-remove-a-storage-target"></a>Forzar la eliminación de un destino de almacenamiento

> [!NOTE]
> Esta opción puede provocar la pérdida de datos para el destino de almacenamiento afectado.

Si no se puede quitar un destino de almacenamiento con una acción de eliminación normal, puede usar la opción **Forzar eliminación** para eliminarlo de Azure HPC Cache.

Esta acción omite el paso que sincroniza los archivos de la memoria caché con los archivos del sistema de almacenamiento back-end. No hay ninguna garantía de que los cambios escritos en HPC Cache se escriban en el sistema de almacenamiento del back-end, por lo que los cambios se pueden perder si se usa esta opción.

Tampoco hay ninguna garantía de que el sistema de almacenamiento de back-end sea accesible después de quitarlo de la memoria caché.

Normalmente, forzar la eliminación solo se usa cuando un destino de almacenamiento deja de responder o está en mal estado. Esta opción le permite quitar el destino de almacenamiento no seguro en lugar de tener que realizar acciones más drásticas.

### <a name="delete-a-storage-target"></a>Eliminación de un destino de almacenamiento

Puede usar Azure Portal o la CLI de Azure para eliminar un destino de almacenamiento.

La opción de eliminación normal quita permanentemente el destino de almacenamiento de HPC Cache, pero primero sincroniza el contenido de la caché con el sistema de almacenamiento back-end. Es diferente de la opción de forzar eliminación, que no sincroniza los datos.

Esta acción quita la asociación del destino de almacenamiento con este sistema de Azure HPC Cache, pero no cambia el sistema de almacenamiento de back-end. Por ejemplo, si ha usado un contenedor Azure Blob Storage, el contenedor y su contenido siguen existiendo después de eliminarlo de la memoria caché. Puede agregar el contenedor a otra instancia de Azure HPC Cache, volver a agregarlo a esta caché o eliminarlo con Azure Portal.

Si hay una gran cantidad de datos modificados almacenados en la caché, la eliminación de un destino de almacenamiento puede tardar varios minutos en completarse. Espere a que finalice la acción para asegurarse de que los datos se almacenan de forma segura en el sistema de almacenamiento a largo plazo.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para quitar un destino de almacenamiento, abra la página **Destinos de almacenamiento**. Haga clic en "..." junto al destino de almacenamiento y elija **Eliminar** en el menú.

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[Configurar la CLI de Azure para Azure HPC Cache](./az-cli-prerequisites.md).

Use [az hpc-cache storage-target remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) para eliminar un destino de almacenamiento de la memoria caché.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Actualización de la dirección IP (solo configuraciones DNS personalizadas)

Si la memoria caché usa una configuración de DNS no predeterminada, es posible que la dirección IP de su destino de almacenamiento de NFS cambie debido a los cambios de DNS de back-end. Si el servidor DNS cambia la dirección IP del sistema de almacenamiento de back-end, Azure HPC Cache puede perder el acceso al sistema de almacenamiento.

Idealmente, debe trabajar con el administrador del sistema DNS personalizado de la memoria caché para planear las actualizaciones, ya que estos cambios hacen que el almacenamiento no esté disponible.

Si necesita actualizar la dirección IP proporcionada por DNS de un destino de almacenamiento, use la página **Destinos de almacenamiento**. Haga clic en el símbolo **...** de la columna derecha para abrir el menú contextual. Haga clic en **Actualizar DNS** para consultar el servidor DNS personalizado para obtener una nueva dirección IP.

![Captura de pantalla de la lista de destinos de almacenamiento Para un destino de almacenamiento, el menú "..." de la columna del extremo derecho está abierto y aparecen estas opciones: Vaciar, Suspender, Actualizar DNS, Forzar eliminación, Reanudar (esta opción está deshabilitada) y Eliminar.](media/refresh-dns.png)

Si se realiza correctamente, la actualización debe tardar menos de dos minutos. Solo puede actualizar un destino de almacenamiento a la vez; Espere a que se complete la operación anterior antes de intentar otra.

## <a name="understand-storage-target-state"></a>Información sobre el estado del destino de almacenamiento

La lista de destinos de almacenamiento muestra dos tipos de estado: **Estado** y **Estado de aprovisionamiento**.

* **Estado** indica el estado operativo del destino de almacenamiento. Este valor se actualiza periódicamente y le ayuda a comprender si el destino de almacenamiento está disponible o no para las solicitudes de cliente y también cuáles de las opciones de administración están disponibles.
* El **estado de aprovisionamiento** indica si la última acción para agregar o editar el destino de almacenamiento se ha realizado correctamente. Este valor solo se actualiza si edita el destino de almacenamiento.

El valor de **Estado** afecta a las opciones de administración que puede usar. Esta es una breve explicación de los valores y sus efectos.

* **Listo**: el destino de almacenamiento funciona con normalidad y está disponible para los clientes. Puede usar cualquiera de las opciones de administración en este destino de almacenamiento (excepto **Reanudar**, que solo es válido para los destinos de almacenamiento suspendidos).
* **Ocupado**: el destino de almacenamiento está procesando otra operación. Puede eliminar el destino de almacenamiento o forzar su eliminación.
* **Suspendido**: el destino de almacenamiento se ha desconectado. Todavía puede vaciar, eliminar o forzar la eliminación de este destino de almacenamiento. Elija **Reanudar** para volver a poner el destino en servicio.
* **Vaciando**: el destino de almacenamiento está escribiendo datos en el almacenamiento de back-end. El destino no puede procesar las solicitudes de cliente durante el vaciado, pero volverá automáticamente a su estado anterior una vez que termine de escribir los datos.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las [acciones de administración de nivel de caché](hpc-cache-manage.md)
* [Edición de un destino de almacenamiento](hpc-cache-edit-storage.md)
