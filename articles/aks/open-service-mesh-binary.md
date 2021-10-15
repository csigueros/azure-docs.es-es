---
title: Descarga de la biblioteca cliente de OSM
description: Descarga de la biblioteca cliente de Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 83eec8682291a74bc9b23bf4ff1326cee7134e7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534949"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>Descarga de la biblioteca cliente de Open Service Mesh (OSM)
En este artículo se explica cómo descargar la biblioteca cliente de OSM que se va a usar para operar y configurar el complemento OSM para AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

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

> [!NOTE]
> Se recomienda configurar la CLI de OSM para [personalizar la experiencia del complemento de AKS de OSM](/open-service-mesh-customize-addon-experience.md) después de instalar el binario, antes de usar la CLI de OSM. 