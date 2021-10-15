---
title: Personalización de la experiencia de la CLI de OSM
description: Personalice la experiencia de la CLI de OSM con un archivo osmconfig.
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1e5b39424b8e162feda65783eecc3a1a95fdaa7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535494"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>Configuración de variables de la CLI de OSM con un archivo OSM_CONFIG

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