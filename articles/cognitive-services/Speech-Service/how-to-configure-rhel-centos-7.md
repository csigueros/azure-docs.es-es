---
title: 'Configuración de RHEL/CentOS 7: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo configurar RHEL/CentOS 7 para que se pueda usar el SDK de voz.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: 8c7b9a6f8bb74bd8c66eac976bf9d17a3fd798d5
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132155972"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Configuración de RHEL/CentOS 7 para el SDK de voz

Para usar el SDK de Voz para el desarrollo en C++ en Red Hat Enterprise Linux (RHEL) 8 x64 y CentOS 8 x64, actualice el compilador de C++ y la biblioteca en tiempo de ejecución de C++ compartida en el sistema.

### <a name="1-general-setup"></a>1. Configuración general

En primer lugar, instale todas las dependencias generales:

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. Bibliotecas de runtime y el compilador de C/C++

Instale los paquetes de requisitos previos con este comando:

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

A continuación, actualice las bibliotecas de runtime y el compilador:

```bash
# Build GCC 7.5.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-7.5.0/gcc-7.5.0.tar.bz2 -O
tar jxf gcc-7.5.0.tar.bz2
mkdir gcc-7.5.0-build && cd gcc-7.5.0-build
../gcc-7.5.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Si el compilador y las bibliotecas actualizados deben implementarse en varias máquinas, simplemente puede copiarlos desde `/usr/local` a otras máquinas. Si solo se necesitan las bibliotecas de runtime, los archivos de `/usr/local/lib64` serán suficientes.

### <a name="3-environment-settings"></a>3. Configuración del entorno

Ejecute los siguientes comandos para completar la configuración:

```bash
# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-<version>/lib/x64:$LD_LIBRARY_PATH

```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Acerca del SDK de Voz](speech-sdk.md)
