---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 0b2d619db998a2339387cb6e2a4c80271fcf6b76
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336172"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Incorporación de direcciones IP a un sistema operativo de la máquina virtual

Conéctese e inicie sesión en una máquina virtual que creó con múltiples direcciones IP privadas. Debe agregar manualmente todas las direcciones IP privadas (incluida la principal) que ha agregado a la máquina virtual. Complete los pasos siguientes para el sistema operativo de su máquina virtual.

### <a name="windows-server"></a>Windows Server

<details>
  <summary>Expanda</summary>

1. En un símbolo del sistema, escriba *ipconfig /all*.  Solo verá la dirección IP privada *principal* (por medio de DHCP).
2. Escriba *ncpa.cpl* en el símbolo del sistema para abrir la ventana **Conexiones de red**.
3. Abra las propiedades del adaptador correspondiente: **Ethernet**.
4. Haga doble clic en el Protocolo de Internet versión 4 (IPv4).
5. Seleccione **Usar la siguiente dirección IP** y escriba los valores siguientes:

    * **Dirección IP**: escriba la dirección IP privada *principal*
    * **Máscara de subred**: establezca este valor en función de la subred. Por ejemplo, si la subred es una subred /24, la máscara de subred es 255.255.255.0.
    * **Puerta de enlace predeterminada**: la primera dirección IP de la subred. Si la subred es 10.0.0.1/24, la dirección IP de la puerta de enlace es 10.0.0.0/24.
    * Seleccione **Usar las siguientes direcciones de servidor DNS** y escriba los valores siguientes:
        * **Servidor DNS preferido**: escriba 168.63.129.16 si no usa un servidor DNS propio.  Si usa su propio servidor DNS, escriba la dirección IP de su servidor.  Para el servidor DNS alternativo, puede elegir cualquier dirección de servidor DNS público que esté disponible.
    * Seleccione el botón **Avanzadas** y agregue más direcciones IP. Agregue cada una de las direcciones IP privadas secundarias, que agregó a la interfaz de red de Azure en un paso anterior, a la interfaz de red de Windows que se asigna a la dirección IP principal asignada a la interfaz de red de Azure.

        No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual. Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure; de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](../articles/virtual-network/virtual-network-network-interface-addresses.md#private). Nunca debe asignar una dirección IP pública de Azure dentro del sistema operativo.

    * Haga clic en **Aceptar** para cerrar la configuración de TCP/IP y en **Aceptar** otra vez para cerrar la configuración del adaptador. Se restablece la conexión RDP.

6. En un símbolo del sistema, escriba *ipconfig /all*. Compruebe que se muestran todas las direcciones IP que ha agregado y que DHCP está desactivado.
7. Configure Windows para usar la dirección IP privada de la configuración IP principal de Azure como dirección IP principal en Windows. Para más información, consulte [Sin acceso a Internet desde una máquina virtual Windows de Azure que tiene varias direcciones IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse). 

#### <a name="validation-windows-server"></a>Validación (Windows Server)

Para comprobar que puede conectarse a Internet desde su configuración de IP secundaria mediante la dirección IP pública asociada, después de haberla agregado correctamente mediante los pasos anteriores, utilice el comando siguiente y sustituya 10.0.0.7 por la dirección IP privada secundaria:

```bash
ping -S 10.0.0.7 outlook.com
```
 
> [!NOTE]
> Para las configuraciones de IP secundarias, solo se puede hacer ping a Internet si la configuración tiene una dirección IP pública asociada. Para configuraciones de IP principales, no se requiere una dirección IP pública para hacer ping a Internet.

</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)

<details>
  <summary>Expanda</summary>

Se recomienda revisar la documentación más reciente de la distribución de Linux. 

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

   ```bash
   sudo -i
   ```

3. Actualice el archivo de configuración de la interfaz de red (suponiendo que sea "eth0").

   * Mantenga el elemento de línea existente para dhcp. La dirección IP principal permanece configurada que estaba.
   * Agregue una configuración para una dirección IP estática adicional con los siguientes comandos:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Debería ver un archivo .cfg.
4. Abra el archivo. Debería ver las siguientes líneas al final del archivo:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Agregue las líneas siguientes después de las que existen en este archivo:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Guarde el archivo mediante el comando siguiente:

   ```bash
   :wq
   ```

7. Restablezca la interfaz de red con el comando siguiente:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Si usa una conexión remota, ejecute ifdown e ifup en la misma línea.
   >

8. Compruebe que la dirección IP se agregue a la interfaz de red con el comando siguiente:

   ```bash
   ip addr list eth0
   ```

   Debería ver la dirección IP que agregó en la lista.

#### <a name="validation-ubuntu-1416"></a>Validación (Ubuntu 14/16)

Para asegurarse de que puede conectarse a internet desde su configuración de IP secundaria a través de la dirección IP pública asociada, use el siguiente comando:

```bash
ping -I 10.0.0.5 outlook.com
```

> [!NOTE]
> Para las configuraciones de IP secundarias, solo se puede hacer ping a Internet si la configuración tiene una dirección IP pública asociada. Para configuraciones de IP principales, no se requiere una dirección IP pública para hacer ping a Internet.

Para máquinas virtuales Linux, al intentar validar la conectividad saliente de una NIC secundaria, debe agregar las rutas adecuadas. Existen distintas formas de hacerlo. Consulte la documentación correspondiente a su distribución de Linux. El siguiente es un método para lograr esto:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom
```

- No olvide reemplazar:
    - **10.0.0.5** con la dirección IP privada que tiene una dirección IP pública asociada
    - **10.0.0.1** con su puerta de enlace predeterminada
    - **eth2** con el nombre de la NIC secundaria 

</details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)

<details>
  <summary>Expanda</summary>

Ubuntu 18.04 y versiones posteriores han cambiado a `netplan` para la administración de red del sistema operativo. Se recomienda revisar la documentación más reciente de la distribución de Linux. 

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

    ```bash
    sudo -i
    ```

3. Cree un archivo para la segunda interfaz y ábralo en un editor de texto:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Agregue las líneas siguientes al archivo, reemplazando `10.0.0.6/24` por la dirección IP o máscara de red:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Guarde el archivo mediante el comando siguiente:

    ```bash
    :wq
    ```

6. Pruebe los cambios mediante [netplan try](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) para confirmar la sintaxis:

    ```bash
    netplan try
    ```

    > [!NOTE]
    > `netplan try` aplicará los cambios de forma temporal y los revertirá después de 120 segundos. Si se produce una pérdida de conectividad, espere 120 segundos y vuelva a conectarse. En ese momento, los cambios se habrán revertido.

7. Si no hay ningún problema con `netplan try`, aplique los cambios de configuración:

    ```bash
    netplan apply
    ```

8. Compruebe que la dirección IP se agregue a la interfaz de red con el comando siguiente:

    ```bash
    ip addr list eth0
    ```

    Debería ver la dirección IP que agregó en la lista. Ejemplo:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```

#### <a name="validation-ubuntu-1804"></a>Validación (Ubuntu 18.04+)

Para asegurarse de que puede conectarse a internet desde su configuración de IP secundaria a través de la dirección IP pública asociada, use el siguiente comando:

```bash
ping -I 10.0.0.5 outlook.com
```

>[!NOTE]
>Para las configuraciones de IP secundarias, solo se puede hacer ping a Internet si la configuración tiene una dirección IP pública asociada. Para configuraciones de IP principales, no se requiere una dirección IP pública para hacer ping a Internet.

Para máquinas virtuales Linux, al intentar validar la conectividad saliente de una NIC secundaria, debe agregar las rutas adecuadas. Existen distintas formas de hacerlo. Consulte la documentación correspondiente a su distribución de Linux. El siguiente es un método para lograr esto:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom
```

- No olvide reemplazar:
    - **10.0.0.5** con la dirección IP privada que tiene una dirección IP pública asociada
    - **10.0.0.1** con su puerta de enlace predeterminada
    - **eth2** con el nombre de la NIC secundaria 

</details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS y otros)

<details>
  <summary>Expanda</summary>

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

    ```bash
    sudo -i
    ```

3. Escriba la contraseña y siga las instrucciones que aparezcan. Una vez que sea el usuario raíz, vaya a la carpeta de scripts de red con el comando siguiente:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Muestra una lista de los archivos de ifcfg relacionados con el siguiente comando:

    ```bash
    ls ifcfg-*
    ```

    Debería ver *ifcfg-eth0* como uno de los archivos.

5. Para agregar una dirección IP, cree un archivo de configuración, tal y como se muestra a continuación. Tenga en cuenta que debe crearse un archivo para cada configuración de IP.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Abra el archivo *ifcfg-eth0:0* con el comando siguiente:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Agregue contenido al archivo, *eth0:0* en este caso, con el siguiente comando. Asegúrese de actualizar la información en función de su dirección IP.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Guarde el archivo mediante el comando siguiente:

    ```bash
    :wq
    ```

9. Reinicie los servicios de red y asegúrese de que los cambios sean correctos con los comandos siguientes:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Debería ver la dirección IP que agregó, *eth0:0*, en la lista que se devuelve.

#### <a name="validation-red-hat-centos-and-others"></a>Validación (Red Hat, CentOS y otros)

Para asegurarse de que puede conectarse a internet desde su configuración de IP secundaria a través de la dirección IP pública asociada, use el siguiente comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para las configuraciones de IP secundarias, solo se puede hacer ping a Internet si la configuración tiene una dirección IP pública asociada. Para configuraciones de IP principales, no se requiere una dirección IP pública para hacer ping a Internet.

Para máquinas virtuales Linux, al intentar validar la conectividad saliente de una NIC secundaria, debe agregar las rutas adecuadas. Existen distintas formas de hacerlo. Consulte la documentación correspondiente a su distribución de Linux. El siguiente es un método para lograr esto:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom
```

- No olvide reemplazar:
    - **10.0.0.5** con la dirección IP privada que tiene una dirección IP pública asociada
    - **10.0.0.1** con su puerta de enlace predeterminada
    - **eth2** con el nombre de la NIC secundaria 


</details>

### <a name="debian-gnulinux"></a>Debian GNU/Linux

<details>
  <summary>Expanda</summary>

1. Abra una ventana del terminal.
1. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

   ```bash
   sudo -i
   ```

1. Actualice el archivo de configuración de la interfaz de red (suponiendo que sea "eth0").

   * Abra el archivo de interfaz de red mediante el comando siguiente:
     
     ```bash
     vi /etc/network/interfaces
     ```
    
   * Debería ver las siguientes líneas al final del archivo:
    
      ```bash
      auth eth0
      iface eth0 inet dhcp
      ```
    
   * Deje el elemento de línea existente para dhcp como está. La dirección IP principal permanece configurada que estaba.
   * Agregue las líneas siguientes después de las que existen en este archivo:

     ```bash
     iface eth0 inet static
     address <your private IP address here> 
     netmask <your subnet mask> 
     ```

1. Guarde el archivo mediante el comando siguiente:

   ```bash
   :wq! 
   ```

1. Reinicie los servicios de red para que los cambios surtan efecto. En Debian 8 y versiones posteriores, esta acción se puede hacer mediante el comando siguiente:

   ```bash
   systemctl restart networking
   ```
   En versiones anteriores de Debian, puede usar los siguientes comandos:
    
   ```bash
   service networking restart
   ```

1. Compruebe que la dirección IP se agregue a la interfaz de red con el comando siguiente:

   ```bash
   ip addr list eth0
    ```

Debería ver la dirección IP que agregó en la lista. Ejemplo:

```bash
 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
  link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
  inet 127.0.0.1/8 scope host lo
     valid_lft forever preferred_lft forever
  inet6 ::1/128 scope host
     valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
  link/ether 00:0d:3a:1d:1d:64 brd ff:ff:ff:ff:ff:ff
  inet 10.2.0.5/24 brd 10.2.0.255 scope global eth0
     valid_lft forever preferred_lft forever
  inet 10.2.0.6/24 brd 10.2.0.255 scope global secondary eth0
     valid_lft forever preferred_lft forever
  inet6 fe80::20d:3aff:fe1d:1d64/64 scope link
     valid_lft forever preferred_lft forever
 ```

</details>
