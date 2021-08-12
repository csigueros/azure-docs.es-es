---
title: Directiva para conservar los manifiestos sin etiquetar
description: Aprenda a habilitar una directiva de retención en la opción Prémium de Azure Container Registry para que los manifiestos sin etiqueta se eliminen de forma automática después de un período definido.
ms.topic: article
ms.date: 04/26/2021
ms.openlocfilehash: 80e16c4b8cccdda2e171159cca807368b2648e82
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062837"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Establecimiento de una directiva de retención para manifiestos sin etiqueta

Azure Container Registry ofrece la opción de establecer una *directiva de retención* para los manifiestos de imagen almacenados que no tienen etiquetas asociadas (*manifiestos sin etiqueta*). Cuando se habilita una directiva de retención, los manifiestos sin etiqueta del registro se eliminan automáticamente después de un número de días establecido. Esta característica evita que el registro se llene con artefactos que no son necesarios y ayuda a ahorrar en los costos de almacenamiento. 

Puede usar Azure Cloud Shell o una instalación local de la CLI de Azure para ejecutar los ejemplos de comando de este artículo. Si quiere usarla de forma local, necesitará la versión 2.0.74 u otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli].

Una directiva de retención para los manifiestos sin etiquetar es actualmente una característica en vista previa (GB) de las instancias **Prémium** de Azure Container Registry. Para obtener información sobre los niveles de servicio de registro, consulte [SKU de Azure Container Registry](container-registry-skus.md).

> [!WARNING]
> Establezca una directiva de retención con cuidado: los datos de imagen eliminados son IRRECUPERABLES. Si tiene sistemas que extraen imágenes por hash de manifiesto (en lugar de por nombre de la imagen), no debe establecer una política de retención para los manifiestos sin etiqueta. La eliminación de imágenes sin etiqueta impedirá que esos sistemas extraigan las imágenes del registro. En lugar de extraer por manifiesto, considere la posibilidad de adoptar un esquema de *etiquetado único*, un [procedimiento recomendado](container-registry-image-tag-version.md).

## <a name="about-the-retention-policy"></a>Acerca de la directiva de retención

Azure Container Registry hace referencia al recuento de manifiestos en el registro. Cuando un manifiesto no está etiquetado, comprueba la directiva de retención. Si se habilita una directiva de retención, se pone en cola una operación de eliminación de manifiestos, con una fecha concreta y según el número de días establecido en la directiva.

Un trabajo de administración de colas independiente se encarga de procesar constantemente los mensajes, escalándolos según sea necesario. Por ejemplo, supongamos que ha etiquetado dos manifiestos con una hora de diferencia y en un registro con una directiva de retención de 30 días. Esos dos mensajes se pondrán en cola. A continuación, 30 días después y, aproximadamente, con una hora de diferencia, los mensajes se recuperan de la cola y se procesan, suponiendo que la directiva aún está en vigor.

Si el atributo `delete-enabled` de un manifiesto sin etiquetas se establece como `false`, el manifiesto está bloqueado y la directiva no lo elimina.

> [!IMPORTANT]
> La directiva de retención se aplica solo a los manifiestos sin etiquetas con marcas de tiempo *después* de que se habilite la directiva. Los manifiestos si etiqueta en el registro con marcas de tiempo anteriores no están sujetos a la directiva. Para ver otras opciones para eliminar datos de imágenes, consulte los ejemplos de [Eliminación de imágenes de contenedor en Azure Container Registry con la CLI de Azure](container-registry-delete.md).

## <a name="set-a-retention-policy---cli"></a>Establecimiento de una directiva de retención (CLI)

En el ejemplo siguiente se muestra cómo usar la CLI de Azure para establecer una directiva de retención para manifiestos sin etiqueta de un registro.

### <a name="enable-a-retention-policy"></a>Habilitación de una directiva de retención

De forma predeterminada, no se establece ninguna directiva de retención en un registro de contenedor. Para establecer o actualizar una directiva de retención, ejecute el comando [az acr config retention update][az-acr-config-retention-update] en la CLI de Azure. Puede especificar un número de días entre 0 y 365 para conservar los manifiestos sin etiqueta. Si no especifica un número de días, el comando establece un valor predeterminado de 7 días. Después del período de retención, se eliminarán automáticamente todos los manifiestos sin etiqueta del registro.

En el ejemplo siguiente se establece una directiva de retención de 30 días para los manifiestos sin etiqueta del registro *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

En el ejemplo siguiente se establece una directiva para eliminar cualquier manifiesto del registro en cuanto no esté etiquetado. Para crear esta directiva, establezca un período de retención de 0 días. 

```azurecli
az acr config retention update \
  --registry myregistry --status enabled \
  --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Validar una directiva de retención

Si habilita la directiva anterior con un período de retención de 0 días, puede comprobar rápidamente si se eliminan los manifiestos sin etiquetar:

1. Inserte una imagen de prueba `hello-world:latest` en su registro o sustituya otra imagen de prueba de su elección.
1. Desetiquete la imagen `hello-world:latest`; por ejemplo, use el comando [az acr repository untag][az-acr-repository-untag]. El manifiesto sin etiquetar permanecerá en el registro.
    ```azurecli
    az acr repository untag \
      --name myregistry --image hello-world:latest
    ```
1. A continuación, se elimina el manifiesto sin etiquetar en unos segundos. Puede comprobar la eliminación enumerando manifiestos en el repositorio; por ejemplo, puede usar para ello el comando [az acr repository show-manifests][az-acr-repository-show-manifests]. Si la imagen de prueba era la única del repositorio, se elimina el repositorio en sí.

### <a name="manage-a-retention-policy"></a>Administración de una directiva de retención

Para mostrar la directiva de retención establecida en un registro, ejecute el comando [az acr config retention show][az-acr-config-retention-show]:

```azurecli
az acr config retention show --registry myregistry
```

Para deshabilitar una directiva de retención de un registro, ejecute el comando [az acr config retention update][az-acr-config-retention-update] y establezca `--status disabled`:

```azurecli
az acr config retention update \
  --registry myregistry --status disabled \
  --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Establecimiento de una directiva de retención (portal)

También puede establecer la directiva de retención de un registro en [Azure Portal](https://portal.azure.com). 

### <a name="enable-a-retention-policy"></a>Habilitación de una directiva de retención

1. Navegue a la instancia de Azure Container Registry. En **Directivas**, seleccione **Retención** (versión preliminar).
1. En **Estado**, seleccione **Habilitado**.
1. Seleccione un número de días entre 0 y 365 para conservar los manifiestos sin etiqueta. Seleccione **Guardar**.

![Habilitación de una directiva de retención en Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Deshabilitación de una directiva de retención

1. Navegue a la instancia de Azure Container Registry. En **Directivas**, seleccione **Retención** (versión preliminar).
1. En **Estado**, seleccione **Deshabilitado**. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre opciones de [eliminación de imágenes y repositorios](container-registry-delete.md) en Azure Container Registry

* Más información sobre cómo [purgar automáticamente ](container-registry-auto-purge.md) imágenes y manifiestos seleccionados de un registro

* Más información sobre opciones de [bloqueo de imágenes y manifiestos](container-registry-image-lock.md) en un registro

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
