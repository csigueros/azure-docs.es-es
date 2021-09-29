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
ms.openlocfilehash: 69c4c86816db612d04f474638369834bbe5163b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619768"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Configuración de RHEL/CentOS 7 para el SDK de voz

Red Hat Enterprise Linux (RHEL) 8 para x64 y CentOS 8 para x64 se admiten oficialmente en la versión 1.10.0 y posteriores del SDK de voz. También se puede usar el SDK de voz en RHEL/CentOS 7 para x64, pero requiere actualizar el compilador C++ (para desarrollo de C++) y la biblioteca en tiempo de ejecución de C++ compartida del sistema.

Para comprobar la versión del compilador de C++, ejecute lo siguiente:

```bash
g++ --version
```

Si el compilador está instalado, la salida debe ser similar a la siguiente:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Este mensaje le permite saber que la versión principal 4 de GCC está instalada. Esta versión no es totalmente compatible con C++ 11 estándar, que usa el SDK de voz. Si intenta compilar un programa de C++ con esta versión de GCC y los encabezados del SDK de voz, se producirá un error de compilación.

También es importante comprobar la versión de la biblioteca en tiempo de ejecución de C++ compartida (libstdc++). La mayor parte del SDK de voz se implementa como bibliotecas nativas de C++, lo que significa que depende de libstdc++ independientemente del lenguaje que se utilice para desarrollar aplicaciones.

Para buscar la ubicación de libstdc++ en el sistema, ejecute:

```bash
ldconfig -p | grep libstdc++
```

La salida de un RHEL/CentOS 7 (x64) básica es:

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

En función de este mensaje, querrá comprobar las definiciones de versión con este comando:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

La salida debe ser:

```bash
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

El SDK de voz requiere **CXXABI_1.3.9** y **GLIBCXX_3.4.21**. Puede encontrar esta información con la ejecución de `ldd libMicrosoft.CognitiveServices.Speech.core.so` en las bibliotecas del SDK de Voz desde el paquete de Linux.

> [!NOTE]
> Se recomienda que la versión de GCC instalada en el sistema sea al menos **5.4.0**, con bibliotecas en tiempo de ejecución coincidentes.

## <a name="example"></a>Ejemplo

Este es un comando de ejemplo que establece cómo configurar RHEL/CentOS 7 x64 para el desarrollo (C++, C#, Java, Python) con el SDK de voz 1.10.0 o posterior:

### <a name="1-general-setup"></a>1. Configuración general

En primer lugar, instale todas las dependencias generales:

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. Bibliotecas de runtime y el compilador de C/C++

Instale los paquetes de requisitos previos con este comando:

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> El paquete libmpc-devel quedó en desuso en la actualización de RHEL 7.8. Si la salida del comando anterior incluye un mensaje
>
> ```bash
> No package libmpc-devel available.
> ```
>
> es necesario instalar los archivos necesarios desde los orígenes originales. Ejecute los comandos siguientes:
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

A continuación, actualice las bibliotecas de runtime y el compilador:

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Si el compilador y las bibliotecas actualizados deben implementarse en varias máquinas, simplemente puede copiarlos desde `/usr/local` a otras máquinas. Si solo se necesitan las bibliotecas de runtime, los archivos de `/usr/local/lib64` serán suficientes.

### <a name="3-environment-settings"></a>3. Configuración del entorno

Ejecute los siguientes comandos para completar la configuración:

```bash
# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

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
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Acerca del SDK de Voz](speech-sdk.md)
