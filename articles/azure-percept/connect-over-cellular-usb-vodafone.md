---
title: Conexión de Azure Percept a través de 5G o LTE con un módem USB de Vodafone
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE mediante un módem USB de Vodafone.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 6264bd855cdc4ff186f51748bf426573e6316e5c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007072"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-vodafone-usb-connect-4g"></a>Conexión de Azure Percept a través de LTE con el módem USB de Vodafone USB Connect 4G
Estos son los pasos para conectar Azure Percept mediante el módem USB Connect 4G v2 de Vodafone.
Puede encontrar más información sobre este hardware de módem de Vodafone en particular en esta página: https://www.vodafone.com/business/iot/iot-devices/integrated-terminals.

## <a name="using-the-modem-to-connect"></a>Uso del módem para conectarse
Asegúrese de que ha realizado los preparativos de Azure Percept DK desde aquí: [Conexión mediante un módem USB](./connect-over-cellular-usb.md). No se necesita preparación para el propio módem USB.   

**1. Conecte una tarjeta SIM en el módem de Vodafone**.

**2. Conecte el módem de Vodafone al puerto USB A de Azure Percept**.

**3. Encienda Azure Percept**.

**4. Inicie sesión mediante SSH a Azure Percept DK**.

**5. Asegúrese de que ModemManager se está ejecutando**.

Escriba el siguiente comando en el símbolo del sistema de SSH:
```
systemctl status ModemManager
```
Si todo está bien, recibirá algo parecido a esto:

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)*
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. Enumere los módems activos**.

A continuación, vamos a comprobar que ModemManager puede reconocer el módem.
```
mmcli --list-modems
```
Aparecerá algo parecido a esto, donde el identificador del módem es `0`, aunque podría ser diferente:

 */org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband*

**7. Obtenga los detalles del módem**.

Para obtener los detalles del estado del módem, puede usar el siguiente comando e identificador de módem `0`.
```
mmcli --modem 0
```
De forma predeterminada, el módem está deshabilitado (`Status -> state: disabled`).
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
Se recomienda comenzar con la configuración predeterminada: `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`, que se puede establecer, si no se usa ya: `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

**8. Habilite el módem**.

Antes de establecer una conexión, es necesario activar las radios del módem.
```
mmcli --modem 0 --enable
```
Debería obtener una respuesta como esta: *se habilitó correctamente el módem*.

Después de un tiempo, el módem debe registrarse en una torre de celdas y debería ver el estado del módem: `Status -> state: registered`, si vuelve a ejecutar:
```
mmcli --modem 0
```

**9. Conéctese mediante la información de APN**.

El proveedor de telefonía móvil proporciona el nombre del punto de acceso APN, como este para Vodafone:
```
mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
```
y, si todo es correcto, aparecerá el mensaje indicando que se *ha conectado correctamente el módem*.

**10. Obtenga el estado del módem**.

Debería ver ahora `Status -> state: connected` y una nueva categoría `Bearer` al final del mensaje de estado.
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

**11. Obtenga los detalles del portador**.

Los detalles del portador son necesarios para conectar el sistema operativo a la conexión de datos de paquetes que el módem ha establecido ahora con la red de telefonía móvil. Por lo tanto, en este momento, el módem tiene conexión IP, pero el sistema operativo no está configurado aún para usarla.

```
mmcli --bearer 0
```
Detalles del portador enumerados:
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

**12. Acceda a la interfaz de red**.

```
sudo ip link set dev wwan0 up
```

**13. Configure la interfaz de red**.

Con la información proporcionada por los detalles del portador, reemplace la dirección IP (aquí 162.177.2.0/22) por la que tiene el portador:
```
sudo ip address add 162.177.2.0/22 dev wwan0
```

**14. Compruebe la información de IP**.

La configuración de IP de esta interfaz debe coincidir con los detalles del portador de ModemManager.
```
sudo ip address show dev wwan0
```
Vea que la dirección IP del portador se muestra a continuación:
```
wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
    inet 162.177.2.0/22 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::c012:44ff:fec4:273c/64 scope link 
       valid_lft forever preferred_lft forever
```

**15. Establezca la ruta predeterminada**.

Use de nuevo la información proporcionada por el portador y emplee la puerta de enlace del módem (reemplace 162.177.2.1) como destino predeterminado para los paquetes de red:
```
sudo ip route add default via 162.177.2.1 dev wwan0
```
Ahora debería haber habilitado Azure Percept para conectarse a Azure mediante el módem LTE.


**16. Pruebe la conectividad**.

Ejecutaremos una solicitud `ping` mediante la interfaz `wwan0`. También puede usar Azure Percept Studio y comprobar si llegan mensajes de telemetría (asegúrese de no tener habilitado el cable Ethernet o la red Wi-Fi para tener la seguridad de que usa LTE).
```
ping -I wwan0 8.8.8.8
```
Y debería obtener:
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
Para información general, consulte [Conexión mediante un módem USB](./connect-over-cellular-usb.md).
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>Reglas de módem de Vodafone para mitigar problemas de enumeración

Para evitar que el módem se enumere en un modo no compatible, se recomienda usar las siguientes reglas UDEV para que ModemManager omita las interfaces no deseadas.

Archivo que se debe crear: `/usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules`, con contenido como:
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
Una vez instalado, vuelva a cargar las reglas UDEV y reinicie ModemManager:
```
udevadm control -R
systemctl restart ModemManager
```
## <a name="next-steps"></a>Pasos siguientes
[Conexión mediante un módem USB](./connect-over-cellular-usb.md).

Vuelva al artículo principal sobre 5G o LTE:

[Conexión mediante 5G o LTE](./connect-over-cellular.md).
