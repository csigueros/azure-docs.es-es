---
title: Creación de imágenes de máquina virtual personalizadas para el dispositivo GPU de Azure Stack Edge Pro
description: Se describe cómo crear imágenes de máquina virtual Windows y Linux personalizadas para implementar máquinas virtuales en dispositivos GPU de Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 94ffb38c71437c8f5902866620b5ac0c2467edfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462921"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-gpu-device"></a>Creación de imágenes de máquina virtual personalizadas para el dispositivo GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para implementar máquinas virtuales en el dispositivo GPU de Azure Stack Edge Pro, debe ser capaz de crear imágenes de máquina virtual personalizadas que pueda usar para crear máquinas virtuales en Azure. En este artículo se describen los pasos para crear imágenes de máquina virtual personalizadas en Azure para máquinas virtuales Windows y Linux y descargar o copiar esas imágenes en una cuenta de Azure Storage.

Para preparar una imagen de máquina virtual personalizada, es necesario seguir un flujo de trabajo. Como origen de la imagen, debe usar un VHD fijo de una máquina virtual Gen1 de cualquier tamaño compatible con Azure. Para ver las opciones de tamaño de máquina virtual, consulte [Tamaños de máquina virtual admitidos](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

## <a name="prerequisites"></a>Prerrequisitos

Antes de crear la imagen de máquina virtual, complete el siguiente requisito previo:

- [Descargue AzCopy](../storage/common/storage-use-azcopy-v10.md#download-azcopy). AzCopy le ofrece una manera rápida de copiar un disco del sistema operativo en una cuenta de Azure Storage.

---

## <a name="create-a-custom-vm-image"></a>Crear una imagen de máquina virtual personalizada

Los pasos para preparar una imagen de máquina virtual personalizada varían según si se trata de una máquina virtual Windows o Linux.


### <a name="windows"></a>[Windows](#tab/windows)

Realice los pasos siguientes para crear una imagen de máquina virtual Windows:

1. Cree una máquina virtual Windows en Azure. Para ver cómo hacerlo en el portal, consulte [Creación de una máquina virtual Windows en Azure Portal](../virtual-machines/windows/quick-create-portal.md). Para ver cómo hacerlo en PowerShell, consulte [Tutorial: Creación y administración de máquinas virtuales Windows con Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md).  

   La máquina virtual solo puede ser de primera generación. El disco del sistema operativo que use para crear la imagen de máquina virtual debe ser un VHD de un tamaño fijo cualquiera compatible con Azure. Para ver las opciones de tamaño de máquina virtual, consulte [Tamaños de máquina virtual admitidos](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

   Puede usar cualquier máquina virtual de Windows Gen1 con un VHD de tamaño fijo en Azure Marketplace. Para obtener una lista de imágenes de Azure Marketplace que pueden servir, consulte la sección sobre [imágenes de Azure Marketplace de uso común para Azure Stack Edge](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images).

2. Generalice la máquina virtual. Para generalizar la máquina virtual, [conéctese a la máquina virtual](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm), abra un símbolo del sistema y ejecute el siguiente comando `sysprep`:

   ```dos
   c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
   ```

   > [!IMPORTANT]
   > Una vez completado el comando, la máquina virtual se apagará. **No reinicie la máquina virtual.** Al reiniciar la máquina virtual se dañará el disco que acaba de preparar.


### <a name="linux"></a>[Linux](#tab/linux)

Realice los pasos siguientes para crear una imagen de máquina virtual Linux:

1. Cree una máquina virtual Linux en Azure. Para hacerlo en portal, consulte [Inicio rápido: Creación de una máquina virtual Linux en Azure Portal](../virtual-machines/linux/quick-create-portal.md).  Para hacerlo en PowerShell, consulte [Inicio rápido: Creación de una máquina virtual Linux en Azure con PowerShell](../virtual-machines/linux/quick-create-powershell.md).

   Puede usar cualquier máquina virtual Gen1 con un VHD de tamaño fijo de Azure Marketplace para crear imágenes personalizadas de Linux, a excepción de las imágenes de Red Hat Enterprise Linux (RHEL), que requieren pasos adicionales. Para obtener una lista de imágenes de Azure Marketplace que pueden servir, consulte la sección sobre [imágenes de Azure Marketplace de uso común para Azure Stack Edge](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images). Para información sobre las imágenes de RHEL, consulte [Uso de imágenes BYOS de RHEL](#using-rhel-byos-images) a continuación.

1. Desaprovisione la máquina virtual. Use el agente de máquina virtual de Azure para eliminar archivos y datos específicos de la máquina. Use el comando `waagent` con el parámetro `-deprovision+user` en la máquina virtual Linux de origen. Para obtener más información, consulte [Información y uso del agente de Linux de Azure](../virtual-machines/extensions/agent-linux.md).

    1. [Conéctese a la máquina virtual Linux con un cliente de SSH](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm).
    2. En la ventana de SSH, escriba el siguiente comando:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. Este comando no garantiza que se haya borrado toda información confidencial de la imagen o que sea adecuada para su redistribución. El parámetro `+user` también elimina la última cuenta de usuario aprovisionada. Para mantener las credenciales de la cuenta de usuario en la máquina virtual, use solo `-deprovision`.
     
    3. Escriba **s** para continuar. Puede agregar el parámetro `-force` para evitar este paso de confirmación.
    4. Una vez finalizado el comando, escriba **exit** para cerrar el cliente de SSH.  La máquina virtual seguirá ejecutándose en este momento.


### <a name="using-rhel-byos-images"></a>Uso de imágenes BYOS de RHEL

Si usa imágenes de Red Hat Enterprise Linux (RHEL), solo se admiten imágenes Bring Your Own Subscription (BYOS de RHEL) de Red Hat Enterprise Linux, también conocidas como imágenes Red Hat Gold para crear la imagen de máquina virtual. Las imágenes estándar de RHEL de pago por uso de Azure Marketplace no se admiten en Azure Stack Edge.

Para crear una imagen de máquina virtual con la imagen BYOS de RHEL, siga estos pasos:

1. Inicie sesión en [Red Hat Subscription Management](https://access.redhat.com/management) (Administración de suscripciones de Red Hat). Vaya al [panel de Cloud Access](https://access.redhat.com/management/cloud) en la barra de menús superior.
1. Habilite su suscripción a Azure. Consulte las [instrucciones detalladas](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access). La habilitación de la suscripción le permitirá acceder a las imágenes Red Hat Gold.

1. Acepte los términos de uso de Azure (solo una vez por suscripción de Azure y por imagen) y aprovisione una máquina virtual. Consulte las [instrucciones](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access). 

Ahora puede usar la máquina virtual que aprovisionó para crear [una imagen de máquina virtual personalizada](#create-a-custom-vm-image) en Linux.

---

## <a name="download-os-disk-to-storage-account"></a>Descarga del disco del sistema operativo en la cuenta de almacenamiento

Para usar la imagen de máquina virtual personalizada para implementar máquinas virtuales en el dispositivo, debe descargar el disco del sistema operativo en una cuenta de Azure Storage. Se recomienda usar la misma cuenta de almacenamiento que usó para el dispositivo.

Para descargar el disco del sistema operativo de la máquina virtual en una cuenta de almacenamiento de Azure, siga estos pasos:

1. [Detenga la máquina virtual en el portal](../virtual-machines/windows/download-vhd.md#stop-the-vm). Debe hacerlo para desasignar el disco del sistema operativo incluso si la máquina virtual Windows se cerró después de que se ejecutara `sysprep` para generalizarla.

1. [Genere una dirección URL de descarga para el disco del sistema operativo](../virtual-machines/windows/download-vhd.md#generate-download-url) y anote la dirección URL. De forma predeterminada, la dirección URL expira después de 3600 segundos (1 hora). Puede aumentar ese tiempo si es necesario.
      
1. Descargue el VHD en la cuenta de Azure Storage mediante uno de estos métodos:
   
   - Método 1: para una transferencia más rápida, use AzCopy para copiar el VHD en la cuenta de Azure Storage. Para instrucciones, consulte a continuación [Uso de AzCopy para copiar una imagen de máquina virtual en una cuenta de almacenamiento](#copy-vhd-to-storage-account-using-azcopy). 

   - Método 2: si busca un método sencillo con un solo clic, puede seleccionar **Descargar el archivo VHD** al generar una dirección URL de descarga (en el paso 3b) para descargar el disco desde el portal. **Cuando se usa este método, la copia del disco puede tardar bastante tiempo y deberá [cargar el VHD en la cuenta de almacenamiento de Azure](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload) para poder crear máquinas virtuales mediante el portal.**

Ahora puede usar este VHD para crear e implementar máquinas virtuales en el dispositivo GPU de Azure Stack Edge Pro.

## <a name="copy-vhd-to-storage-account-using-azcopy"></a>Copia del VHD en la cuenta de almacenamiento mediante AzCopy

Los procedimientos siguientes describen cómo usar AzCopy para copiar una imagen de máquina virtual personalizada en una cuenta de Azure Storage para que pueda usar la imagen para implementar máquinas virtuales en el dispositivo GPU de Azure Stack Edge Pro. Se recomienda almacenar las imágenes de máquina virtual personalizadas en cualquier cuenta de almacenamiento existente que use, que se encuentre en la misma región o suscripción que Azure Stack Edge.


### <a name="create-target-uri-for-a-container"></a>Creación del URI de destino para un contenedor

AzCopy requiere un *URI de destino* que indique dónde copiar la nueva imagen en la cuenta de almacenamiento. Antes de ejecutar AzCopy, generará una dirección URL de firma de acceso compartido (SAS) para el contenedor de blobs en el que quiere copiar el archivo. Para crear el URI de destino, agregará el nombre de archivo a la dirección URL de SAS.

Para crear el URI de destino para el VHD preparado, siga estos pasos:

1. Genere una dirección URL de SAS para un contenedor en una cuenta de Azure Storage mediante estos pasos:

   1. En Azure Portal, abra la cuenta de almacenamiento y seleccione **Contenedores**. Seleccione el contenedor de blobs que quiere usar, haga clic en él con el botón derecho y elija **Generar SAS**.

      ![Captura de pantalla de la opción Generar SAS para un contenedor de blobs en Azure Portal](./media/azure-stack-edge-gpu-create-virtual-machine-image/storage-container-generate-sas-option.png)

   1. En la pantalla **Generar SAS**, seleccione **Lectura** y **Escritura** en **Permisos**.  

      ![Captura de pantalla de Generar SAS con los permisos de lectura y escritura seleccionados](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-read-and-write-permissions.png)

   1. Seleccione **Generar URL y token de SAS** y, luego, elija **Copiar** para copiar la **dirección URL de SAS de blob**.

      ![Captura de pantalla de Generar SAS, con opciones para generar y copiar una dirección URL de SAS de blob](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-copy-blob-sas-url.png)

1. Para crear el URI de destino para el comando `azcopy`, agregue el nombre de archivo deseado a la dirección URL de SAS.

   Una dirección URL de SAS tiene el formato siguiente: 

   ![Gráfico de una dirección URL de SAS de blob, con la ruta de acceso del contenedor y el lugar para insertar el nuevo nombre de archivo etiquetado](./media/azure-stack-edge-gpu-create-virtual-machine-image/blob-sas-url-format.png)

   Inserte el nombre de archivo, en el formato `/<filename>.vhd`, delante del signo de interrogación que comienza la cadena de consulta. La extensión del nombre de archivo debe ser VHD. 

   Por ejemplo, la siguiente dirección URL de SAS de blob copiará el archivo **osdisk.vhd** en el contenedor de blobs **virtualmachines** en **mystorageaccount**.

   ![Gráfico de un ejemplo de dirección URL de SAS de blob para un VHD denominado osdisk](./media/azure-stack-edge-gpu-create-virtual-machine-image/target-uri-example-based-on-blob-sas-url.png)


### <a name="copy-vhd-to-blob-container"></a>Copia del VHD en el contenedor de blobs

Para copiar el VHD en un contenedor de blobs mediante AzCopy, siga estos pasos:

 1. [Descargue AZCopy](../storage/common/storage-use-azcopy-v10.md#download-azcopy) si aún no lo ha hecho.
 
 1. En PowerShell, vaya al directorio donde guardó azcopy.exe y ejecute el siguiente comando:

    `.\azcopy copy <source URI> <target URI> --recursive`

    donde:
    * `<source URI>` es la dirección URL de descarga que generó anteriormente.
    * `<target URI>` indica en qué contenedor de blobs se va a copiar la nueva imagen en la cuenta de Azure Storage. Para instrucciones, consulte [Uso de AzCopy para copiar una imagen de máquina virtual en una cuenta de almacenamiento](#copy-vhd-to-storage-account-using-azcopy).

    Por ejemplo, el siguiente URI copiará un archivo denominado **windowsosdisk.vhd** en el contenedor de blobs **virtual machines** de la cuenta de almacenamiento **mystorageaccount**:

    ```azcopy 
    .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
    ```

#### <a name="sample-output"></a>Salida de ejemplo

En el ejemplo de comando AzCopy anterior, la salida siguiente indica que se realizó una copia correcta.

   ```output
   PS C:\azcopy\azcopy_windows_amd64_10.10.0> .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
   INFO: Scanning...
   INFO: Failed to create one or more destination container(s). Your transfers may still succeed if the container already exists.
   INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c has started
   Log file is located at: C:\Users\aseuser\.azcopy\783f2177-8317-3e4b-7d2f-697a8f1ab63c.log

   INFO: Destination could not accommodate the tier P10. Going ahead with the default tier. In case of service to service transfer, consider setting the flag --s2s-preserve-access-tier=false.
   100.0 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c summary
   Elapsed Time (Minutes): 1.4671
   Number of File Transfers: 1
   Number of Folder Property Transfers: 0
   Total Number of Transfers: 1
   Number of Transfers Completed: 1
   Number of Transfers Failed: 0
   Number of Transfers Skipped: 0
   TotalBytesTransferred: 136367309312
   Final Job Status: Completed

   PS C:\azcopy\azcopy_windows_amd64_10.10.0>
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de máquinas virtuales en el dispositivo mediante Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Implementación de máquinas virtuales en el dispositivo mediante PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)