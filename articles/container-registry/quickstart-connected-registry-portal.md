---
title: 'Inicio rápido: Creación de un registro conectado mediante el portal'
description: Use Azure Portal para crear un recurso de Azure Container Registry conectado que pueda sincronizar imágenes y otros artefactos con el registro en la nube.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6181f0fbdea8c03741f28dc9023a6761971cf2e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030506"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-portal"></a>Inicio rápido: Creación de un registro conectado mediante la CLI de Azure

En este inicio rápido, usará Azure Portal para crear un recurso de [registro conectado](intro-connected-registry.md) en Azure. La característica de registro conectado de Azure Container Registry le permite implementar un registro de forma remota o local y sincronizar imágenes y otros artefactos con el registro en la nube. 

Aquí se crean dos recursos de registro conectados para un registro en la nube: un registro conectado permite la funcionalidad de lectura y escritura (extracción e inserción de artefactos) y otro permite la funcionalidad de solo lectura. 

Después de crear un registro conectado, puede seguir otras guías para implementarlo y usarlo en la infraestructura local o remota.

## <a name="prerequisites"></a>Prerrequisitos

* Azure Container Registry: si aún no tiene un registro de contenedor, [cree uno](container-registry-get-started-portal.md) (se requiere el nivel Premium) en una [región](intro-connected-registry.md#available-regions) que admita registros conectados. 

Para importar imágenes en el registro de contenedor, use la CLI de Azure: [!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>Habilitación del punto de conexión de datos dedicado para el registro en la nube

Habilite el [punto de conexión de datos dedicado](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) para Azure Container Registry en la nube. Este paso es necesario para que un registro conectado se comunique con el registro en la nube.

1. En [Azure Portal](https://portal.azure.com), vaya al registro de contenedor.
1. Seleccione **Redes > Acceso público**.
Active la casilla **Habilitar punto de conexión de datos dedicado**.
1. Seleccione **Guardar**.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>Creación de un recurso de registro conectado para la funcionalidad de lectura y escritura

En los pasos siguientes se crea un registro conectado en [modo ReadWrite](intro-connected-registry.md#modes) que está vinculado al registro en la nube.

1. En [Azure Portal](https://portal.azure.com), vaya al registro de contenedor.
1. Seleccione **Registros conectados (versión preliminar) > + Crear**.
1. Escriba o seleccione los valores de la tabla siguiente y seleccione **Guardar**.


|Elemento  |Descripción  |
|---------|---------|
|Parent     | Seleccione **Sin elemento primario** para un registro conectado vinculado al registro en la nube.        |
|Mode     | Seleccione **ReadWrite**.         |
|Nombre     | El nombre del registro conectado debe empezar con una letra y contener solo caracteres alfanuméricos. Debe tener entre 5 y 40 caracteres y ser único en la jerarquía de este registro de contenedor de Azure.       |
|Propiedades de registro     | Acepte la configuración predeterminada.       |
|Propiedades de sincronización    | Acepte la configuración predeterminada. Dado que no hay ninguna programación de sincronización definida de manera predeterminada, los repositorios se sincronizarán entre el registro en la nube y el registro conectado sin interrupciones.      |
|Repositorios     | Seleccione o escriba los nombres de los repositorios que importó en el paso anterior. Los repositorios especificados se sincronizarán entre el registro en la nube y el registro conectado una vez implementado.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readwrite-connected-registry.png" alt-text="Creación de un registro conectado en modo ReadWrite":::


> [!IMPORTANT]
> Para admitir escenarios anidados en los que las capas inferiores no tienen acceso a Internet, siempre debe permitir la sincronización del repositorio `acr/connected-registry`. Este repositorio contiene la imagen del entorno de ejecución del registro conectado.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>Creación de un recurso de registro conectado para la funcionalidad de solo lectura

En los pasos siguientes se crea un registro conectado en [modo ReadOnly](intro-connected-registry.md#modes) cuyo elemento primario es el registro conectado que creó en la sección anterior. Este registro conectado habilita la funcionalidad de solo lectura (extracción de artefactos) una vez implementada.

1. En [Azure Portal](https://portal.azure.com), vaya al registro de contenedor.
1. Seleccione **Registros conectados (versión preliminar) > + Crear**.
1. Escriba o seleccione los valores de la tabla siguiente y seleccione **Guardar**.


|Elemento  |Descripción  |
|---------|---------|
|Parent     | Seleccione el registro conectado que creó anteriormente.        |
|Mode     | Seleccione **ReadOnly**.         |
|Nombre     | El nombre del registro conectado debe empezar con una letra y contener solo caracteres alfanuméricos. Debe tener entre 5 y 40 caracteres y ser único en la jerarquía de este registro de contenedor de Azure.      |
|Propiedades de registro     | Acepte la configuración predeterminada.       |
|Propiedades de sincronización    | Acepte la configuración predeterminada. Dado que no hay ninguna programación de sincronización definida de manera predeterminada, los repositorios se sincronizarán entre el registro en la nube y el registro conectado sin interrupciones.      |
|Repositorios     | Seleccione o escriba los nombres de los repositorios que importó en el paso anterior. Los repositorios especificados se sincronizarán entre el registro en la nube y el registro conectado una vez implementado.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readonly-connected-registry.png" alt-text="Creación de un registro conectado en modo ReadOnly":::

## <a name="view-connected-registry-properties"></a>Visualización de las propiedades del registro conectado

Seleccione un registro conectado en el portal para ver sus propiedades, como su estado de conexión (Sin conexión, En línea o Incorrecto) y si se ha activado (implementado en el entorno local). En el ejemplo siguiente, no se implementa el registro conectado. Su estado de conexión "Sin conexión" indica que actualmente está desconectado de la nube.

:::image type="content" source="media/quickstart-connected-registry-portal/connected-registry-properties.png" alt-text="Visualización de las propiedades del registro conectado":::

Desde esta vista, también puede generar una cadena de conexión y, opcionalmente, generar contraseñas para el [token de sincronización](overview-connected-registry-access.md#sync-token). Una cadena de conexión contiene los valores de configuración utilizados para implementar un registro conectado y sincronizar contenido con un registro primario.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, usará Azure Portal para crear dos recursos de registro conectados en Azure. Esos nuevos recursos de registro conectados están vinculados al registro en la nube y permiten la sincronización de artefactos con el registro en la nube.

Continúe con las guías de implementación del registro conectado para aprender a implementar y usar un registro conectado en la infraestructura de IoT Edge.

> [!div class="nextstepaction"]
> [Inicio rápido: Implementación del registro conectado en IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
