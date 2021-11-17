---
title: Conexión de Azure Percept DK a través de 5G o LTE con un módem Quectel RM500 5G
description: En este artículo se explica cómo conectar Azure Percept DK mediante redes 5G o LTE con un módem Quectel 5G.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: de4f889883b7c3da4e8129fc66d31aed094ebda6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252428"
---
# <a name="connect-azure-percept-dk-over-5g-or-lte-by-using-a-quectel-rm500-gl-5g-modem"></a>Conexión de Azure Percept DK a través de 5G o LTE con un módem Quectel RM500-GL 5G  

En este artículo se analiza cómo conectar Azure Percept DK a través de 5G o LTE con un módem Quectel RM500-GL 5G. 

Para más información sobre este kit de desarrollo de módem 5G, póngase en contacto con el equipo de ventas local de Quectel:

* Para clientes en Norteamérica: northamerica-sales@quectel.com
* Para clientes en el resto del mundo: sales@quectel.com

> [!Note] 
> **Acerca de los cables USB para módems 5G**:  
> Los módems 5G requieren más energía que los módems LTE y usar un cable USB incorrecto puede implicar un cuello de botella que impida alcanzar las mejores velocidades de datos 5G posibles. Para suministrar energía suficiente y uniforme a un módem 5G, asegúrese de que el cable USB cumpla con estos estándares:  
> **Energía:**
> - El amperaje máximo debe ser igual o mayor que 3 amperes.
> - El cable no puede medir más de 1 metro de largo.
> - Cuando use un módem 5G, solo debe estar activo un puerto USB A en Azure Percept DK.
>  
> **Throughput** (Capacidad de proceso):
> - USB 3.1 Gen2
> - Certificado USB-IF

## <a name="prepare-to-connect-azure-percept-dk"></a>Preparación para conectar Azure Percept DK
Para información sobre cómo preparar Azure Percept DK, vaya a [Conexión de Azure Percept DK mediante redes 5G y LTE con un módem USB](./connect-over-cellular-usb.md). Asegúrese de tener en cuenta los comentarios sobre los cables USB que se deben usar. 

### <a name="prepare-the-modem"></a>Preparación del módem
Antes de empezar, el módem tiene que estar en modo Mobile Broadband Interface Model (MBIM). Se puede usar un comando Quectel Attention (AT) no documentado pero estándar para esto: `AT+QCFG="usbnet"`.

La propiedad `usbnet` se puede establecer en cuatro valores diferentes, de `0` a `3`:
- `0` para el **modo NDIS/ PPP/QMI** (compatible con el controlador `qmi_wwan`, habilitado con `CONFIG_USB_NET_QMI_WWAN=y|m`)
- `1` para el **modo Ethernet CDC** (compatible con Linux cuando `CONFIG_USB_NET_CDCETHER=y|m`)
- `2` para el **modo MBIM** (compatible con Linux cuando `CONFIG_USB_NET_CDC_MBIM=y|m`)
- `3` para el **modo RNDIS**

La manera más sencilla de configurar el modo es conectar el módem Quectel 5G a un equipo y usar software de terminal como TeraTerm o el propio software de PC de Quectel, como QCOM. Puede utilizar el administrador de dispositivos de Windows para ver qué puerto USB está asignado para el módem. La configuración del puerto COM debe ser la siguiente:
* **Velocidad en baudios**: 115 200
* **Bits de parada**: 1
* **Paridad**: ninguna
* **Tamaño de bytes**: 8
* **Control de flujo**: sin control de flujo

Estos son los comandos AT:

Para comprobar qué dispositivo Quectel en modo USB se ejecuta actualmente, utilice:

```
AT+QCFG="usbnet"
```

Para cambiar al modo 2, utilice:

```
AT+QCFG="usbnet",2
```

Si utiliza el primer comando AT para volver a comprobarlo, obtendrá lo siguiente:

```
+QCFG: "usbnet",2`
```

Una vez que establezca el modo USB correcto, emita un restablecimiento de hardware con:

```
AT+CFUN=1,1
```

En este momento, el módem debe desconectarse y volver a conectarse posteriormente al puerto USB.


## <a name="use-the-modem-to-connect"></a>Uso del módem para conectarse

1. Coloque una tarjeta SIM en el módem Quectel.

1. Conecte el módem Quectel al puerto USB de Azure Percept DK. Asegúrese de usar un cable USB adecuado.

1. Encienda Azure Percept DK.

1. Asegúrese de que ModemManager está en ejecución.

      ```
      systemctl status ModemManager
      ```
   
      Si el proceso se completa correctamente, recibirá un resultado similar al siguiente:

      ```
      * ModemManager.service - Modem Manager
         Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
         Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
      [...]
      ```

      Si no es así, asegúrese de que guardó la imagen correcta en la memoria flash del dispositivo Azure Percept DK (habilitado para 5G).

1. Enumere los módems activos.

      Al enumerar los módems, verá que se reconoció el módem Quectel y ahora lo controla ModemManager.

      ```
      mmcli --list-modems
      ```
   
      Debe obtener un resultado similar al siguiente:

      ```
      /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
      ```

      Aquí, el id. del módem es `0`, que se usa en los comandos siguientes para abordarlo (es decir, `--modem 0`).

1. Obtenga los detalles del módem.

      De manera predeterminada, el módem está deshabilitado (`Status -> state: disabled`). Para ver el estado, ejecute:

      ```
      mmcli --modem 0
      ```
      
      Debe obtener un resultado similar al siguiente:

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

1. Habilite el módem.

      Antes de establecer una conexión, ejecute lo siguiente para activar las radios del módem:

      ```
      mmcli --modem 0 --enable
      ```

      Debe obtener una respuesta similar a la siguiente:

      ```
      successfully enabled the modem
      ```
   
      Después de un tiempo, el módem se debería registrar en una torre de telefonía móvil, y se debería ver que el estado del módem es `Status -> state: registered` después de ejecutar este código:
   
      ```
      mmcli --modem 0
      ```

1. Para conectarse, use la información del nombre del punto de acceso (APN).

      Por lo general, los módems proporcionan el APN que se va a usar (consulte la información `3GPP EPS -> initial bearer APN`) a fin de que pueda usarlo para establecer una conexión. Si el módem no proporciona un APN, consulte con el proveedor de telefonía móvil el APN que debe usar. 
      
      Este es el comando de ModemManager para conectarse mediante, por ejemplo, el APN de Verizon`APN=vzwinternet`.

      ```
      mmcli --modem 0 --simple-connect="apn=vzwinternet"
      ```

      Nuevamente, debe obtener una respuesta similar a la siguiente:

      ```
      successfully connected the modem
      ```

1. Obtenga el estado del módem.

      Ahora debería ver un estado de `Status -> state: connected` y una categoría `Bearer` nueva al final del mensaje de estado.
 
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

1. Obtenga los detalles del portador.

      El portador resultante del paso anterior, `--simple-connect`, está en la ruta de acceso `/org/freedesktop/ModemManager1/Bearer/1`.

      Se trata del portador que consultamos para obtener la información del módem sobre la conexión activa. El portador inicial no está asociado a una conexión activa y, por lo tanto, no contiene información de IP.

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

      Estas son las descripciones de algunos detalles clave:
      - `Status -> interface: wwan0`: indica qué interfaz de red de Linux coincide con este módem.
      - `IPv4 configuration`: proporciona la configuración IP de la interfaz anterior para que se pueda utilizar.

1. Compruebe el estado de la interfaz de red del módem.

      De manera predeterminada, la interfaz de red muestra `DOWN`.

      ```
      ip link show dev wwan0
      ```

      Debe obtener un resultado similar al siguiente:

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
      ```

1. Abra la interfaz.

      ```
      sudo ip link set dev wwan0 up
      ```

1. Compruebe la información de IP.

      De manera predeterminada, la interfaz muestra `UP,LOWER_UP`, sin información de dirección IP.

      ```
      sudo ip address show dev wwan0
      ```

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. Emita una solicitud DHCP.

      Se trata de una característica específica del módulo Quectel, pero no limitada a él. Por lo general, la información de IP se debe establecer manualmente en la interfaz o a través de un demonio de administrador de red que admita ModemManager (por ejemplo, NetworkManager), pero aquí simplemente puede usar dhclient en el módem Quectel:

      ```
      sudo dhclient wwan0
      ```

1. Compruebe la información de IP.

      La configuración de IP de esta interfaz debe coincidir con los detalles del portador de ModemManager.

      ```
      sudo ip address show dev wwan0
      ```

      Debe obtener un resultado similar al siguiente:

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
            valid_lft forever preferred_lft forever
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. Compruebe las rutas de la interfaz.

      Observe que el cliente DHCP también establece una ruta predeterminada para que los paquetes pasen por la interfaz `wwan0`.

      ```
      ip route show dev wwan0
      ```

      Debe obtener un resultado similar al siguiente:

      ```
      default via 25.21.113.166 
      25.21.113.164/30 proto kernel scope link src 25.21.113.165
      ```

      Ya estableció una conexión a Azure Percept DK con el módem Quectel.


1. Pruebe la conectividad.

      Ejecute una solicitud `ping` mediante la interfaz `wwan0`.

      ```
      ping -I wwan0 8.8.8.8
      ```

      Debe obtener un resultado similar al siguiente:

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

Para información general sobre la depuración, consulte [Conexión mediante un módem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Pasos siguientes

En función del dispositivo de telefonía móvil al que tenga acceso, puede conectarse de una de estas dos maneras:

* [Conexión mediante un módem USB](./connect-over-cellular-usb.md)
* [Conexión mediante 5G o LTE](./connect-over-cellular.md)
