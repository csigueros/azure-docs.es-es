---
title: Conexión de Azure Percept a través de LTE con el módem USB Multiconnect de MultiTech
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE mediante el módem USB de MultiTech.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 336e67de6d178dcff1b57fe75d57f2272386d2c5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007097"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-multitech-multiconnect"></a>Conexión de Azure Percept a través de LTE con el módem USB Multiconnect de MultiTech 
Estos son los pasos para conectar Azure Percept mediante el módem USB Multiconnect (MTCM-LNA3-B03) de MultiTech. 

> [!Note]
> Hay varios modelos, y hemos usado LNA3 que funciona al menos con tarjetas SIM de Verizon y Vodafone. No pudimos conectarnos a una red de AT&T, pero estamos investigando el problema y modificaremos esta información si encontramos la causa principal. Puede encontrar más información sobre este hardware de módem en particular en esta página: https://www.multitech.com/brands/multiconnect-microcell.

## <a name="preparation"></a>Preparación
Asegúrese de que ha realizado los preparativos de Azure Percept desde aquí: [Conexión mediante un módem USB](./connect-over-cellular-usb.md), y que ha anotado los comentarios sobre los cables USB que se deben usar. 

### <a name="preparation-of-the-modem"></a>Preparación del módem
Para empezar, necesitamos que el módem esté en modo MBIM. Para saber cómo hacerlo, puede consultar la guía de referencia de comandos de AT aquí: https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf.

Usamos el comando AT `AT#USBCFG=<mode>` para configurar el modo USB correcto para habilitar la interfaz MBIM.

En la guía de referencia de comandos de AT se enumeran todos los modos posibles, pero nos interesa el modo `3`; el valor predeterminado es `0`.

La manera más fácil de configurar el modo es conectar el módem de MultiTech a un equipo y usar software de terminal, como TeraTerm o Putty PC. Con el Administrador de dispositivos de Windows puede ver qué puerto USB está asignado al módem; es posible que tenga que probar cuál responde a los comandos AT, si hay varios. La configuración del puerto COM debe ser: Velocidad de bits: 9600 (o 115200) Bits de parada: 1 Paridad: Ninguna Tamaño de bytes: 8 Flujo de control: Sin flujo de control

Y estos son los comandos AT: Para comprobar qué dispositivo MultiTech en modo USB está actualmente:
```
AT#USBCFG?
```
Cambie al modo 3:
```
AT#USBCFG=3
```
Y, si vuelve a comprobarlo usando primero el comando de AT, obtendría: `#USBCFG: 3`.

Cuando haya establecido el modo USB correcto, debe emitir un restablecimiento con:
```
AT#REBOOT
```
En este momento, el módem debe desconectarse y volver a conectarse posteriormente al puerto USB mediante el modo establecido anteriormente.

## <a name="using-the-modem-to-connect"></a>Uso del módem para conectarse
Asegúrese de que ha realizado los preparativos de Azure Percept desde aquí: [Conexión mediante un módem USB](./connect-over-cellular-usb.md).   

**1. Conecte una tarjeta SIM en el módem de MultiTech**.

**2. Conecte el módem de MultiTech al puerto USB A de Azure Percept**.

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

En este caso, debería ver que ModemManager ha reconocido el modelo FIH7160.
```
mmcli --list-modems
```
Y recibirá algo parecido a esto: */org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160*

**7. Obtenga los detalles del módem**.

El identificador del módem es `0`, que podría ser diferente en su caso. El identificado del módem (`--modem 0`) se usa en los comandos de ModemManager como este:
```
mmcli --modem 0
```
De forma predeterminada, el módem está deshabilitado (`Status -> state: disabled`).
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

**8. Habilite el módem**.

Antes de establecer una conexión, es necesario activar las radios del módem.
```
mmcli --modem 0 --enable
```
Y debería obtener una respuesta como esta: *se habilitó correctamente el módem*.

Después de un tiempo, el módem debe registrarse en una torre de celdas y debería ver el estado del módem: `Status -> state: registered`, si vuelve a ejecutar:
```
mmcli --modem 0
```

**9. Conéctese mediante la información de APN**.

El proveedor de telefonía móvil proporciona el nombre del punto de acceso APN, igual que aquí para Verizon:
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"  
```
y, si todo es correcto, *habrá conectado correctamente el módem*.

**10. Obtenga el estado del módem**.

Debería ver ahora `Status -> state: connected` y una nueva categoría `Bearer`val final del mensaje de estado.
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

**11. Obtenga los detalles del portador**.

Los detalles del portador son necesarios para conectar el sistema operativo a la conexión de datos de paquetes que el módem ha establecido ahora con la red de telefonía móvil. Por lo tanto, en este momento, el módem tiene conexión IP, pero el sistema operativo no está configurado aún para usarla.
```
mmcli --bearer 0
```
Detalles del portador enumerados:
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

**12. Acceda a la interfaz de red**.

```
sudo ip link set dev wwan0 up
```

**13. Configure la interfaz de red**.

Con la información proporcionada por el portador, reemplace la dirección IP (aquí 100.112.107.46/24) por la que tiene el portador:
```
sudo ip address add 100.112.107.46/24 dev wwan0
```

**14. Compruebe la información de IP**.

La configuración de IP de esta interfaz debe coincidir con los detalles del portador de ModemManager.
```
sudo ip address show dev wwan0
```
Vea que la dirección IP del portador se muestra a continuación:
```
6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
    inet 100.112.107.46/24 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
       valid_lft forever preferred_lft forever
```

**15. Establezca la ruta predeterminada**.

Use de nuevo la información proporcionada por el portador y emplee la puerta de enlace del módem (reemplace 100.112.107.1) como destino predeterminado para los paquetes de red:
```
sudo ip route add default via 100.112.107.1 dev wwan0
```
Ahora Azure Percept tiene una conexión que usa el módem USB.

**16. Pruebe la conectividad**.

Ejecutaremos una solicitud `ping` mediante la interfaz `wwan0`. Sin embargo, también puede usar Azure Percept Studio y comprobar si llegan mensajes de telemetría (compruebe que no tiene habilitado el cable Ethernet o la red Wi-Fi para tener la seguridad de que usa LAN).
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

## <a name="next-steps"></a>Pasos siguientes
[Conexión mediante un módem USB](./connect-over-cellular-usb.md).

Vuelva al artículo principal sobre 5G o LTE:

[Conexión mediante 5G o LTE](./connect-over-cellular.md).
   

