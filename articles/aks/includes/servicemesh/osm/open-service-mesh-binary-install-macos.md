---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: e72c6536ab15246466e16e439997b6deb6547604
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440102"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>Descarga e instalación del binario de cliente de Open Service Mesh (OSM)

En un shell basado en Bash, utilice `curl` para descargar la versión de OSM y, a continuación, realice la extracción con `tar`, tal como se indica a continuación:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.9.1

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

El binario de cliente `osm` se ejecuta en la máquina cliente y permite administrar el controlador de OSM en el clúster de AKS. Use los siguientes comandos para instalar el binario de cliente `osm` de OSM en un shell basado en Bash en macOS. Estos comandos copian el binario de cliente `osm` en la ubicación del programa de usuario estándar en su `PATH`.

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Puede comprobar que la biblioteca cliente `osm` se ha agregado correctamente a la ruta de acceso y su número de versión con el siguiente comando.

```
osm version
```
