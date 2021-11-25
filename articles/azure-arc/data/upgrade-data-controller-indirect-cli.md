---
title: Actualización de un controlador de datos de Azure Arc de modo indirecto mediante la CLI
description: Actualización de un controlador de datos de Azure Arc de modo indirecto mediante la CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5c1afa620fb3b81666aada950c257f38ff5d9a4f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707721"
---
# <a name="upgrade-indirect-mode-azure-arc-data-controller-using-the-cli"></a>Actualización de un controlador de datos de Azure Arc de modo indirecto mediante la CLI

En este artículo se explica cómo actualizar un controlador de datos de Azure Arc conectado de forma indirecta mediante la CLI de Azure (`az`).

> [!IMPORTANT]
> Este artículo no se aplica a los controladores de datos habilitados para Azure Arc conectados de forma directa. Para obtener la información más reciente sobre cómo actualizar un controlador de datos conectado de forma directa, vea las [notas de la versión](./release-notes.md#data-controller-upgrade).

## <a name="prerequisites"></a>Requisitos previos

Va a necesitar un controlador de datos de modo indirecto con imageTag v1.0.0_2021-07-30 o posterior.

Para comprobar la versión, ejecute:

```console
kubectl get datacontrollers -n -o custom-columns=BUILD:.spec.docker.imageTag
```

## <a name="install-tools"></a>Instalación de herramientas

Para poder continuar con las tareas de este artículo, debe instalar:

- La [CLI de Azure (az)](/cli/azure/install-azure-cli)
- La [extensión de `arcdata` para la CLI de Azure](install-arcdata-extension.md)

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

## <a name="view-available-images-and-chose-a-version"></a>Visualización de imágenes disponibles y elección de una versión

Extraiga la lista de imágenes disponibles del controlador de datos con el siguiente comando:

   ```azurecli
   az arcdata dc list-upgrades --k8s-namespace <namespace> –-use-k8s
   ```

El comando anterior devuelve una salida como la del ejemplo siguiente:

```output
Found 2 valid versions.  The current datacontroller version is v1.0.0_2021-07-30.
v1.1.0_2021-11-02
v1.0.0_2021-07-30
```

## <a name="upgrade-data-controller"></a>Actualización del controlador de datos

En esta sección se muestra cómo actualizar un controlador de datos en modo indirecto.

> [!NOTE]
> Algunos de los modos y niveles de servicio de datos están disponibles con carácter general y otros están en versión preliminar.
> Si instala servicios de disponibilidad general y de versión preliminar en el mismo controlador de datos, no puede realizar una actualización local.
> Para actualizar, elimine todas las instancias de base de datos que no sean de disponibilidad general. Puede encontrar la lista de servicios disponibles con carácter general y en versión preliminar en las [notas de la versión](./release-notes.md).

### <a name="indirect-mode"></a>Modo indirecto

Tiene que conectarse a un clúster de Kubernetes y autenticarse en él, además de tener un contexto de Kubernetes existente seleccionado, antes de empezar a actualizar el controlador de datos de Azure Arc.

Primero puede hacer un simulacro. El simulacro comprueba que el registro existe, el esquema de versión y el token de autorización del repositorio privado (si se usa). Para hacer un simulacro, use el parámetro `--dry-run` en el comando `az arcdata dc upgrade`. Por ejemplo:

```azurecli
az arcdata dc upgrade --desired-version <version> --k8s-namespace <namespace> --dry-run --use-k8s
```

La salida del comando anterior es:

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
****Dry Run****
Arcdata Control Plane would be upgraded to: 20211024.1
```

Para actualizar el controlador de datos, ejecute el comando `az arcdata dc upgrade`. Si no especifica una imagen de destino, el controlador de datos se actualiza a la versión más reciente. En el ejemplo siguiente se emplea una variable local (`$version`) para usar la versión seleccionada anteriormente ([Visualización de imágenes disponibles y elección de una versión](#view-available-images-and-chose-a-version)).

```azurecli
az arcdata dc upgrade --desired-version $version --k8s-namespace <namespace> --use-k8s
```

La salida del comando anterior muestra el estado de los pasos:

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Creating service account: arc:cr-upgrade-worker
Creating cluster role: arc:cr-upgrade-worker
Creating cluster role binding: arc:crb-upgrade-worker
Cluster role binding: arc:crb-upgrade-worker created successfully.
Cluster role: arc:cr-upgrade-worker created successfully.
Service account arc:cr-upgrade-worker has been created successfully.
Creating privileged job arc-elevated-bootstrapper-job
```

## <a name="monitor-the-upgrade-status"></a>Supervisión del estado de actualización

Puede supervisar el progreso de la actualización con kubectl o la CLI.

### <a name="kubectl"></a>kubectl

```console
kubectl get datacontrollers --namespace <namespace>
kubectl get monitors --namespace <namespace>
```

La actualización es un proceso de dos partes. En primer lugar se actualiza el controlador y luego la pila de supervisión. Durante la actualización, use ```kubectl get monitors -n <namespace> -w``` para ver el estado. El resultado será:

```output
NAME           STATUS     AGE
monitorstack   Updating   36m
monitorstack   Updating   36m
monitorstack   Updating   39m
monitorstack   Updating   39m
monitorstack   Updating   41m
monitorstack   Ready      41m
```

### <a name="cli"></a>CLI

```azurecli
 az arcdata dc status show --k8s-namespace <namespace> --use-k8s
```

La actualización es un proceso de dos partes. En primer lugar se actualiza el controlador y luego la pila de supervisión. Una vez completada la actualización, la salida es:

```output
Ready
```

## <a name="troubleshoot-upgrade-problems"></a>Solución de problemas de actualización

Si tiene problemas con la actualización, consulte la [guía de solución de problemas](troubleshoot-guide.md).