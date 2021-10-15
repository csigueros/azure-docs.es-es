---
title: Actualización de los destinos de almacenamiento en Azure HPC Cache
description: Procedimientos para editar los destinos de almacenamiento en Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: v-erkel
ms.openlocfilehash: 151186d3d42ad799707f4a352e76cd154d3443bb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276196"
---
# <a name="edit-storage-targets"></a>Edición de los destinos de almacenamiento

Puede modificar destinos de almacenamiento con Azure Portal o la CLI de Azure. Por ejemplo, puede cambiar las directivas de acceso, los modelos de uso y las rutas de acceso de espacio de nombres de un destino de almacenamiento existente.

> [!TIP]
> Lea [Visualización y administración de destinos de almacenamiento](manage-storage-targets.md) para saber cómo eliminar o suspender destinos de almacenamiento, o hacer que escriban datos almacenados en caché en el almacenamiento back-end.

En función del tipo de almacenamiento, puede modificar estos valores de destino de almacenamiento:

* En el caso de los destinos de almacenamiento de blobs, se puede cambiar la ruta del espacio de nombres y la directiva de acceso.

* En el caso de los destinos de almacenamiento de NFS, se pueden cambiar estos valores:

  * Rutas de acceso del espacio de nombres
  * Directiva de acceso
  * El subdirectorio de exportación o la exportación de almacenamiento asociados a una ruta de acceso del espacio de nombres
  * Modelo de uso

* En el caso de los destinos de almacenamiento de ADLS-NFS, puede cambiar la ruta de acceso del espacio de nombres, la directiva de acceso y el modelo de uso.

No se puede editar el nombre, el tipo o el sistema de almacenamiento back-end de un destino de almacenamiento. Si tiene que cambiar estas propiedades, elimine el destino de almacenamiento y cree un reemplazo con el valor nuevo.

En el [vídeo de administración de Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) se muestra cómo editar un destino de almacenamiento en Azure Portal.

## <a name="change-a-blob-storage-targets-namespace-path-or-access-policy"></a>Cambio de la ruta o la directiva de acceso del espacio de nombres de un destino de almacenamiento de blobs

Las rutas de acceso de espacios de nombres son las rutas de acceso que los clientes usan para montar este destino de almacenamiento. Para obtener más información, lea [Planeamiento del espacio de nombres agregado](hpc-cache-namespace.md) y [Configuración del espacio de nombres agregado](add-namespace-paths.md).

Use Azure Portal o la CLI de Azure para cambiar la ruta o la directiva de acceso del espacio de nombres.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Use la página **Espacio de nombres** de su instancia de Azure HPC Cache para actualizar la ruta de acceso o la directiva de acceso de cliente del espacio de nombres. La página del espacio de nombres se describe con más detalle en el artículo [Configuración del espacio de nombres agregado](add-namespace-paths.md).

1. Haga clic en la ruta de acceso que quiere cambiar.
   ![Captura de pantalla de la página Espacio de nombres con el cursor sobre un elemento de la columna Ruta de acceso del espacio de nombres (primera columna a la izquierda). El nombre tiene el formato de hipervínculo y el cursor indica que se puede hacer clic en él.](media/edit-select-namespace.png)

1. Utilice la ventana de edición para escribir una nueva ruta de acceso virtual o para actualizar la directiva de acceso.

   ![Captura de pantalla de la página de espacio de nombres después de hacer clic en una ruta de acceso de espacio de nombres de blobs: los campos de edición aparecen en un panel a la derecha.](media/update-namespace-blob.png)

Después de realizar los cambios, haga clic en **Aceptar** para actualizar el destino de almacenamiento o en **Cancelar** para descartar los cambios.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[Configurar la CLI de Azure para Azure HPC Cache](./az-cli-prerequisites.md).

Para cambiar el espacio de nombres de un destino de almacenamiento de blobs con la CLI de Azure, use el comando [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update). Solo se puede cambiar el valor de `--virtual-namespace-path`.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Actualización de un destino de almacenamiento de NFS

En el caso de los destinos de almacenamiento de NFS, puede cambiar o agregar rutas de acceso de espacios de nombres virtuales, cambiar los valores de exportación o el subdirectorio de NFS al que apunta una ruta de acceso de espacio de nombres, y cambiar el modelo de uso.

Los destinos de almacenamiento en cachés con algunos tipos de valores de DNS personalizados también tienen un control para actualizar sus direcciones IP. (Este tipo de configuración es poco frecuente). Aprenda a actualizar la configuración de DNS en [Visualización y administración de destinos de almacenamiento](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only).

A continuación se muestran los detalles:

* [Cambio de los valores del espacio de nombres agregado](#change-aggregated-namespace-values) (ruta de acceso del espacio de nombres virtual, de exportación y del subdirectorio de exportación)
* [Cambiar el modelo de uso](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Cambio de los valores del espacio de nombres agregado

Puede usar Azure Portal o la CLI de Azure para cambiar la ruta de acceso del espacio de nombres orientado al cliente, la exportación del almacenamiento y el subdirectorio de exportación (si se usa). Si necesita cambiar la directiva de acceso, use Azure Portal.

Lea las instrucciones en [Incorporación de rutas de acceso del espacio de nombres de NFS](add-namespace-paths.md#nfs-namespace-paths) si necesita recordar cómo crear varias rutas válidas en un destino de almacenamiento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Use la página **Espacio de nombres** de su instancia de Azure HPC Cache para actualizar los valores del espacio de nombres, incluida la directiva de acceso de cliente. Esta página se describe con más detalle en el artículo [Configuración del espacio de nombres agregado](add-namespace-paths.md).

![Captura de pantalla de la página del espacio de nombres del portal con la página de actualización de NFS abierta a la derecha](media/update-namespace-nfs.png)

1. Haga clic en el nombre de la ruta de acceso que desee cambiar.
1. Utilice la ventana de edición para escribir nuevos valores de ruta de acceso virtual, de exportación o de subdirectorio, o para seleccionar una directiva de acceso diferente.
1. Después de realizar los cambios, haga clic en **Aceptar** para actualizar el destino de almacenamiento o en **Cancelar** para descartar los cambios.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[Configurar la CLI de Azure para Azure HPC Cache](./az-cli-prerequisites.md).

Use la opción ``--junction`` del comando [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) para cambiar la ruta de acceso del espacio de nombres, la exportación de NFS o el subdirectorio de exportación.

El parámetro ``--junction`` usa estos valores:

* ``namespace-path``: Ruta de acceso del archivo virtual orientado al cliente
* ``nfs-export``: Exportación del sistema de almacenamiento que se va a asociar a la ruta de acceso orientada al cliente
* ``target-path`` (opcional): Subdirectorio de la exportación, si es necesario

Ejemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Debe proporcionar los tres valores para cada ruta de acceso en la instrucción ``--junction``. Use los valores existentes en los valores que no quiera cambiar.

El nombre de la memoria caché, el nombre del destino de almacenamiento y el grupo de recursos también son obligatorios en todos los comandos de actualización.

Comando de ejemplo:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Cambio del modelo de uso

El modelo de uso influye en el modo en que la memoria caché conserva los datos. Consulte [Descripción de los modelos de uso de caché](cache-usage-models.md) para más información.

> [!NOTE]
> No se puede cambiar entre **Lectura de textos densos y poco frecuentes** y otros modelos de uso. Consulte [Descripción de los modelos de uso de caché](cache-usage-models.md#change-usage-models) para más información.

Para cambiar el modelo de uso de un destino de almacenamiento de NFS, use uno de estos métodos.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Abra la página **Destinos de almacenamiento** en Azure Portal. Haga clic en el nombre de un destino de almacenamiento de la lista para abrir su página de edición.

![captura de pantalla de la página de edición de un destino de almacenamiento de NFS](media/edit-storage-nfs.png)

Use el selector desplegable para elegir un nuevo modelo de uso. Haga clic en **Aceptar** para actualizar el destino de almacenamiento o en **Cancelar** para descartar los cambios.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[Configurar la CLI de Azure para Azure HPC Cache](./az-cli-prerequisites.md).

Use el comando [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update).

El comando update es casi idéntico al comando que se usa para agregar un destino de almacenamiento de NFS. Consulte [Creación de un destino de almacenamiento de NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) para obtener más información y ejemplos.

Para cambiar el modelo de uso, actualice la opción ``--nfs3-usage-model``. Ejemplo: ``--nfs3-usage-model WRITE_WORKLOAD_15``

El nombre de la memoria caché, el nombre del destino de almacenamiento y el grupo de recursos son obligatorios.

Si quiere, compruebe los nombres de los modelos de uso con el comando [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Si la memoria caché se ha detenido o no tiene un estado correcto, la actualización se aplicará después de que la memoria caché vuelva al estado correcto.

---

## <a name="update-an-adls-nfs-storage-target"></a>Actualización de un destino de almacenamiento de ADLS-NFS

De forma similar a los destinos de NFS, puede cambiar la ruta de acceso del espacio de nombres y el modelo de uso para los destinos de almacenamiento de ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Cambio de la ruta de acceso de un espacio de nombres de ADLS-NFS

Use la página **Espacio de nombres** de su instancia de Azure HPC Cache para actualizar los valores de espacio de nombres. Esta página se describe con más detalle en el artículo [Configuración del espacio de nombres agregado](add-namespace-paths.md).

![Captura de pantalla de la página del espacio de nombres del portal con la página de actualización de NFS abierta a la derecha](media/update-namespace-adls.png)

1. Haga clic en el nombre de la ruta de acceso que desee cambiar.
1. Utilice la ventana de edición para escribir nuevas rutas de acceso virtual o para actualizar la directiva de acceso.
1. Después de realizar los cambios, haga clic en **Aceptar** para actualizar el destino de almacenamiento o en **Cancelar** para descartar los cambios.

### <a name="change-adls-nfs-usage-models"></a>Cambio de los modelos de uso de ADLS-NFS

La configuración de los modelos de uso de ADLS-NFS es idéntica a la selección del modelo de uso de NFS. Lea las instrucciones del portal en [Cambio del modelo de uso](#change-the-usage-model) en la sección de NFS anterior. Hay herramientas adicionales para actualizar los destinos de almacenamiento de ADLS-NFS en desarrollo.

## <a name="next-steps"></a>Pasos siguientes

* Lea [Administración de destinos de almacenamiento](manage-storage-targets.md) para obtener información sobre cómo detener, eliminar y vaciar destinos de almacenamiento individuales.
* Lea [Incorporación de destinos de almacenamiento](hpc-cache-add-storage.md) para más información sobre opciones de destino de almacenamiento.
* Lea [Planeamiento del espacio de nombres agregado](hpc-cache-namespace.md) para obtener más recomendaciones sobre el uso de rutas de acceso virtuales.
