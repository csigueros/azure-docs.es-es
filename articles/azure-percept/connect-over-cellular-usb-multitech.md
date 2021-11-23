---
title: Conexión de Azure Percept DK a través de LTE con un módem USB MultiConnect de MultiTech
description: En este artículo se explica cómo conectar Azure Percept DK mediante redes 5G o LTE con un módem USB MultiConnect de MultiTech.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5000c7be25a82069da0585a40252c91262a7ea12
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252447"
---
# <a name="connect-azure-percept-dk-over-lte-by-using-a-multitech-multiconnect-usb-modem"></a>Conexión de Azure Percept DK a través de LTE con un módem USB MultiConnect de MultiTech 

En este artículo se indica cómo conectar Azure Percept DK con un módem USB MultiConnect (MTCM-LNA3-B03) de MultiTech. 

> [!Note]
> El módem USB MultiConnect de MultiTech viene en una variedad de modelos. En este artículo, usamos el modelo LNA3, que funciona con tarjetas SIM de Verizon y Vodafone, entre otras. En este momento, no podemos conectarnos a una red AT&T, pero estamos investigando el problema y actualizaremos este artículo al encontrar la causa principal. Para más información sobre el módem USB MultiConnect de MultiTech, visite el sitio de [MultiTech](https://www.multitech.com/brands/multiconnect-microcell).

## <a name="prepare-to-connect-azure-percept-dk"></a>Preparación para conectar Azure Percept DK
Para información sobre cómo preparar Azure Percept DK, vaya a [Conexión de Azure Percept DK mediante redes 5G y LTE con un módem USB](./connect-over-cellular-usb.md). Asegúrese de tener en cuenta los comentarios sobre los cables USB que se deben usar. 

### <a name="prepare-the-modem"></a>Preparación del módem
Antes de empezar, el módem tiene que estar en modo Mobile Broadband Interface Model (MBIM). Para aprender a preparar el módem, consulte la [guía de referencia de comandos Attention (AT) de soluciones inalámbricas de Telit](
https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf).

Para habilitar la interfaz MBIM, en este artículo usamos el comando AT `AT#USBCFG=<mode>` para configurar el modo USB correcto.

En la guía de referencia de comandos AT se muestra todos los modos posibles, pero nos interesa el modo `3` para los fines de este artículo. El modo predeterminado es `0`.

La manera más sencilla de configurar el modo es conectar el módem de MultiTech a un equipo y utilizar software de terminal, como TeraTerm o PuTTY. Puede utilizar el administrador de dispositivos de Windows para ver qué puerto USB está asignado para el módem. Si hay varios puertos, es posible que tenga que probar para ver cuál responde a los comandos AT. La configuración del puerto COM debe ser la siguiente:
* **Velocidad en baudios**: 9600 (o 115 200)
* **Bits de parada**: 1
* **Paridad**: ninguna
* **Tamaño de bytes**: 8
* **Control de flujo**: sin control de flujo

Estos son los comandos AT:

Para comprobar qué dispositivo MultiTech en modo USB se ejecuta actualmente, utilice:

```
AT#USBCFG?
```

Para cambiar al modo 3, utilice:

```
AT#USBCFG=3
```

Si utiliza el primer comando AT para volver a comprobarlo, obtendrá: `#USBCFG: 3`

Una vez que establezca el modo USB correcto, debe emitir un restablecimiento con:

```
AT#REBOOT
```

En este momento, se debe desconectar el módem y volver a conectarlo al puerto USB posterior mediante el modo que se estableció con anterioridad.

## <a name="use-the-modem-to-connect"></a>Uso del módem para conectarse

Asegúrese de haber completado las preparaciones de Azure Percept DK que se describen en el artículo [Conexión mediante un módem USB](./connect-over-cellular-usb.md).   

1. Conecte una tarjeta SIM al módem de MultiTech.

1. Conecte el módem MultiTech al puerto USB A de Azure Percept DK.

1. Encienda Azure Percept DK.

1. Conéctese a Azure Percept DK mediante el protocolo de red Secure Shell (SSH).

1. Asegúrese de que ModemManager esté en ejecución. Para ello, escriba el comando siguiente en el símbolo del sistema SSH:

    ```
    systemctl status ModemManager
    ```
    Si el proceso se completa correctamente, recibirá un resultado similar al siguiente:

    *ModemManager.service - Modem Manager* *Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* *Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

1. Enumere los módems activos.

    Para comprobar que ModemManager puede reconocer el módem, ejecute:

    ```
    mmcli --list-modems
    ```

    Debe obtener un resultado similar al siguiente:

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160
    ```

1. Obtenga los detalles del módem.

    Aquí, el id. del módem es `0`, pero el resultado puede diferir. El id. del módem (`--modem 0`) se usa en los comandos de ModemManager como se indica a continuación:
    
    ```
    mmcli --modem 0
    ```
    
    De manera predeterminada, el módem está deshabilitado (`Status -> state: disabled`).

    ```
    --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
      --------------------------------
      Hardware |         manufacturer: Telit
              |                model: FIH7160
              |    firmware revision: 20.00.525
              |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
              |              drivers: cdc_acm, cdc_mbim
              |               plugin: telit
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
              |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
              |                       wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 3g, 4g; preferred: none
              |              current: allowed: 3g, 4g; preferred: none
      --------------------------------
      Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
              |                       eutran-13, eutran-17
              |              current: utran-2, eutran-2
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
    ```

1. Habilite el módem.

    Antes de establecer una conexión, habilite las radios del módem. Para ello, ejecute este código:

    ```
    mmcli --modem 0 --enable
    ```

    Debería recibir una respuesta que indique que habilitó correctamente el módem.

    Después de un tiempo, el módem se debería registrar en una torre de telefonía móvil, y se debería ver que el estado del módem es `Status -> state: registered` después de ejecutar este código:

    ```
    mmcli --modem 0
    ```

1. Para conectarse, use la información del nombre del punto de acceso (APN).

    El proveedor de telefonía móvil proporciona un APN, como este para Verizon:

    ```
    mmcli --modem 0 --simple-connect="apn=vzwinternet"  
    ```

    Debería recibir una respuesta que indique que habilitó correctamente el módem.

1. Obtenga el estado del módem.

    Ahora debería ver un estado de `Status -> state: connected` y una categoría `Bearer` nueva al final del mensaje de estado.

    ```
    mmcli --modem 0
    ```

    ```
    --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
      --------------------------------
      Hardware |         manufacturer: Telit
              |                model: FIH7160
              |    firmware revision: 20.00.525
              |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
              |              drivers: cdc_acm, cdc_mbim
              |               plugin: telit
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
              |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
              |                       wwan0 (net)
      --------------------------------
      Numbers  |                  own: +1xxxxxxxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 16% (recent)
      --------------------------------
      Modes    |            supported: allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 3g, 4g; preferred: none
              |              current: allowed: 3g, 4g; preferred: none
      --------------------------------
      Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
              |                       eutran-13, eutran-17
              |              current: utran-2, eutran-2
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 311480
              |        operator name: Verizon
              |         registration: home
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
      --------------------------------
      Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
    ```

1. Obtenga los detalles del portador.

    Necesita estos detalles para conectar el sistema operativo a la conexión de datos de paquetes que el módem tiene establecida con la red de telefonía móvil. En este momento, el módem tiene una conexión IP, pero el sistema operativo todavía no está configurado para usarla.
  
    ```
    mmcli --bearer 0
    ```

    Los detalles del portador se muestran en el código siguiente:

    ```
    ------------------------------------
      General            |           path: /org/freedesktop/ModemManager1/Bearer/0
                        |           type: default
      ------------------------------------
      Status             |      connected: yes
                        |      suspended: no
                        |      interface: wwan0
                        |     ip timeout: 20
      ------------------------------------
      Properties         |            apn: vzwinternet
                        |        roaming: allowed
      ------------------------------------
      IPv4 configuration |         method: static
                        |        address: 100.112.107.46
                        |         prefix: 24
                        |        gateway: 100.112.107.1
                        |            dns: 198.224.166.135, 198.224.167.135
      ------------------------------------
      Statistics         |       duration: 119
                        |       attempts: 1
                        | total-duration: 119
    ```

1. Acceda a la interfaz de red.

    ```
    sudo ip link set dev wwan0 up
    ```

1. Configure la interfaz de red.

    Con la información proporcionada por el portador, reemplace la dirección IP (por ejemplo, aquí usamos 100.112.107.46/24) por la que tiene el portador:

    ```
    sudo ip address add 100.112.107.46/24 dev wwan0
    ```

1. Compruebe la información de la dirección IP.

    La configuración de IP de esta interfaz debe coincidir con los detalles del portador de ModemManager. Ejecute:

    ```
    sudo ip address show dev wwan0
    ```

    La dirección IP del portador aparece tal como se muestra aquí:

    ```
    6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
        inet 100.112.107.46/24 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
          valid_lft forever preferred_lft forever
    ```

1. Establezca la ruta predeterminada.

    Como ya indicamos, con la información que proporciona el portador y la puerta de enlace (100.112.107.1) del módem como el destino predeterminado para los paquetes de red, ejecute:

    ```
    sudo ip route add default via 100.112.107.1 dev wwan0
    ```

    Azure Percept DK ya está conectado con el módem USB.

1. Pruebe la conectividad.

    En este artículo, ejecutará una solicitud `ping` a través de la interfaz `wwan0`. También puede usar Azure Percept Studio y comprobar si llegan mensajes de telemetría. Asegúrese de no usar un cable Ethernet y de que Wi-Fi no esté habilitado a fin de que se utilice LTE. Ejecute:

    ```
    ping -I wwan0 8.8.8.8
    ```

    Debe obtener un resultado similar al siguiente:

    ```
    PING 8.8.8.8 (8.8.8.8) from 162.177.2.0 wwan0: 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=111 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=92.0 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=88.8 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 4ms
    rtt min/avg/max/mdev = 88.779/97.254/110.964/9.787 ms
    ```


## <a name="debugging"></a>Depuración

Para información general sobre la depuración, consulte [Conexión mediante un módem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Pasos siguientes

En función del dispositivo de telefonía móvil al que tenga acceso, puede conectarse de una de estas dos maneras:

* [Conexión mediante un módem USB](./connect-over-cellular-usb.md)
* [Conexión mediante 5G o LTE](./connect-over-cellular.md)
