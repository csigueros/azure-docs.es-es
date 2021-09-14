---
title: Deshabilitación de OSM
description: Deshabilitación de Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b2ad12db78451e2313494ccf9df6613443e9652b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440282"
---
# <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Deshabilitación del complemento Open Service Mesh (OSM) para el clúster de AKS

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Para deshabilitar el complemento de OSM, ejecute el comando siguiente:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```