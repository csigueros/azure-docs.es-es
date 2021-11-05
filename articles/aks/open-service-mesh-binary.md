---
title: Descarga de la biblioteca cliente de OSM
description: Descarga y configuración de la biblioteca cliente de Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2524dca92126ac0fdd6c0ace502cc16d78b6d480
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066850"
---
# <a name="download-and-configure-the-open-service-mesh-osm-client-library"></a>Descarga y configuración de la biblioteca cliente de Open Service Mesh (OSM)

En este artículo se explica cómo descargar la biblioteca cliente de OSM que se usará para operar y configurar el complemento de OSM para AKS, además de cómo configurar el binario para un entorno.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> No intente instalar OSM desde el archivo binario mediante `osm install`. Esto producirá una instalación de OSM que no está integrada como un complemento para AKS.

## <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>Configuración de variables de la CLI de OSM con un archivo OSM_CONFIG

Los usuarios pueden invalidar la configuración predeterminada de la CLI de OSM para mejorar la experiencia del complemento. Esto se puede hacer mediante la creación de un archivo de configuración, similar a `kubeconfig`. El archivo de configuración se puede crear en `$HOME/.osm/config.yaml` o en una ruta de acceso diferente que se exporta mediante la variable de entorno `OSM_CONFIG`.

El archivo debe contener el siguiente contenido con formato YAML:

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

Si el archivo no se crea en `$HOME/.osm/config.yaml`, no olvide establecer la variable de entorno `OSM_CONFIG` para que apunte a la ruta de acceso donde se crea el archivo de configuración.

Después de establecer OSM_CONFIG, la salida del comando `osm env` debe ser la siguiente:

```console
$ osm env
---
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```
