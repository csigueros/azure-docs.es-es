---
title: Prueba de la latencia de red de máquinas virtuales de Azure en una red virtual de Azure | Microsoft Docs
description: Obtenga información sobre cómo probar la latencia de red entre máquinas virtuales de Azure en una red virtual
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: bd316ce7a868f639cdfd80293d5d6189e2942f6f
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988182"
---
# <a name="test-vm-network-latency"></a>Comprobación de la latencia de red de VM

Para que los resultados que se logren sean lo más precisos posible, mida la latencia de la red de las máquinas virtuales (VM) de Azure con una herramienta diseñada para tal fin. Algunas herramientas disponibles públicamente, como SockPerf (para Linux) y latte.exe (para Windows) permiten aislar y medir la latencia de red, a la vez que excluyen otros tipos de latencia, como la de las aplicaciones. Estas herramientas se centran en el tipo de tráfico de red que afecta al rendimiento de la aplicación (es decir, el tráfico TCP [protocolo de control de transmisión] y UDP [protocolo de datagramas de usuario]). 

Otras herramientas de conectividad comunes, como Ping, pueden medir la latencia, pero es posible que sus resultados no representen el tráfico de red que se usa en las cargas de trabajo reales. Eso se debe a que la mayoría de estas herramientas emplean el protocolo de mensajes de control de Internet (ICMP), que puede tratarse de forma diferente al tráfico de la aplicación y cuyos resultados es posible que no se apliquen a las cargas de trabajo en las que se usa TCP y UDP. 

Para realizar pruebas precisas de la latencia de red de los protocolos usados por la mayoría de las aplicaciones, SockPerf (para Linux) y latte.exe (para Windows) generan los resultados más pertinentes. En este artículo se describen ambas herramientas.

## <a name="overview"></a>Información general

El uso de dos máquinas virtuales, una como remitente y otra como receptor, permite crear un canal de comunicaciones bidireccional. Con este enfoque, puede enviar y recibir paquetes en ambas direcciones y medir el tiempo de ida y vuelta (RTT).

También puede usarlo para medir la latencia de red entre dos máquinas virtuales, o incluso entre dos equipos físicos. Las medidas de latencia pueden ser útiles para los escenarios siguientes:

- Establecer una prueba comparativa de la latencia de red entre las máquinas virtuales implementadas.
- Comparar los efectos de los cambios en la latencia de red después de que se realicen cambios relacionados en:
  - El sistema operativo o el software de la pila de red, lo que incluye los cambios de configuración.
  - Un método de implementación de la máquina virtual, como la implementación en una zona de disponibilidad o en un grupo de selección de ubicación de proximidad (PPG).
  - Las propiedades de la máquina virtual, como Redes aceleradas o cambios de tamaño.
  - Una red virtual, como el enrutamiento o el filtrado de cambios.

### <a name="tools-for-testing"></a>Herramientas para pruebas
Para medir la latencia, hay dos herramientas diferentes:

* Para sistemas con Windows: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Para sistemas con Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Con estas herramientas se asegura de que solo se miden los tiempos de entrega de la carga TCP o UDP, pero no ICMP (ping) u otros tipos de paquetes que no usen las aplicaciones y que no afecten a su rendimiento.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Sugerencias para crear una configuración óptima de las máquinas virtuales

Al crear la configuración de una máquina virtual, tenga en cuenta las siguientes recomendaciones:
- Use la versión más reciente de Windows o Linux.
- Habilite Redes aceleradas para obtener los mejores resultados.
- Implemente las máquinas virtuales con un [grupo de selección de ubicación de proximidad de Azure](../virtual-machines/co-location.md).
- Las máquinas virtuales grandes generalmente funcionan mejor que las pequeñas.

### <a name="tips-for-analysis"></a>Sugerencias para el análisis

Cuando analice los resultados de las pruebas, tenga en cuenta las siguientes recomendaciones:

- Establezca una línea de base rápidamente, en cuanto se completen la implementación, la configuración y las optimizaciones.
- Compare siempre los nuevos resultados con una línea de base o los resultados de una prueba con los de otra con cambios controlados.
- Repita las pruebas cada vez que se observen o se planeen cambios.


## <a name="test-vms-that-are-running-windows"></a>Realización de pruebas en máquinas virtuales que usan Windows

### <a name="get-latteexe-onto-the-vms"></a>Instalación de latte.exe en las máquinas virtuales

Descargue la [versión más reciente de latte.exe](https://github.com/microsoft/latte/releases/download/v0/latte.exe).

Considere la posibilidad de colocar latte.exe en una carpeta independiente, como *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Permitir que latte.exe pase el Firewall de Windows Defender

En el *receptor*, cree una regla de permiso en el Firewall de Windows Defender para permitir que llegue el tráfico de latte.exe. Es más fácil permitir todo el programa latte.exe por su nombre que determinados puertos TCP de entrada.

Deje que latte.exe atraviese el Firewall de Windows Defender, para lo que debe ejecutar el siguiente comando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Por ejemplo, si ha copiado latte.exe en la carpeta *c:\tools*, este sería el comando:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Ejecución de pruebas de latencia

* En el *receptor*, inicie latte.exe (ejecútelo desde la ventana del símbolo del sistema, no desde PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Para obtener unos resultados representativos, es suficiente la realización de unas 65 000 iteraciones.

    Cualquier número de puerto disponible es válido.

    Si la máquina virtual tiene la dirección IP 10.0.0.4, el comando sería similar a este:

    `latte -a 10.0.0.4:5005 -i 65100`

* En el *emisor*, inicie latte.exe (ejecútelo desde la ventana del símbolo del sistema, no desde PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    El comando resultante es el mismo que en el receptor, salvo que se agrega&nbsp; *-c* para indicar que se trata del *cliente* o *emisor*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Espere a que se muestren los resultados. En función de la distancia que haya entre las máquinas virtuales, la prueba podría tardar unos minutos en finalizar. Considere comenzar con menos iteraciones para probar si se ha realizado correctamente antes de ejecutar pruebas más largas.

## <a name="test-vms-that-are-running-linux"></a>Realización de pruebas en máquinas virtuales que usan Linux

Para realizar pruebas en máquinas virtuales en las que se ejecutan Linux, use [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalación de SockPerf en las máquinas virtuales

En las máquinas virtuales Linux, tanto el *emisor* como el *receptor* ejecutan los siguientes comandos para preparar SockPerf en las máquinas virtuales. Se proporcionan comandos para las principales distribuciones.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Para Red Hat Enterprise Linux (RHEL)/CentOS

Ejecute los comandos siguientes:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Para Ubuntu

Ejecute los comandos siguientes:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Para todas las distribuciones

Copie, compile e instale SockPerf siguiendo estos pasos:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Ejecución de SockPerf en las máquinas virtuales

Después de que se complete la instalación de SockPerf, las máquinas virtuales están listas para ejecutar las pruebas de latencia. 

En primer lugar, inicie SockPerf en el *receptor*.

Cualquier número de puerto disponible es válido. En este ejemplo, se usa el puerto 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Ahora que el servidor está a la escucha, el cliente puede empezar a enviarle paquetes al puerto en el que realiza la escucha (en este caso 12345).

Bastan unos 100 segundos para que se devuelvan resultados representativos, como se muestra en el ejemplo siguiente:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Espere a que se muestren los resultados. En función de la distancia entre las máquinas virtuales, el número de iteraciones variará. Para comprobar que las pruebas se realizan de forma correcta, antes de ejecutar pruebas largas considere la posibilidad comenzar con pruebas cortas, de unos 5 segundos.

En este ejemplo de SockPerf se usa un tamaño de mensaje de 350 bytes, el cual es un paquete medio típico. Puede aumentar o reducir el tamaño para lograr resultados que representen con mayor precisión la carga de trabajo que se ejecuta en las máquinas virtuales.


## <a name="next-steps"></a>Pasos siguientes
* Mejore la latencia con un [grupo de selección de ubicación de proximidad de Azure](../virtual-machines/co-location.md).
* Aprenda a [optimizar las redes para las máquinas virtuales](../virtual-network/virtual-network-optimize-network-bandwidth.md) en su escenario.
* Obtenga información acerca de la [asignación del ancho de banda a las máquinas virtuales](../virtual-network/virtual-machine-network-throughput.md).
* Para más información, consulte [Preguntas más frecuentes acerca de Azure Virtual Network](../virtual-network/virtual-networks-faq.md).
