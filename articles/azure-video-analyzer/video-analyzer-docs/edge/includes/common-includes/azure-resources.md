---
author: naiteeks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 04/20/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: f3fe037ab3b3f976536f7f0563cfc16bdfa5ce71
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030742"
---
El proceso de implementación tardará unos **20 minutos**. Al finalizar, tendrá determinados recursos de Azure implementados en la suscripción de Azure, entre los que se incluyen:

1. **Cuenta de Video Analyzer**: este [servicio en la nube](../../../overview.md) se usa para registrar el módulo perimetral Video Analyzer y para reproducir vídeo grabado y análisis de vídeo.
1. **Cuenta de almacenamiento**: para almacenar vídeo grabado y análisis de vídeo.
1. **Identidad administrada**: es la [identidad administrada](../../../../../active-directory/managed-identities-azure-resources/overview.md) asignada por el usuario que se usa para administrar el acceso a la cuenta de almacenamiento anterior.
1. **Máquina virtual**: esta es una máquina virtual que actuará como su dispositivo perimetral simulado.
1. **IoT Hub**: funciona como un centro de mensajes común para la comunicación bidireccional entre la aplicación de IoT, los módulos de IoT Edge y los dispositivos que administra.

Además de los recursos mencionados anteriormente, también se crean los siguientes elementos en el recurso compartido de archivos "deployment-output" de la cuenta de almacenamiento, para su uso en los inicios rápidos y tutoriales:

- **_appsettings.json_**: este archivo contiene la **cadena de conexión del dispositivo** y otras propiedades necesarias para ejecutar la aplicación de ejemplo en Visual Studio Code.
- **_env.txt_**: este archivo contiene las variables de entorno que necesita para generar manifiestos de implementación mediante Visual Studio Code.
- **_deployment.json_**: este es el manifiesto de implementación que usa la plantilla para implementar módulos perimetrales en el dispositivo perimetral simulado.

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

> [!TIP]
> Si tiene problemas para crear todos los recursos de Azure necesarios, siga los pasos manuales de este [inicio rápido](../../get-started-detect-motion-emit-events-portal.md).
