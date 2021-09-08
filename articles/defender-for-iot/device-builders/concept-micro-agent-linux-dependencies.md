---
title: Dependencias de Linux de microagentes (versión preliminar)
description: En este artículo se describen las distintas dependencias del sistema operativo Linux para el microagente de Defender para IoT.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: e878052e534ce7740fff1fdd462d2c95fcb11609
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481764"
---
# <a name="micro-agent-linux-dependencies-preview"></a>Dependencias de Linux de microagentes (versión preliminar)

En este artículo se describen las distintas dependencias del sistema operativo Linux para el microagente de Defender para IoT. 

## <a name="linux-dependencies"></a>Dependencias de Linux

En la tabla siguiente se muestran las dependencias de Linux para cada componente. 

| Componente | Dependencia | Tipo | Requerido por el SDK de IoT | Notas |
|--|--|--|--|--|
| **Principal** |  |  |  |  |
|  | libcurl-openssl (libcurl) | Biblioteca | ✔ |  |
|  | libssl | Biblioteca | ✔ |  |
|  | uuid | Biblioteca | ✔ |  |
|  | pthread | Marca de compilación ulibc | ✔ |  |
|  | libuv1 | Biblioteca |  |  |
|  | sudo | Paquete |  |  |
|  | uuid-runtime | Paquete |  |  |
| **Recopilador de información del sistema** |  |  |  |  |
|  | uname | Llamada del sistema |  |  |
| **Recopilador de línea base** |  |  |  |  |
|  | BusyBox | Marca de compilación de Linux |  |  |
|  | Bash | Marca de compilación de Linux |  |  |
| **Recopilador de procesos** |  |  |  |  |
|  | CONFIG_CONNECTOR=y | Configuración del kernel |  |  |
|  | CONFIG_PROC_EVENTS=y | Configuración del kernel |  |  |
| **Recopilador de redes** |  |  |  |  |
|  | libpcap | Biblioteca |  |  |
|  | CONFIG_PACKET=y | Configuración del kernel |  |  |
|  | CONFIG_NETFILTER =y | Configuración del kernel |  | Opcional: mejora del rendimiento |

## <a name="next-steps"></a>Pasos siguientes

[Instalación del microagente de Defender para IoT (versión preliminar)](quickstart-standalone-agent-binary-installation.md)