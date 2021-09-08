---
title: Preparación de máquinas para la migración sin agente con Azure Migrate
description: Aprenda a preparar máquinas locales para la migración sin agente con Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 4d819993f2c0fdab6da67e98fc767020e543844f
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720444"
---
# <a name="prepare-for-vmware-agentless-migration"></a>Preparación para la migración sin agentes de VMware

En este artículo se proporciona información general sobre los cambios realizados al [migrar máquinas virtuales de VMware a Azure con el método de migración sin agente](./tutorial-migrate-vmware.md) mediante la herramienta Azure Migrate: Server Migration.

Antes de migrar la máquina virtual local a Azure, es posible que necesite algunos cambios para preparar la máquina virtual para Azure. Estos cambios son importantes para asegurarse de que la máquina virtual migrada pueda arrancar correctamente en Azure y se pueda establecer la conectividad con la máquina virtual de Azure.
Azure Migrate controla automáticamente estos cambios de configuración para las versiones del sistema operativo mencionadas a continuación de Linux y Windows. Este proceso se denomina *hidratación*.

**Versiones del sistema operativo compatibles con la hidratación**

- Windows Server 2008 o posterior
- Red Hat Enterprise Linux 8, 7.9, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x
- CentOS 8, 7.7, 7.6, 7.5, 7.4, 6.x
- SUSE Linux Enterprise Server 15 SP0, 15 SP1, 12, 11
- Ubuntu 20.04, 19.04, 19.10, 18.04LTS, 16.04LTS, 14.04LTS
- Ubuntu 18.04LTS, 16.04LTS
- Debian 9, 8, 7
- Oracle Linux 6, 7.7, 7.7-CI y 7.8

También puede usar este artículo para preparar manualmente las máquinas virtuales para la migración a Azure de las versiones de sistemas operativos no enumeradas anteriormente. En un nivel general, estos cambios incluyen:

- Validación de la presencia de los controladores necesarios
- Habilitación de la consola serie
- Configuración de la red
- Instalación del agente invitado de la máquina virtual

## <a name="hydration-process"></a>Proceso de hidratación

Debe realizar algunos cambios en la configuración de las máquinas virtuales antes de la migración para asegurarse de que las máquinas virtuales migradas funcionen correctamente en Azure. Azure Migrate controla estos cambios de configuración a través del proceso de *hidratación*. El proceso de hidratación solo se realiza para las versiones de los sistemas operativos compatibles con Azure indicados anteriormente. Antes de migrar, es posible que tenga que realizar manualmente los cambios necesarios para otras versiones del sistema operativo que no están en la lista anterior. Si la máquina virtual se migra sin los cambios necesarios, es posible que la máquina virtual no arranque o que no tenga conectividad con la máquina virtual migrada. En el diagrama siguiente se muestra que Azure Migrate realiza el proceso de hidratación.

 [![Pasos del proceso de hidratación](./media/concepts-prepare-vmware-agentless-migration/hydration-process-inline.png)](./media/concepts-prepare-vmware-agentless-migration/hydration-process-expanded.png#lightbox)

Cuando un usuario desencadena *Migración de prueba* o *Migrar*, Azure Migrate realiza el proceso de hidratación para preparar la máquina virtual local para la migración a Azure.
Para configurar el proceso de hidratación, Azure Migrate crea una máquina virtual de Azure temporal y conecta los discos de la máquina virtual de origen para realizar los cambios y preparar la máquina virtual de origen para Azure. Esta máquina virtual de Azure temporal es una máquina virtual intermedia creada durante el proceso de migración antes de que se cree la máquina virtual migrada final. La máquina virtual temporal se creará con un tipo de sistema operativo similar (Windows o Linux) mediante una de las imágenes del sistema operativo de Marketplace. Si en la máquina virtual local se ejecuta Windows, el disco del sistema operativo de la máquina virtual local se conectará como disco de datos a la máquina virtual temporal para realizar los cambios. Si se trata de un servidor Linux, todos los discos conectados a la máquina virtual local se conectarán como discos de datos a la máquina virtual temporal de Azure.

Azure Migrate creará la interfaz de red, una nueva red virtual, una subred y un grupo de seguridad de red para hospedar la máquina virtual temporal. Estos recursos se crean en la suscripción del cliente. Si hay directivas en conflicto que impiden la creación de los artefactos de red, Azure Migrate intentará crear la máquina virtual temporal de Azure en la red virtual y la subred proporcionadas como parte de las opciones de configuración del destino de la replicación.

Una vez creada la máquina virtual, Azure Migrate invocará la [extensión de script personalizado](../virtual-machines/extensions/custom-script-windows.md) en la máquina virtual temporal mediante la API REST de Azure Virtual Machines. La utilidad de extensión de script personalizado ejecutará un script de preparación que contiene la configuración necesaria de la preparación para Azure en los discos de máquina virtual locales conectados a la máquina virtual temporal de Azure. El script de preparación se descarga de una cuenta de almacenamiento propiedad de Azure Migrate. Las reglas del grupo de seguridad de red de la red virtual se configurarán para permitir que la máquina virtual temporal de Azure acceda a la cuenta de almacenamiento de Azure Migrate para invocar el script.

 ![Pasos de migración](./media/concepts-vmware-agentless-migration/migration-steps.png)

## <a name="changes-performed-during-the-hydration-process"></a>Cambios realizados durante el proceso de hidratación

El script de preparación ejecuta los siguientes cambios en función del tipo de sistema operativo de la máquina virtual de origen que se va a migrar. También puede usar esta sección como guía para preparar manualmente las máquinas virtuales para la migración de versiones de sistemas operativos no compatibles con la hidratación.

### <a name="changes-performed-on-windows-servers"></a>Cambios realizados en servidores Windows

1. **Detección y preparación del volumen del sistema operativo Windows**

   Antes de realizar los cambios de configuración pertinentes, el script de preparación validará si se seleccionó el disco del sistema operativo correcto para la migración. El script de preparación buscará todos los volúmenes asociados visibles para el sistema y buscará la ruta de acceso del archivo de Hive del Registro del sistema para encontrar el volumen del sistema operativo de origen.

   Se llevan a cabo las acciones siguientes en este paso:
   - Se monta cada partición en el disco del sistema operativo conectado a la máquina virtual temporal.

   - Se buscan los archivos de registro de \Windows\System32\Config\System después de montar la partición.
   - Si no se encuentran los archivos, la partición se desmonta y la búsqueda continúa para la partición correcta.
   - Si los archivos no están presentes en ninguna de las particiones, puede que eso indique que se seleccionó un disco del sistema operativo incorrecto o que este está dañado. Se producirá un error en el proceso de migración de Azure Migrate y se generará el mensaje de error correspondiente.

   >[!NOTE]
   >Este paso no es importante si está preparando manualmente los servidores para la migración.

1. **Realización de cambios relacionados con el arranque y la conectividad**

   Una vez detectados los archivos del volumen del sistema operativo de origen, el script de preparación cargará el subárbol del registro del sistema en el Editor del Registro de la máquina virtual temporal de Azure y realizará los siguientes cambios para garantizar el arranque y la conectividad de la máquina virtual. Deberá configurar estos valores manualmente si la versión del sistema operativo no es compatible con la hidratación.

   1. **Validación de la presencia de los controladores necesarios**
      
      Asegúrese de que los controladores necesarios estén instalados y establecidos para cargarse al **iniciar el arranque**. Estos controladores de Windows permiten al servidor comunicarse con el hardware y con otros dispositivos conectados.

      - IntelIde.sys
      - Atapi
      - Storfit
      - Storvsc
      - VMbus

   1. **Establecimiento de la directiva de red de área de almacenamiento (SAN) en Todos en línea**

      Esto garantiza que los volúmenes de Windows en la máquina virtual de Azure usen las mismas asignaciones de letra de unidad que la máquina virtual local. De forma predeterminada, las máquinas virtuales de Azure tienen asignada la unidad D para su uso como almacenamiento temporal. Esta asignación de unidad hace que todas las demás asignaciones de unidad de almacenamiento asociadas aumenten en una letra. Para evitar esta asignación automática y para asegurarse de que Azure asigna la siguiente letra de unidad libre a su volumen temporal, establezca la directiva de red de área de almacenamiento (SAN) en Todos en línea:

      Para configurar manualmente este valor:

      - En el servidor local, abra el símbolo del sistema con privilegios elevados y escriba **diskpart**.

        ![Configuración manual](./media/concepts-prepare-vmware-agentless-migration/command-prompt-diskpart.png)

      - Escriba SAN. Si no se mantiene la letra de unidad del sistema operativo invitado, se devuelven Offline All u Offline Shared.

      - En la petición de DISKPART, escriba SAN Policy=OnlineAll. Esta configuración garantiza que los discos se ponen en línea y que se puede leer y escribir en ambos discos.

        ![Directiva en línea de diskpart en el símbolo del sistema de administrador](./media/concepts-prepare-vmware-agentless-migration/diskpart-online-policy.png)

1. **Establecimiento del tipo de inicio DHCP**

   El script de preparación también establecerá el tipo de inicio del servicio DHCP como Automático. Esto permitirá que la máquina virtual migrada obtenga una dirección IP y establezca una conectividad posterior a la migración. Asegúrese de que el servicio DHCP esté configurado y de que el estado es "En ejecución".

    ![Establecer tipo de inicio DHCP](./media/concepts-prepare-vmware-agentless-migration/get-service-dhcp.png)

   Para editar manualmente la configuración de inicio DHCP, ejecute el ejemplo siguiente en Windows PowerShell:

   ```
   Get-Service -Name Dhcp
   Where-Object StartType -ne Automatic
   Set-Service -StartupType Automatic
   ```

1. **Deshabilitación de VMware Tools**

   Haga que el tipo de inicio del servicio "VMware Tools" se deshabilite si existe, ya que no es necesario para la máquina virtual en Azure.

   >[!NOTE]                        
   >Para conectarse a las máquinas virtuales de Windows Server 2003, se deben instalar los servicios de integración de Hyper-V en la máquina virtual de Azure. Las máquinas con Windows Server 2003 no lo tienen instalado de forma predeterminada. Consulte este [artículo](./prepare-windows-server-2003-migration.md) para instalar y preparar la migración.

1. **Instalación del agente invitado de Windows Azure**

    Azure Migrate intentará instalar el agente de máquina virtual de Microsoft Azure, el cual es un proceso ligero y seguro que administra la interacción de la máquina virtual con el controlador de tejido de Azure. El agente de máquina virtual tiene un rol principal en la habilitación y ejecución de extensiones de máquina virtual de Azure que permiten una configuración posterior a la implementación de la máquina virtual como, por ejemplo, la instalación y configuración del software. Azure Migrate instala automáticamente el agente de máquina virtual Windows en Windows Server 2008 R2 y versiones posteriores.

    El agente de máquina virtual de Windows puede instalarse manualmente con un paquete de Windows Installer. Para instalar manualmente el agente de máquina virtual de Windows, [descargue el instalador del agente de máquina virtual](https://go.microsoft.com/fwlink/?LinkID=394789). También puede buscar una versión específica en las [versiones del agente de máquina virtual IaaS de Windows en GitHub](https://github.com/Azure/WindowsVMAgent/releases). El Agente de máquina virtual solo se admite en Windows Server 2008 (64 bits) y posterior.

    Para comprobar si el agente de máquina virtual de Azure se ha instalado correctamente, abra el Administrador de tareas, seleccione la pestaña **Detalles** y busque el nombre de proceso *WindowsAzureGuestAgent.exe*. La presencia de este proceso indica que el agente de VM está instalado. También puede usar [PowerShell para detectar el agente de máquina virtual.](../virtual-machines/extensions/agent-windows.md#powershell)

    ![Instalación correcta del agente de máquina virtual de Azure](./media/concepts-prepare-vmware-agentless-migration/installation-azure-vm-agent.png)

    Una vez realizados los cambios mencionados anteriormente, se descargará la partición del sistema. La máquina virtual ya está lista para la migración.
    [Más información sobre los cambios de los servidores Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="changes-performed-on-linux-servers"></a>Cambios realizados en servidores Linux

1. **Detección y montaje de particiones del sistema operativo Linux**  

   Antes de realizar los cambios de configuración pertinentes, el script de preparación validará si se seleccionó el disco del sistema operativo correcto para la migración. El script recopilará la información sobre todas las particiones, sus UUID y puntos de montaje. El script examinará todas estas particiones visibles para buscar las particiones /boot y /root.

   Se llevan a cabo las acciones siguientes en este paso:

   - Detección de la partición /root:
     - Monte cada partición visible y busque etc/fstab.
      - Si no se encuentran los archivos fstab, la partición se desmonta y la búsqueda continúa para la partición correcta.
      - Si se encuentran los archivos fstab, lea el contenido de fstab para identificar el dispositivo raíz y montarlo como punto de montaje base.
   - Detección de la partición /boot y otras particiones del sistema:
     - Use el contenido de fstab para determinar si /boot es una partición independiente. Si lo es, obtenga el nombre de dispositivo de la partición /boot del contenido de fstab o busque la partición, la cual tendrá la marca /boot.
     - El script procederá a detectar y montar /boot y otras particiones necesarias en "/mnt/azure_sms_root" para crear el árbol raíz del sistema de archivos necesario para la celda chroot. Otras particiones necesarias son: /boot/grub/menu.lst, /boot/grub/grub.conf, /boot/grub2/grub.cfg, /boot/grub/grub.cfg, /boot/efi (para el arranque UEFI), /var, /lib, /etc, /usr, etc.

1. **Detección de la versión del sistema operativo**

   Una vez detectada la partición raíz, el script usará los archivos siguientes para determinar la distribución y la versión del sistema operativo Linux.

   - RHEL/CentOS: etc/redhat-release
   - OL: etc/oracle-release
   - SLES: etc/SuSE-release
   - Ubuntu: etc/lsb-release
   - Debian: etc/debian_version

1. **Instalación de los servicios de integración de Linux de Hyper-V y regeneración de la imagen de kernel**  

   El siguiente paso consiste en inspeccionar la imagen del kernel y recompilar la imagen de inicialización de Linux para que contenga los controladores de Hyper-V necesarios (**hv_vmbus, hv_storvsc, hv_netvsc**) en el disco RAM inicial. La recompilación de la imagen init garantiza que la máquina virtual se iniciará en Azure.

   Azure se ejecuta en el hipervisor de Hyper-V. Por eso, Linux requiere que ciertos módulos del kernel se ejecuten en Azure. Para preparar la imagen de Linux, es posible que necesite recompilar el initrd para que al menos los módulos kernel hv_vmbus y hv_storvsc estén disponibles en el disco RAM inicial. El mecanismo para volver a generar la imagen initrd o initramfs puede variar dependiendo de la distribución. Consulte la documentación de distribución o el soporte para conocer el procedimiento adecuado. Este es un ejemplo de cómo recompilar initrd mediante la utilidad mkinitrd:

   1. Busque la lista de kernels instalados en el sistema (/lib/modules)
   1. Para cada módulo, inspeccione si los controladores de Hyper-V ya están incluidos.
   1. Si falta alguno de estos controladores, agregue los controladores necesarios y vuelva a generar la imagen para la versión del kernel correspondiente.

      >[!NOTE]
      >Es posible que este paso no se aplique a las máquinas virtuales de Ubuntu y Debian, ya que los controladores de Hyper-V están integrados de forma predeterminada. [Más información acerca de los cambios.](../virtual-machines/linux/create-upload-generic.md#installing-kernel-modules-without-hyper-v)

      Un ejemplo ilustrativo de recompilación de initrd

      - Haga una copia de seguridad de la imagen initrd existente

        ```
        cd /boot
        sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
        ```

      - Recompile el initrd con los módulos kernel hv_vmbus y hv_storvsc:

        ```
          sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
        ```
   La mayoría de las nuevas versiones de las distribuciones de Linux lo incluyen de forma predeterminada. Si no se ha incluido, instálelo manualmente para todas las versiones, excepto las mencionadas anteriormente, mediante los pasos que se indican más arriba.

1. **Habilitar el registro de la consola serie de Azure**

   A continuación, el script realizará cambios para habilitar el registro de la consola serie de Azure. La habilitación del registro de la consola ayuda a solucionar problemas en la máquina virtual de Azure. Obtenga más información sobre la consola serie de Azure para Linux en [Consola serie de Azure para Linux: Virtual Machines | Microsoft Docs](/troubleshoot/azure/virtual-machines/serial-console-linux).

   Modifique la línea de arranque de kernel en GRUB o GRUB2 para incluir los siguientes parámetros, de modo que todos los mensajes de la consola se envíen al primer puerto serie. Estos mensajes pueden ayudar al soporte técnico de Azure con la depuración de problemas.

   ```
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
   ```

   También se recomienda quitar los parámetros siguientes, si existen.

   ```
   rhgb quiet crashkernel=auto
   ```
    [Consulte este artículo](../virtual-machines/linux/create-upload-generic.md#general-linux-system-requirements) para más información sobre cambios específicos.

1. **Cambios de red para la conectividad**

   Según la versión del sistema operativo, el script realizará los cambios de red necesarios para la conectividad con la máquina virtual migrada. Los cambios son:

   1. Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Azure.

      Ejemplo ilustrativo de servidores RedHat

      ```console
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
      ```

   1. Elimine Network Manager si es necesario. Network Manager puede interferir con el agente Linux de Azure en algunas versiones del sistema operativo. Se recomienda realizar estos cambios en los servidores que ejecutan distribuciones de RedHat y Ubuntu.

   1. Desinstale el paquete ejecutando el comando siguiente:
    
      Ejemplo ilustrativo de servidores RedHat

      ```console
         # sudo rpm -e --nodeps NetworkManager
      ```

   1. Haga una copia de seguridad de la configuración de NIC existente y cree el archivo de configuración de NIC eth0 con la configuración de DHCP. Para ello, el script creará o editará el archivo /etc/sysconfig/network-scripts/ifcfg-eth0 y agregará el texto siguiente:

      Ejemplo ilustrativo de servidores RedHat

      ```config
         DEVICE=eth0
         ONBOOT=yes
         BOOTPROTO=dhcp
         TYPE=Ethernet
         USERCTL=no
         PEERDNS=yes
         IPV6INIT=no
         PERSISTENT_DHCLIENT=yes
         NM_CONTROLLED=yes
      ```

   1. Restablezca el archivo etc/sysconfig/network como se muestra a continuación:

      Ejemplo ilustrativo de servidores RedHat

      ```config
         NETWORKING=yes
         HOSTNAME=localhost.localdomain
      ```

1. **Validación de Fstab**

    Azure Migrate validará las entradas del archivo fstab y reemplazará las entradas fstab por identificadores de volumen persistentes o UUID siempre que sea necesario. Esto garantiza que el nombre de la unidad o partición permanece constante independientemente del sistema al que esté conectado.

   - Si el nombre del dispositivo es un nombre de dispositivo estándar (por ejemplo, /dev/sdb1):
     - Si se trata de una partición raíz o de arranque, se reemplaza por el UUID.
     - Si la partición coexiste con la partición raíz o de arranque como particiones estándar en el mismo disco, se reemplaza por el UUID.
   - Si el nombre del dispositivo es UUID/LABEL/LV, no se realizará ningún cambio.
   - Si se trata de un dispositivo de red (nfs, cifs, smbfs, etc.), el script comentará la entrada. Para acceder a ella, puede quitar la marca de comentario y reiniciar la máquina virtual de Azure.

1. **Instalará el agente invitado de Linux de Azure**

    Azure Migrate intentará instalar el agente Linux de Microsoft Azure (waagent), el cual es un proceso ligero y seguro que administra el aprovisionamiento de Linux y FreeBSD, y la interacción de máquina virtual con el controlador de tejido de Azure.  [Obtenga más información](../virtual-machines/extensions/agent-linux.md) sobre la funcionalidad habilitada para las implementaciones de IaaS de Linux y FreeBSD a través del agente Linux.

    Revise la lista de [paquetes necesarios](../virtual-machines/extensions/agent-linux.md#requirements) para instalar el agente de máquina virtual Linux. Azure Migrate instala el agente de máquina virtual Linux automáticamente para RHEL6, RHEL7, CentOS7 (6 debe ser compatible como RHEL), Ubuntu 14.04, Ubuntu 16.04, Ubuntu 18.04, Ubuntu 19.04, Ubuntu 19.10 y Ubuntu 20.04 cuando se usa el método sin agente para la migración de VMware. Siga estas instrucciones para [instalar el agente de Linux manualmente](../virtual-machines/extensions/agent-linux.md#installation) para otras versiones del sistema operativo.

    Puede usar el comando para comprobar el estado del servicio del agente Linux de Azure para asegurarse de que se está ejecutando. El nombre del servicio podría ser **walinuxagent** o **waagent**.
    Una vez realizados los cambios de hidratación, el script desmontará todas las particiones montadas, desactivará los grupos de volúmenes y, a continuación, vaciará los dispositivos.

   ```
    $ vgchange -an <vg-name>
    $ blockdev –flushbufs <disk-device-name>
   ```

   [Obtenga más información sobre los cambios de los servidores Linux.](../virtual-machines/linux/create-upload-generic.md)

### <a name="clean-up-the-temporary-vm"></a>Limpieza de la máquina virtual temporal

Después de realizar los cambios necesarios, Azure Migrate retirará la máquina virtual temporal y liberará los discos del sistema operativo conectados (y los discos de datos). Esto marca el final del *proceso de hidratación*.     

Después de esto, se clonan el disco del sistema operativo modificado y los discos de datos que contienen los datos replicados. Se crea una nueva máquina virtual en la región de destino, la red virtual y la subred, y los discos clonados se conectan a la máquina virtual. Esto marca la finalización del proceso de migración.

## <a name="learn-more"></a>Más información

- [Preparación de las máquinas locales para la migración a Azure.](./prepare-for-migration.md)
