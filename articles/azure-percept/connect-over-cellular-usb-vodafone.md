---
title: Conexión de Azure Percept DK mediante 5G y LTE con un módem USB de Vodafone
description: En este artículo se explica cómo conectar Azure Percept DK mediante redes 5G y LTE con un módem USB de Vodafone.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 226fc6890253bd4701b400dcd42c420618701630
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252371"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-by-using-a-vodafone-usb-connect-4g-v2-modem"></a>Conexión de Azure Percept DK mediante 5G y LTE con un módem USB Connect 4G v2 de Vodafone

En este artículo se describe cómo conectar Azure Percept DK con un módem USB Connect 4G v2 de Vodafone.

Para más información sobre este módem, vaya a la página de [terminales integrados de Vodafone](https://www.vodafone.com/business/iot/iot-devices/integrated-terminals).

## <a name="use-the-modem-to-connect"></a>Uso del módem para conectarse

Antes de empezar, asegúrese de que preparó Azure Percept DK para [conectarse mediante un módem USB](./connect-over-cellular-usb.md). No se necesita preparación para el módem USB mismo.   

1. Conecte una tarjeta SIM al módem de Vodafone.

1. Conecte el módem de Vodafone al puerto USB A de Azure Percept.

1. Encienda Azure Percept DK.

1. Conéctese a Azure Percept DK mediante el protocolo de red Secure Shell (SSH).

1. Asegúrese de que ModemManager esté en ejecución. Para ello, escriba el comando siguiente en el símbolo del sistema SSH:

    ```
    systemctl status ModemManager
    ```

    Si el proceso se completa correctamente, recibirá un resultado similar al siguiente:

    ```
    ModemManager.service - Modem Manager
    Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)
    Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago
    ```

1. Enumere los módems activos.

    Para comprobar que ModemManager puede reconocer el módem, ejecute:

    ```
    mmcli --list-modems
    ```

    Debe obtener un resultado similar al siguiente. Aquí, el id. del módem es `0`, pero el resultado puede diferir.

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband
    ```

1. Obtenga los detalles del módem.

    Para obtener los detalles de estado del módem, ejecute este comando (donde el id. del módem es `0`).

    ```
    mmcli --modem 0
    ```

    De manera predeterminada, el módem está deshabilitado (`Status -> state: disabled`).

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
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

    Se recomienda empezar con la configuración predeterminada: 
    
    `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`. 
    
    Si todavía no utiliza esta configuración, ejecute:
    
     `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

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

    El proveedor de telefonía móvil proporciona un APN, como este para Vodafone:

    ```
    mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
    ```

    Debería recibir una respuesta que indique que conectó correctamente el módem.

1. Obtenga el estado del módem.

    Ahora debería ver un estado de `Status -> state: connected` y una categoría `Bearer` nueva al final del mensaje de estado.

    ```
    mmcli --modem 0
    ```

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0-mobile.
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Numbers  |                  own: xxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (10)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 19% (recent)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 302220
              |        operator name: TELUS
              |         registration: roaming
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
      --------------------------------
      General            |       path: /org/freedesktop/ModemManager1/Bearer/0
                        |       type: default
      --------------------------------
      Status             |  connected: yes
                        |  suspended: no
                        |  interface: wwan0
                        | ip timeout: 20
      --------------------------------
      Properties         |        apn: internet4gd.gdsp
                        |    roaming: allowed
      --------------------------------
      IPv4 configuration |     method: static
                        |    address: 162.177.2.0
                        |     prefix: 22
                        |    gateway: 162.177.2.1
                        |        dns: 10.177.0.34, 10.177.0.210
                        |        mtu: 1500
      --------------------------------
      Statistics         |   attempts: 1
    ```

1. Acceda a la interfaz de red.

    ```
    sudo ip link set dev wwan0 up
    ```

1. Configure la interfaz de red.

    Con la información proporcionada por el portador, reemplace la dirección IP (por ejemplo, aquí usamos 162.177.2.0/22) por la que tiene el portador:

    ```
    sudo ip address add 162.177.2.0/22 dev wwan0
    ```

1. Compruebe la información de IP.

    La configuración de IP de esta interfaz debe coincidir con los detalles del portador de ModemManager. Ejecute:

    ```
    sudo ip address show dev wwan0
    ```

    La dirección IP del portador aparece tal como se muestra aquí:

    ```
    wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
        inet 162.177.2.0/22 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::c012:44ff:fec4:273c/64 scope link 
          valid_lft forever preferred_lft forever
    ```

1. Establezca la ruta predeterminada.

    Como ya indicamos, con la información que proporciona el portador y la puerta de enlace (reemplace 162.177.2.1) del módem como el destino predeterminado para los paquetes de red, ejecute:

    ```
    sudo ip route add default via 162.177.2.1 dev wwan0
    ```

    Azure Percept DK ahora está habilitado para conectarse a Azure mediante el módem LTE.


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
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>Reglas de módem de Vodafone para mitigar problemas de enumeración

Si desea evitar que el módem se enumere en un modo no compatible, se recomienda aplicar las reglas de userspace/dev (udev) siguientes para que ModemManager omita las interfaces no deseadas.

Cree un archivo */usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules* con el contenido siguiente:

```
ACTION!="add|change|move|bind", GOTO="mm_vodafone_port_types_end"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1bbb", GOTO="mm_vodafone_generic_vendorcheck"
GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_generic_vendorcheck"
SUBSYSTEMS=="usb", ATTRS{bInterfaceNumber}=="?*", ENV{.MM_USBIFNUM}="$attr{bInterfaceNumber}"

# Interface 1 is QDCM (ignored) and interfaces 3 and 4 are MBIM Control and Data.
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="00", ENV{ID_MM_PORT_TYPE_AT_PRIMARY}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="01", ENV{ID_MM_PORT_IGNORE}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="02", ENV{ID_MM_PORT_AT_SECONDARY}="1"

GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_port_types_end"
```

Una vez que realice la instalación, vuelva a cargar las reglas udev y reinicie ModemManager:

```
udevadm control -R
systemctl restart ModemManager
```

## <a name="next-steps"></a>Pasos siguientes

En función del dispositivo de telefonía móvil al que tenga acceso, puede conectarse de una de estas dos maneras:

* [Conexión mediante un módem USB](./connect-over-cellular-usb.md)
* [Conexión mediante 5G o LTE](./connect-over-cellular.md)
