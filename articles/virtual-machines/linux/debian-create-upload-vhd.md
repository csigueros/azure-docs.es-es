---
title: Preparación de un disco duro virtual con Debian Linux
description: Aprenda a crear imágenes de VHD de Debian para implementar máquinas virtuales en Azure.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 6/3/2021
ms.author: srijangupta
ms.openlocfilehash: 4e9d7eec14e994d88f4212df4d0e848de1ea6476
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689777"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar VHD en Debian de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

## <a name="prerequisites"></a>Prerequisites
En esta sección, se supone que ya instaló en un sistema operativo Debian Linux desde un archivo .iso que obtuvo del [sitio web Debian](https://www.debian.org/distrib/) y que descargó a un disco duro virtual. Existen varias herramientas para crear archivos .vhd; Hyper-V es solo un ejemplo. Para obtener instrucciones acerca de cómo usar Hyper-V, consulte [Instalación del rol de Hyper-V y configuración de una máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Notas de instalación
* Consulte también las [Notas generales sobre la instalación de Linux](create-upload-generic.md#general-linux-installation-notes) para obtener más consejos sobre la preparación de Linux para Azure.
* el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet **convert-vhd** .
* Al instalar el sistema Linux, se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) o [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) se pueden utilizar en discos de datos si así se prefiere.
* No cree una partición de intercambio en el disco del SO. El agente de Linux de Azure se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información en los pasos que vienen a continuación.
* En Azure, todos los discos duros virtuales deben tener un tamaño virtual alineado con 1 MB. Al convertir un disco sin procesar en un disco duro virtual, tiene que asegurarse de que su tamaño es un múltiplo de 1 MB antes de la conversión. Para obtener más información, consulte las [Notas sobre la instalación de Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Uso de Azure-Manage para crear VHD Debian
Hay herramientas disponibles para generar un VHD de Debian para Azure, como los scripts [azure-manage](https://github.com/credativ/azure-manage) de [Credativ](https://www.credativ.com/). Este es el enfoque recomendado, en lugar de crear una imagen desde el principio. Por ejemplo, para crear un VHD de Debian 8, ejecute los comandos siguientes para descargar la utilidad `azure-manage` (y sus dependencias) y ejecute el script `azure_build_image`:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="prepare-a-debian-image-for-azure"></a>Preparación de una imagen de Debian para Azure

Puede crear la imagen base de nube de Debian para Azure con el [generador de imágenes de nube de FAI](https://salsa.debian.org/cloud-team/debian-cloud-images).

(Los siguientes comandos git clone y apt install se han extraído del repositorio Debian Cloud Images). Empiece por clonar el repositorio e instalar las dependencias:

```
$ git clone https://salsa.debian.org/cloud-team/debian-cloud-images.git
$ sudo apt install --no-install-recommends ca-certificates debsums dosfstools \
    fai-server fai-setup-storage make python3 python3-libcloud python3-marshmallow \
    python3-pytest python3-yaml qemu-utils udev
$ cd ./debian-cloud-images
```

(Opcional) Personalice la compilación agregando scripts (por ejemplo, scripts de shell) a `./config_space/scripts/AZURE`.



## <a name="an-example-of-a-script-to-customize-the-image-is"></a>Un ejemplo de un script para personalizar la imagen es:

```
$ mkdir -p ./config_space/scripts/AZURE
$ cat > ./config_space/scripts/AZURE/10-custom <<EOF
#!/bin/bash

\$ROOTCMD bash -c "echo test > /usr/local/share/testing"
EOF
$ sudo chmod 755 ./config_space/scripts/AZURE/10-custom
```

Tenga en cuenta que es importante preceder los comandos con los que quiera personalizar la imagen con `$ROOTCMD`, ya que esto se conoce como `chroot $target`.


## <a name="build-the-azure-debian-10-image"></a>Cree la imagen de Debian 10 de Azure:

```
$ make image_buster_azure_amd64
```


Como resultado se generarán unos cuantos archivos en el directorio actual, especialmente el archivo de imagen `image_buster_azure_amd64.raw`.

Para convertir la imagen sin formato en VHD para Azure, puede hacer lo siguiente:

```
rawdisk="image_buster_azure_amd64.raw"
vhddisk="image_buster_azure_amd64.vhd"

MB=$((1024*1024))
size=$(qemu-img info -f raw --output json "$rawdisk" | \
gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

rounded_size=$(((($size+$MB-1)/$MB)*$MB))
rounded_size_adjusted=$(($rounded_size + 512))

echo "Rounded Size Adjusted = $rounded_size_adjusted"

sudo qemu-img resize "$rawdisk" $rounded_size
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc "$rawdisk" "$vhddisk"
```


Se crea un archivo VHD `image_buster_azure_amd64.vhd` con un tamaño redondeado para poder copiarlo correctamente en un disco de Azure.

Ahora es necesario crear los recursos de Azure de esta imagen (para ello se usa la variable `$rounded_size_adjusted`, por lo que debe hacerse desde el mismo proceso de shell anterior).

```
az group create -l $LOCATION -n $RG

az disk create \
    -n $DISK \
    -g $RG \
    -l $LOCATION \
    --for-upload --upload-size-bytes "$rounded_size_adjusted" \
    --sku standard_lrs --hyper-v-generation V1

ACCESS=$(az disk grant-access \
    -n $DISK -g $RG \
    --access-level write \
    --duration-in-seconds 86400 \
    --query accessSas -o tsv)

azcopy copy "$vhddisk" "$ACCESS" --blob-type PageBlob

az disk revoke-access -n $DISK -g $RG
az image create \
    -g $RG \
    -n $IMAGE \
    --os-type linux \
    --source $(az disk show \
        -g $RG \
        -n $DISK \
        --query id -o tsv)
az vm create \
    -g $RG \
    -n $VM \
    --ssh-key-value $SSH_KEY_VALUE \
    --public-ip-address-dns-name $VM \
    --image $(az image show \
        -g $RG \
        -n $IMAGE \
        --query id -o tsv)
```


>[!Note]
> Si el ancho de banda desde la máquina local hasta el disco de Azure está haciendo que se tarde mucho tiempo en procesar la carga con azcopy, puede usar un JumpBox de máquina virtual de Azure para acelerar el proceso. Así es cómo se puede hacer:
>
>1. Cree un tarball del VHD en la máquina local: `tar -czvf ./image_buster_azure_amd64.vhd.tar.gz ./image_buster_azure_amd64.vhd`.
>2. Cree una máquina virtual Linux de Azure (la distribución que prefiera). Asegúrese de crearla con un disco lo suficientemente grande como para contener el VHD extraído.
>3. Descargue la utilidad azcopy en la máquina virtual Linux de Azure. Se puede recuperar desde [aquí](../../storage/common/storage-use-azcopy-v10.md#download-azcopy).
>4. Copie el tarball en la máquina virtual: `scp ./image_buster_azure_amd64.vhd.tar.gz <vm>:~`.
>5. En la máquina virtual, extraiga el VHD: `tar -xf ./image_buster_azure_amd64.vhd.tar.gz`; esta operación tardará algo de tiempo según el tamaño del archivo.
>6. Por último, en la máquina virtual, copie el VHD en el disco de Azure `azcopy` (con el comando anterior).
