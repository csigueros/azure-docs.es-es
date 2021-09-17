---
title: Cómo cambiar el tamaño de los discos cifrados con Azure Disk Encryption
description: En este artículo se proporcionan instrucciones para cambiar el tamaño de los discos cifrados de ADE mediante la administración de volúmenes lógicos.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: bd23b2381fb042d80297c7b29fad382787ef1281
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697182"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Cambio del tamaño de dispositivos de administración de volúmenes lógicos cifrados que usan Azure Disk Encryption

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

En este artículo, aprenderá a cambiar el tamaño de los discos de datos que usan Azure Disk Encryption. Para cambiar el tamaño de los discos, usará la administración de volúmenes lógicos (LVM) en Linux. Los pasos se aplican a varios escenarios.

Puede usar este proceso de cambio de tamaño en los entornos siguientes:

- Distribuciones de Linux:
    - Red Hat Enterprise Linux (RHEL) 7 o posterior
    - Ubuntu 16 o posterior
    - SUSE 12 o posterior
- Versiones de Azure Disk Encryption: 
    - Extensión de paso único
    - Extensión de paso doble

## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que ha:

- Una configuración de LVM existente. Para obtener más información, consulte [Configuración del LVM en una VM Linux](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- Discos que ya estén cifrados mediante Azure Disk Encryption. Para obtener más información, consulte [Configuración de LVM y RAID en dispositivos cifrados](how-to-configure-lvm-raid-on-crypt.md).

- Experiencia en el uso de Linux y LVM.

- Experiencia en el uso de rutas de acceso */dev/disk/scsi1/* para discos de datos en Azure. Para obtener más información, consulte [Solución de problemas de nombres de dispositivo de VM Linux](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Escenarios

Los procedimientos descritos en este artículo se aplican a los escenarios siguientes:

- Configuraciones de LVM tradicional y LVM-on-crypt
- Cifrado de LVM tradicional 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Configuraciones de LVM tradicional y LVM-on-crypt

Las configuraciones de LVM tradicional y LVM-on-crypt amplían un volumen lógico (LV) cuando el grupo de volúmenes (VG) tiene espacio disponible.

### <a name="traditional-lvm-encryption"></a>Cifrado de LVM tradicional 

En el cifrado de LVM tradicional, se cifran los LV. No se cifra el disco completo.

Mediante el cifrado de LVM tradicional, se puede:

- Ampliar el LV al agregar un nuevo volumen físico (PV).
- Ampliar el LV al cambiar el tamaño de un PV existente.

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

El método recomendado para el cifrado de disco es LVM-on-crypt. Con este método se cifra todo el disco, no solo el LV.

Mediante LVM-on-crypt, se puede: 

- Ampliar el LV al agregar un nuevo PV.
- Ampliar el LV al cambiar el tamaño de un PV existente.

> [!NOTE]
> No se recomienda combinar el cifrado de LVM tradicional y de LVM-on-crypt en la misma VM.

En las secciones siguientes se proporcionan ejemplos de cómo usar LVM y LVM-on-crypt. En los ejemplos se usan valores preexistentes para discos, PV, VG, LV, sistemas de archivos, identificadores únicos universales (UUID) y puntos de montaje. Reemplace estos valores por sus propios valores para que se adapten a su entorno.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Ampliación de un LV cuando el VG tiene espacio disponible

La manera tradicional de cambiar el tamaño de los volúmenes lógicos (LV) es ampliar un LV cuando el grupo de volúmenes (VG) tiene espacio disponible. Puede usar este método para discos sin cifrar, volúmenes cifrados con LVM tradicional y configuraciones de LVM-on-crypt.

1. Compruebe el tamaño actual del sistema de archivos que quiere aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del sistema de archivos. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Compruebe que el VG tiene suficiente espacio para aumentar el LV:

    ``` bash
    vgs
    ```

    ![Captura de pantalla que muestra el código que comprueba el espacio en el VG. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    También puede usar `vgdisplay`:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de pantalla que muestra el código de vgdisplay que comprueba el espacio en el VG. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identifique qué LV tiene que cambiar de tamaño:

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra el resultado del comando lsblk. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    En el caso de LVM-on-crypt, la diferencia es que este resultado muestra que la capa cifrada se encuentra en el nivel de disco.

    ![Captura de pantalla que muestra el resultado del comando lsblk. El resultado está resaltado. Muestra la capa cifrada.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Compruebe el tamaño del LV:

    ``` bash
    lvdisplay lvname
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del volumen lógico. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumente el tamaño de LV mediante `-r` para cambiar el tamaño del sistema de archivos en línea:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que aumenta el tamaño del volumen lógico. El comando y los resultados están resaltados.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Compruebe los nuevos tamaños del LV y del sistema de archivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del LV y del sistema de archivos. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    El resultado del tamaño indica que el tamaño del LV y del sistema de archivos se cambió correctamente.

Puede volver a comprobar la información del LV para confirmar los cambios en el nivel de LV:

``` bash
lvdisplay lvname
```

![Captura de pantalla que muestra el código que confirma los nuevos tamaños. Los tamaños están resaltados.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Ampliación de un volumen de LVM tradicional al agregar un nuevo PV

Cuando tenga que agregar un nuevo disco para aumentar el tamaño del VG, amplíe el volumen de LVM tradicional agregando un nuevo PV.

1. Compruebe el tamaño actual del sistema de archivos que quiere aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño actual de un sistema de archivos. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Compruebe la configuración actual del PV:

    ``` bash
    pvs
    ```

    ![Captura de pantalla que muestra el código que comprueba la configuración actual de un PV. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Compruebe la información actual del VG:

    ``` bash
    vgs
    ```

    ![Captura de pantalla que muestra el código que comprueba la información actual del grupo de volúmenes. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Compruebe la lista de discos actual. Para identificar los discos de datos, compruebe los dispositivos en */dev/disk/azure/scsi1/* .

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Captura de pantalla que muestra el código que comprueba la lista de discos actual. El comando y los resultados están resaltados.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Compruebe el resultado de `lsblk`: 

    ``` bash
    lsbk
    ```

    ![Captura de pantalla que muestra el código que comprueba el resultado de lsblk. El comando y los resultados están resaltados.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Conecte el nuevo disco a la VM según las instrucciones de [Conexión de un disco de datos a una VM Linux](attach-disk-portal.md).

7. Compruebe la lista de discos y observe el nuevo disco.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Captura de pantalla que muestra el código que comprueba la lista de discos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Captura de pantalla que muestra el código que comprueba la lista de discos mediante lsblk. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Cree un nuevo PV además del nuevo disco de datos:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Captura de pantalla que muestra el código que crea un nuevo PV. El resultado está resaltado.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Este método usa el disco completo como PV sin ninguna partición. Como alternativa, puede usar `fdisk` para crear una partición y, a continuación, utilizar esa partición para `pvcreate`.

9. Compruebe que el PV se ha agregado a la lista de PV:

    ``` bash
    pvs
    ```

    ![Captura de pantalla que muestra el código que muestra la lista de volúmenes físicos. El resultado está resaltado.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Amplíe el VG al agregarle el nuevo PV:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Captura de pantalla que muestra el código que amplía el grupo de volúmenes. El resultado está resaltado.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Compruebe el nuevo tamaño del VG:

    ``` bash
    vgs
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del grupo de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Use `lsblk` para identificar el LV cuyo tamaño debe cambiar:

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra el código que identifica el volumen local cuyo tamaño debe cambiarse. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Amplíe el tamaño del LV mediante `-r` para aumentar el sistema de archivos en línea:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que aumenta el tamaño del sistema de archivos en línea. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Compruebe los nuevos tamaños del LV y del sistema de archivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba los tamaños del volumen local y del sistema de archivos. El comando y el resultado están resaltados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Cuando se usa el cifrado de datos de Azure en configuraciones de LVM tradicionales, la capa cifrada se crea en el nivel de LV, no en el nivel de disco.
    >
    >En este punto, la capa cifrada se amplía al nuevo disco. El disco de datos real no tiene ninguna configuración de cifrado en el nivel de plataforma, por lo que no se actualiza el estado de cifrado.
    >
    >Estos son algunos de los motivos por los que LVM-on-crypt es el enfoque recomendado. 

15. Compruebe la información de cifrado desde el portal:

    ![Captura de pantalla que muestra la información de cifrado en el portal. El nombre del disco y el cifrado están resaltados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Para actualizar la configuración de cifrado en el disco, agregue un nuevo LV y habilite la extensión en la VM.
    
16. Agregue un nuevo LV, cree un sistema de archivos en él y agréguelo a `/etc/fstab`.

17. Vuelva a establecer la extensión de cifrado. Esta vez marcará la configuración de cifrado en el nuevo disco de datos en el nivel de plataforma. Este es un ejemplo de la CLI:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Compruebe la información de cifrado desde el portal:

    ![Captura de pantalla que muestra la información de cifrado en el portal. El nombre del disco y la información del cifrado están resaltados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Una vez actualizada la configuración de cifrado, puede eliminar el nuevo LV. Elimine también la entrada de `/etc/fstab` y `/etc/crypttab` que creó.

![Captura de pantalla que muestra el código que elimina el nuevo volumen lógico. Los archivos fstab y crypttab eliminados están resaltados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Siga estos pasos para finalizar la limpieza:

1. Desmonte el LV:

    ``` bash
    umount /mountpoint
    ```

1. Cierre la capa cifrada del volumen:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Elimine el LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Ampliación de un volumen de LVM tradicional al cambiar el tamaño de un PV existente

En algunos escenarios, es posible que las limitaciones le obliguen a cambiar el tamaño de un disco existente. A continuación, se indica cómo puede hacerlo.

1. Identifique los discos cifrados:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Captura de pantalla que muestra el código que identifica los discos cifrados. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Captura de pantalla que muestra el código alternativo que identifica los discos cifrados. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Compruebe la información del PV:

    ``` bash
    pvs
    ```

    ![Captura de pantalla que muestra el código que comprueba la información sobre el volumen físico. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Los resultados de la imagen muestran que actualmente se está usando todo el espacio de los PV.

3. Compruebe la información del VG:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Captura de pantalla que muestra el código que comprueba la información sobre el grupo de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Compruebe los tamaños de disco. Puede usar `fdisk` o `lsblk` para enumerar los tamaños de unidad.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Captura de pantalla que muestra el código que comprueba los tamaños de disco. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Aquí hemos identificado cuáles PV están asociados con cuáles LV mediante `lsblk -fs`. Para identificar las asociaciones, puede ejecutar `lvdisplay`.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Captura de pantalla que muestra una manera alternativa de identificar las asociaciones de volúmenes físicos con volúmenes locales. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    En este caso, las cuatro unidades de datos forman parte del mismo VG y un único LV. Su configuración puede ser diferente.

5. Compruebe el uso del sistema de archivos actual:

    ``` bash
    df -h /datalvm*
    ```

    ![Captura de pantalla que muestra el código que comprueba el uso del sistema de archivos. El comando y los resultados están resaltados.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Cambie el tamaño de los discos de datos según las instrucciones de [Expansión de un disco administrado de Azure](expand-disks.md#expand-an-azure-managed-disk). Puede usar el portal, la CLI o PowerShell.

    >[!IMPORTANT]
    >No se puede cambiar el tamaño de los discos virtuales mientras la VM está en ejecución. Desasigne la VM para este paso.

7. Inicie la VM y compruebe los nuevos tamaños mediante `fdisk`.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del disco. El resultado está resaltado.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    En este caso, se cambió el tamaño de `/dev/sdd` de 5 GB a 20 GB.

8. Compruebe el tamaño actual del PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del PV. El resultado está resaltado.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Aunque se cambió el tamaño del disco, el PV todavía tiene el tamaño anterior.

9. Cambie el tamaño del PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Captura de pantalla que muestra el código que cambia el tamaño del volumen físico. El resultado está resaltado.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Compruebe el tamaño del PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del volumen físico. El resultado está resaltado.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Aplique el mismo procedimiento para todos los discos cuyo tamaño quiere cambiar.

11. Compruebe la información del VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de pantalla que muestra el código que comprueba la información del grupo de volúmenes. El resultado está resaltado.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    El VG ahora tiene espacio suficiente para asignarlo a los LV.

12. Cambie el tamaño del LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Captura de pantalla que muestra el código que cambia el tamaño del LV. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Compruebe el tamaño del sistema de archivos:

    ``` bash
    df -h /datalvm2
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del sistema de archivos. El resultado está resaltado.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Ampliación de un volumen LVM-on-crypt al agregar un nuevo PV

También puede ampliar un volumen LVM-on-crypt agregando un nuevo PV. Este método sigue estrechamente los pasos descritos en [Configuración de LVM y RAID en dispositivos cifrados](how-to-configure-lvm-raid-on-crypt.md#general-steps). Consulte las secciones en las que se explica cómo agregar un disco nuevo y establecerlo en una configuración LVM-on-crypt.

Puede usar este método para agregar espacio a un LV existente. O bien, puede crear nuevos VG o LV.

1. Compruebe el tamaño actual del VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del grupo de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Compruebe el tamaño del sistema de archivos y del LV que quiere ampliar:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del volumen local. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del sistema de archivos. El resultado está resaltado.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Agregue un nuevo disco de datos a la VM e identifíquelo.

    Antes de agregar el nuevo disco, compruebe los discos:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño de los discos. El resultado está resaltado.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Esta es otra manera de comprobar los discos antes de agregar el nuevo disco:

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra un código alternativo que comprueba el tamaño de los discos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Para agregar el nuevo disco, puede usar PowerShell, la CLI de Azure o Azure Portal. Para obtener más información, consulte [Conexión de un disco de datos a una VM Linux](attach-disk-portal.md).

    El esquema de nombres del kernel se aplica al dispositivo recién agregado. Normalmente, a una nueva unidad se le asigna la siguiente letra disponible. En este caso, el disco agregado es `sdd`.

4. Compruebe los discos para asegurarse de que se ha agregado el nuevo disco:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Captura de pantalla que muestra el código que enumera los discos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra el disco recién agregado en el resultado.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Cree un sistema de archivos sobre el disco recién agregado. Asocie el disco con los dispositivos vinculados en `/dev/disk/azure/scsi1/`.

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Captura de pantalla que muestra el código que crea un sistema de archivos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Captura de pantalla que muestra código adicional que crea un sistema de archivos y asocia el disco con los dispositivos vinculados. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Cree un punto de montaje temporal para el nuevo disco agregado:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Agregue el sistema de archivos recién creado a `/etc/fstab`.

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Monte el sistema de archivos recién creado:

    ``` bash
    mount -a
    ```

9. Verifique que el nuevo sistema de archivos se ha montado:

    ``` bash
    df -h
    ```

    ![Captura de pantalla que muestra el código que comprueba que el sistema de archivos se ha montado. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra código adicional que comprueba que el sistema de archivos se ha montado. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Reinicie el cifrado que inició anteriormente para las unidades de datos.

    >[!TIP]
    >Para LVM-on-crypt, se recomienda usar `EncryptFormatAll`. De lo contrario, es posible que vea un cifrado doble mientras configura discos adicionales.
    >
    >Para obtener más información, consulte [Configuración de LVM y RAID en dispositivos cifrados](how-to-configure-lvm-raid-on-crypt.md).

    Este es un ejemplo:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Cuando finalice el cifrado, verá una capa de cifrado en el disco recién agregado:

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra el código que comprueba la capa de cifrado. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Desmonte la capa cifrada del nuevo disco:

    ``` bash
    umount ${newmount}
    ```

12. Compruebe la información actual del PV:

    ``` bash
    pvs
    ```

    ![Captura de pantalla que muestra el código que comprueba la información sobre el volumen físico. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Cree un PV sobre la capa cifrada del disco. Tome el nombre del dispositivo del comando `lsblk` anterior. Agregue un asignador `/dev/` delante del nombre del dispositivo para crear el PV:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Captura de pantalla que muestra el código que crea un volumen físico en la capa cifrada. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Verá una advertencia sobre borrar la firma `ext4 fs` actual. Se espera esta advertencia. Responda a esta pregunta con `y`.

14. Compruebe que el nuevo PV se ha agregado a la configuración de LVM:

    ``` bash
    pvs
    ```

    ![Captura de pantalla que muestra el código que comprueba que el volumen físico se ha agregado a la configuración de LVM. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Agregue el nuevo PV al VG que necesita aumentar.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Captura de pantalla que muestra el código que agrega un volumen físico a un grupo de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Compruebe el nuevo tamaño y el espacio disponible del VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño y el espacio disponible del grupo de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Observe el aumento del recuento de `Total PE` y `Free PE / Size`.

17. Aumente el tamaño del LV y del sistema de archivos. Usa la opción `-r` en `lvextend`. En este ejemplo, vamos a agregar el espacio total disponible en el VG al LV dado.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que aumenta el tamaño del volumen local y del sistema de archivos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Siga los pasos a continuación para comprobar los cambios.

1. Compruebe el tamaño del LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que comprueba el nuevo tamaño del volumen local. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Compruebe el nuevo tamaño del sistema de archivos:

    ``` bash
    df -h mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el nuevo tamaño del sistema de archivos. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Compruebe que la capa de LVM está sobre la capa cifrada:

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra el código que comprueba que la capa de LVM está sobre la capa cifrada. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Si usa `lsblk` sin opciones, verá los puntos de montaje varias veces. El comando ordena por dispositivo y LV. 

    Podría interesarle usar `lsblk -fs`. En este comando, `-fs` invierte el criterio de ordenación para que los puntos de montaje se muestren una vez. Los discos se muestran varias veces.

    ``` bash
    lsblk -fs
    ```

    ![Captura de pantalla que muestra código alternativo que comprueba que la capa de LVM está sobre la capa cifrada. El resultado está resaltado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Ampliación de un LVM en un volumen de cifrado al cambiar el tamaño de un PV existente

1. Identifique los discos cifrados:

    ``` bash
    lsblk
    ```

    ![Captura de pantalla que muestra el código que identifica los discos cifrados. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Captura de pantalla que muestra el código alternativo que identifica los discos cifrados. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Compruebe la información del PV:

    ``` bash
    pvs
    ```

    ![Captura de pantalla que muestra el código que comprueba la información de los volúmenes físicos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Compruebe la información del VG:

    ``` bash
    vgs
    ```

    ![Captura de pantalla que muestra el código que comprueba la información de los grupos de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Compruebe la información del LV:

    ``` bash
    lvs
    ```

    ![Captura de pantalla que muestra el código que comprueba la información del volumen local. El resultado está resaltado.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Compruebe el uso del sistema de archivos:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Captura de pantalla que muestra el código que comprueba cuánto se usa del sistema de archivos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Compruebe los tamaños de los discos:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño de los discos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Cambie el tamaño del disco de datos. Puede usar el portal, la CLI o PowerShell. Para obtener más información, consulte la sección sobre el cambio de tamaño de discos en [Expansión de discos duros virtuales en una VM Linux](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >No se puede cambiar el tamaño de los discos virtuales mientras la VM está en ejecución. Desasigne la VM para este paso.

8. Compruebe los tamaños de los discos:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Captura de pantalla que muestra el código que comprueba los tamaños de los discos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    En este caso, el tamaño de ambos discos se cambió de 2 GB a 4 GB. Pero el tamaño del sistema de archivos, el LV y el PV sigue siendo el mismo.

9. Compruebe el tamaño actual del PV. Recuerde que en LVM-on-crypt, el PV es el dispositivo `/dev/mapper/`, no el dispositivo `/dev/sd*`.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del volumen físico actual. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Cambie el tamaño del PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Captura de pantalla que muestra el código que cambia el tamaño del volumen físico. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Compruebe el nuevo tamaño del PV:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Captura de pantalla que muestra el código que comprueba el tamaño del volumen físico. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Cambie el tamaño de la capa cifrada en el PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Aplique el mismo procedimiento para todos los discos cuyo tamaño quiere cambiar.

13. Compruebe la información del VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de pantalla que muestra el código que comprueba la información del grupo de volúmenes. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    El VG ahora tiene espacio suficiente para asignarlo a los LV.

14. Compruebe la información del LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que comprueba la información del volumen local. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Compruebe el uso del sistema de archivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el uso del sistema de archivos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Cambie el tamaño del LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que cambia el tamaño del volumen local. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Aquí usamos la opción `-r` para cambiar también el tamaño del sistema de archivos.

17. Compruebe la información del LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Captura de pantalla que muestra el código que obtiene la información sobre el volumen local. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Compruebe el uso del sistema de archivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de pantalla que muestra el código que comprueba el uso del sistema de archivos. Los resultados están resaltados.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Aplique el mismo procedimiento de cambio de tamaño a cualquier otro LV que lo requiera.

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de Azure Disk Encryption](disk-encryption-troubleshooting.md)