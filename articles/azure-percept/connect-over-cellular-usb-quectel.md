---
title: Conexión de Azure Percept a través de 5G o LTE con un módem USB Quectel RM500
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE mediante un módem USB Quectel.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 52914140f80bad9c8bd8bb57bec0938211c25e71
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007075"
---
# <a name="connect-azure-percept-over-5g-or-lte-with-usb-modem-quectel-5g-rm500-gl"></a>Conectar Azure Percept sobre 5G o GSM con módem USB Quectel 5G RM500-GL 

Estos son los pasos para conectar Azure Percept mediante el módem Quectel 5G RM500-GL. Puede ponerse en contacto con el equipo de ventas local de Quectel para obtener más detalles sobre este kit para desarrolladores de módems 5G:

northamerica-sales@quectel.com: para clientes de NA

sales@quectel.com: para clientes globales

> [!Note] 
> Sobre los módems 5G y cables USB. Esto solo se aplica a los módems 5G. Los módems 5G requieren más potencia que los módems LTE. Además, el cable USB puede convertirse en un cuello de botella para las mejores velocidades de datos 5G. Para suministrar una potencia suficiente y constante a un módem 5G, debe asegurarse de que el cable USB no sea demasiado largo y pueda resistir una corriente de al menos 3A. Para obtener el máximo rendimiento, debe usar cables USB 3.1 Gen2 y asegurarse de que haya un logotipo USB-IF que indique la certificación. Por lo tanto, como resumen: **Para potencia**:
> - El amperaje máximo debe ser igual o mayor que 3 Amp.
> - Menos de 1 m de longitud
> - Cuando se usan módems 5G, solo un puerto USB A en Azure Percept DK debe estar activo.
>  
> **Para rendimiento:**
> - USB 3.1 Gen2
> - Certificado USB-IF

## <a name="preparation"></a>Preparación
Asegúrese de que ha realizado los preparativos de Azure Percept desde aquí: [Conexión mediante un módem USB](./connect-over-cellular-usb.md), y que ha anotado los comentarios sobre los cables USB que se deben usar. 

### <a name="preparation-of-the-modem"></a>Preparación del módem
Para empezar, necesitamos que el módem esté en modo MBIM. Se puede usar un comando Quectel AT no documentado pero estándar para esto: `AT+QCFG="usbnet"`.

La propiedad `usbnet` se puede establecer en cuatro valores diferentes, de `0` a `3`:
- `0` para el **modo NDIS/ PPP/QMI** (compatible con el controlador `qmi_wwan`, habilitado con `CONFIG_USB_NET_QMI_WWAN=y|m`)
- `1` para el **modo Ethernet CDC** (compatible con Linux cuando `CONFIG_USB_NET_CDCETHER=y|m`)
- `2` para el **modo MBIM** (compatible con Linux cuando `CONFIG_USB_NET_CDC_MBIM=y|m`)
- `3` para el **modo RNDIS**

La manera más fácil de configurar el modo es conectar el módem Quectel 5G a un equipo y usar software terminal como TeraTerm o el propio software de PC de Quectel, como QCOM. Con el Administrador de dispositivos de Windows puede ver qué puerto USB se asigna para los comandos AT y usarlo. La configuración del puerto COM debe ser: Velocidad de bits: 115200 Bits de parada: 1 Paridad: Ninguna Tamaño de bytes: 8 Flujo de control: Sin flujo de control

Y estos son los comandos AT: Para comprobar qué dispositivo Quectel en modo USB es:
```
AT+QCFG="usbnet"
```
Cambie al modo 2:
```
AT+QCFG="usbnet",2
```
Y, si vuelve a comprobarlo usando primero el comando de AT, obtendría: "+QCFG: "usbnet",2".

Una vez que haya establecido el modo USB correcto, debe emitir el restablecimiento de hardware con:
```
AT+CFUN=1,1
```
En este momento, el módem debe desconectarse y volver a conectarse posteriormente al puerto USB.


## <a name="using-the-modem-to-connect"></a>Uso del módem para conectarse

**1. Coloque una tarjeta SIM en el módem de Quectel**.

**2. Conecte el módem Quectel al puerto USB de Azure Percept. No olvide usar un cable USB adecuado**.

**3. Encienda Azure Percept**.

**4. Asegúrese de que ModemManager se esté ejecutando**.

```
systemctl status ModemManager
```
Debe obtener un resultado parecido a este.
```
* ModemManager.service - Modem Manager
   Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
   Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
[...]
```
Si no es así, debe asegurarse de que ha guardado la imagen correcta en la memoria flash del dispositivo Azure Percept (habilitado para 5G).

**5. Enumere los módems activos**.

Al enumerar los módems, debería ver que el módem Quectel se ha reconocido y ahora lo controla ModemManager.
```
mmcli --list-modems
```
Y obtiene algo parecido a esto:
```
 /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
```
El identificador del módem está aquí `0`, que se usa en los siguientes comandos para solucionarlo (es decir, `--modem 0`).

**6. Obtenga los detalles del módem**.

De forma predeterminada, el módem está deshabilitado (`Status -> state: disabled`), consulte el estado:
```
mmcli --modem 0
```
Y debería obtener algo parecido a esto:
```
General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: disabled
           |             power state: on
           |          signal quality: 0% (cached)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
```

**7. Habilite el módem**.

Antes de establecer una conexión, es necesario activar las radios del módem.
```
mmcli --modem 0 --enable
```
Y debería obtener esto:
```
successfully enabled the modem
```
Después de un tiempo, el módem debe registrarse en una torre de celdas y debería ver el estado del módem: `Status -> state: registered`. Puede volver a comprobarlo mediante:
```
mmcli --modem 0
```

**8. Conéctese mediante la información de APN**.

Normalmente, los módems proporcionarán qué APN usar (consulte la información de `3GPP EPS -> initial bearer APN`) para que pueda usarlo para establecer una conexión. De lo contrario, consulte con el proveedor de teléfonos móviles para que lo use la APN. Este es el comando ModemManager para conectarse mediante, por ejemplo, la APN de Verizon `APN=vzwinternet`.
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"
```
Y, de nuevo, debería obtener esto:
```
successfully connected the modem
```

**9. Obtenga el estado del módem**.

Debería ver ahora `Status -> state: connected` y una nueva categoría `Bearer` al final del mensaje de estado.
```
mmcli -m 0
```
```
-----------------------------------
  General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: connected
           |             power state: on
           |             access tech: lte
           |          signal quality: 12% (recent)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
           |             operator id: 311480
           |           operator name: Verizon
           |            registration: home
           |                     pco: 0: (partial) '27058000FF0100'

  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |     initial bearer path: /org/freedesktop/ModemManager1/Bearer/0
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
  -----------------------------------
  Bearer   |                   paths: /org/freedesktop/ModemManager1/Bearer/1

```

**10. Obtenga los detalles del portador**.

El portador resultante del `--simple-connect` anterior está aquí en la ruta de acceso: `/org/freedesktop/ModemManager1/Bearer/1`.
Este es el que estamos consultando la información del módem sobre la conexión activa. El portador inicial no está asociado a una conexión activa y, por tanto, no contendrá información de IP.
```
mmcli --bearer 1
```
```
  --------------------------------
  General            |       path: /org/freedesktop/ModemManager1/Bearer/1
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwan0
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: fast.t-mobile.com
                     |    roaming: allowed
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 25.21.113.165
                     |     prefix: 30
                     |    gateway: 25.21.113.166
                     |        dns: 10.177.0.34, 10.177.0.210
                     |        mtu: 1500
  --------------------------------
  Statistics         |   attempts: 1
```
Algunos detalles sobre:
- `Status -> interface: wwan0`: enumera qué interfaz de red de Linux coincide con este módem.
- `IPv4 configuration`: proporciona la configuración IP de la interfaz anterior para que se pueda usar.

**11. Compruebe el estado de la interfaz de red del módem**.

La interfaz de red debe ser `DOWN` de forma predeterminada.
```
ip link show dev wwan0
```
El resultado es:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
```

**12. Abra la interfaz**.

```
sudo ip link set dev wwan0 up
```

**13. Compruebe la información de IP**.

Debería ver la interfaz como `UP,LOWER_UP` sin información IP de forma predeterminada.
```
sudo ip address show dev wwan0
```
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**14. Emita una solicitud DHCP**.

Se trata de una característica específica del módulo Quectel, pero no limitado a él. Normalmente, la información de IP se debe establecer manualmente en la interfaz o a través de un demonio de administrador de red que admita ModemManager (por ejemplo, NetworkManager), pero aquí simplemente podemos usar dhclient en el módem Quectel:
```
sudo dhclient wwan0
```

**15. Compruebe la información de IP**.

La configuración de IP de esta interfaz debe coincidir con los detalles del portador de ModemManager.
```
sudo ip address show dev wwan0
```
Y los resultados:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**16. Compruebe de las rutas de interfaz**.

Observe que el cliente DHCP también establece una ruta predeterminada para que los paquetes pasen por la interfaz `wwan0`.
```
ip route show dev wwan0
```
como esto:
```
default via 25.21.113.166 
25.21.113.164/30 proto kernel scope link src 25.21.113.165
```
Por lo tanto, ahora ha establecido una conexión a Azure mediante el módem Quectel.


**17. Pruebe la conectividad**.

Ejecutaremos una solicitud `ping` mediante la interfaz `wwan0`.
```
ping -I wwan0 8.8.8.8
```
```
PING 8.8.8.8 (8.8.8.8) from 25.21.113.165 wwan0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=137 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=114 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 2ms
rtt min/avg/max/mdev = 113.899/125.530/137.162/11.636 ms
```

## <a name="debugging"></a>Depuración
Consulte [Conexión mediante módem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Pasos siguientes
[Conexión mediante un módem USB](./connect-over-cellular-usb.md)

Vuelva al artículo principal sobre 5G o LTE:

[Conexión mediante 5G o LTE](./connect-over-cellular.md).  