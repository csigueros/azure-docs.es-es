---
title: Dependencias de Linux de microagentes (versión preliminar)
description: En este artículo se describen las distintas dependencias del sistema operativo Linux para el microagente de Defender para IoT.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 102cf31dbe19c068de344f1f7f294a6f3f0a722b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967564"
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
| **Recopilador de inicios de sesión** |  |  |  |  |
|  | Wtmp, btmp | Archivos de registro |  | [utmp](https://en.wikipedia.org/wiki/Utmp) |

## <a name="next-steps"></a>Pasos siguientes

[Instalación del microagente de Defender para IoT (versión preliminar)](quickstart-standalone-agent-binary-installation.md)