---
title: Implementación de máquinas virtuales en Azure Stack Edge Pro con GPU mediante Azure Portal
description: Aprenda a crear y administrar máquinas virtuales en el dispositivo de Azure Stack Edge Pro con GPU a través de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 8089666388134e8443c515d86e91ae3ba5c32b58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749788"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU a través de Azure Portal

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Puede crear y administrar máquinas virtuales (VM) en un dispositivo de Azure Stack Edge con GPU mediante Azure Portal, plantillas y cmdlets de Azure PowerShell y a través de scripts de Python o de la CLI de Azure. En este artículo se describe cómo crear y administrar una máquina virtual en su dispositivo de Azure Stack Edge Pro con GPU mediante Azure Portal.

> [!IMPORTANT] 
> Se recomienda habilitar la autenticación multifactor para el usuario que administra las máquinas virtuales que se implementan en el dispositivo desde la nube.
        
## <a name="vm-deployment-workflow"></a>Flujo de trabajo de implementación de una máquina virtual

El resumen general del flujo de trabajo de implementación es el siguiente:

1. Habilite una interfaz de red para el proceso en el dispositivo Azure Stack Edge. Este paso crea un conmutador virtual en la interfaz de red especificada.
1. Habilite la administración en la nube de máquinas virtuales desde Azure Portal.
1. Cargue un disco duro virtual en una cuenta de Azure Storage mediante el Explorador de Azure Storage. 
1. Use el disco duro virtual cargado para descargarlo en el dispositivo y crear una imagen de máquina virtual a partir de este. 
1. Use los recursos creados en los pasos anteriores:
    1. Imagen de máquina virtual que creó.
    1. Conmutador virtual asociado con la interfaz de red en la que habilitó el proceso.
    1. Subred asociada al conmutador virtual.

    Y cree o especifique los recursos insertados siguientes:
    1. Nombre de la VM, elija un tamaño de VM compatible, credenciales de inicio de sesión para la VM. 
    1. Cree discos de datos nuevos o conecte discos de datos existentes.
    1. Configure una dirección IP estática o dinámica para la VM. Si proporciona una dirección IP estática, elija una dirección IP libre en el intervalo de la subred de la interfaz de red habilitada para el proceso.

    Use los recursos anteriores para crear una máquina virtual.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a crear y administrar máquinas virtuales en el dispositivo a través de Azure Portal, debe asegurarse de lo siguiente:

1. Ha completado la configuración de red en el dispositivo de Azure Stack Edge Pro con GPU como se describe en el [Paso 1: Configuración de un dispositivo de Azure Stack Edge Pro con GPU](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device).

    1. Ha habilitado una interfaz de red para el proceso. Esta dirección IP de la interfaz de red se utiliza para crear un conmutador virtual para la implementación de la máquina virtual. En la interfaz de usuario local del dispositivo, vaya a **Proceso**. Seleccione la interfaz de red que va a usar para crear un conmutador virtual.

        > [!IMPORTANT] 
        > Solo puede configurar un puerto por proceso.

    1. Habilite Proceso en la interfaz de red. Azure Stack Edge Pro GPU crea y administra un conmutador virtual correspondiente a esa interfaz de red.

1. Tiene acceso a un disco duro virtual de Windows o Linux que usará para crear la imagen de la máquina virtual que quiere crear.

## <a name="deploy-a-vm"></a>Implementación de una máquina virtual

Siga estos pasos para crear una máquina virtual en el dispositivo Azure Stack Edge Pro con GPU.

### <a name="add-a-vm-image"></a>Incorporación de una imagen de máquina virtual

1. Cargue un VHD en una cuenta de Azure Storage. Siga los pasos descritos en [Uso del Explorador de Storage para la carga](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload).

   Para obtener información sobre cómo preparar el VHD, vea [Preparación de una imagen generalizada a partir de un VHD de Windows](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).

   [Solución de problemas de carga de imágenes de máquina virtual](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).

1. En Azure Portal, vaya al recurso de Azure Stack Edge correspondiente a su dispositivo. Luego, vaya a **Servicios perimetrales** > **Máquinas virtuales**.

    ![Captura de pantalla que muestra el panel Información general de un dispositivo de Azure Stack Edge, con la opción Máquinas virtuales, en Servicios perimetrales, resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. En la página **Información general**. Seleccione **Habilitar** para habilitar la administración en la nube de máquinas virtuales. 

    ![Captura de pantalla del panel Información general en la vista Máquinas virtuales de un dispositivo de Azure Stack Edge. El botón Habilitar, para habilitar la administración en la nube de máquinas virtuales, está resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. El primer paso es agregar una imagen de VM. En el paso anterior, ya cargó un disco duro virtual en la cuenta de almacenamiento. Lo usará para crear una imagen de máquina virtual.

    Seleccione **+ Agregar imagen** para descargar el disco duro virtual de la cuenta de almacenamiento y agregarlo al dispositivo. El proceso de descarga tarda varios minutos según el tamaño del disco duro virtual y el ancho de banda de Internet disponible para la descarga. 

    ![Captura de pantalla del panel Información general de Maquinas virtuales, con el botón Más Agregar imagen resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. En el panel **Agregar imagen**, escriba los campos siguientes. A continuación, seleccione **Agregar**.

    |Campo  |Descripción  |
    |---------|---------|
    |Descargar del blob de almacenamiento    |Vaya a la ubicación del blob de almacenamiento en la cuenta de almacenamiento donde se cargó el VHD.         |
    |Descargar a    | Establézcalo automáticamente en el dispositivo actual en el que va a implementar la máquina virtual.        |
    |Grupo de recursos de Edge  |Seleccione el grupo de recursos al que quiere agregar la imagen. |
    |Guardar imagen como      | El nombre de la imagen de máquina virtual que va a crear a partir del disco duro virtual que ha cargado en la cuenta de almacenamiento.        |
    |Tipo de SO     |Elija entre Windows o Linux como sistema operativo del disco duro virtual que usará para crear la imagen de máquina virtual.         |
   

    ![Captura de pantalla que muestra la página Agregar imagen para una máquina virtual, con el botón Agregar resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Se descarga el disco duro virtual y se crea la imagen de máquina virtual. La creación de la imagen tarda varios minutos en completarse. Verá una notificación que indica que se ha creado correctamente la imagen de máquina virtual.<!--There's a fleeting notification that image creation is in progress, but I didn't see any notification that image creation completed successfully.-->

    ![Captura de pantalla de Información general en Máquinas virtuales de un dispositivo de Azure Stack Edge. En el panel Imágenes, se resalta una VM recién creada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Una vez que la imagen de máquina virtual se crea correctamente, se agrega a la lista de imágenes en el panel **Imágenes**.

    ![Captura de pantalla que muestra el panel Imágenes en la vista Máquinas virtuales de un dispositivo de Azure Stack Edge. La entrada de una imagen de VM está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    El panel **Implementaciones** se actualiza para mostrar el estado de la implementación.

    ![Captura de pantalla que muestra el panel Implementaciones en la vista Máquinas virtuales de un dispositivo de Azure Stack Edge. La entrada de una VM implementada está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    La imagen que se acaba de agregar también se muestra en la página **Información general**.

    ![Captura de pantalla que muestra el panel Información general para Máquinas virtuales de un dispositivo de Azure Stack Edge. La imagen de una VM recién agregada está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Agregar una VM

Siga estos pasos para crear una máquina virtual después de crear una imagen de máquina virtual.

1. En la página **Información general**, para **Máquinas virtuales**, seleccione **+ Agregar máquina virtual**.

    ![Captura de pantalla que muestra el panel Información general para máquinas virtuales de un dispositivo de Azure Stack Edge. El botón Más Agregar máquina virtual está resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. En la pestaña **Aspectos básicos**, escriba los parámetros siguientes.

    |Parámetro |Descripción  |
    |---------|---------|
    |Nombre de la máquina virtual     | Escriba el nombre de la nueva máquina virtual.        |
    |Grupo de recursos de Edge     | Cree un nuevo grupo de recursos para todos los recursos asociados a la máquina virtual.        |
    |Imagen     | Seleccione de las imágenes de VM disponibles en el dispositivo.        |
    |Size     | Elija entre los [tamaños de VM compatibles](azure-stack-edge-gpu-virtual-machine-sizes.md).<br>Para una VM de GPU, seleccione un [tamaño de VM de la serie NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). |
    |Nombre de usuario     | Use el nombre de usuario predeterminado **azureuser** para que el administrador inicie sesión en la máquina virtual.        |
    |Tipo de autenticación    | Elija una clave pública SSH o una contraseña definida por el usuario.       |
    |Clave pública SSH. | Se muestra cuando selecciona el tipo de autenticación **Clave pública SSH**. Pegue la clave pública SSH. |
    |Contraseña     | Se muestra cuando selecciona el tipo de autenticación **Contraseña**. Escriba una contraseña para iniciar sesión en la máquina virtual. La contraseña debe tener al menos 12 caracteres y cumplir los [requisitos de complejidad](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-) definidos. |
    |Confirmar contraseña    | Vuelva a escribir la contraseña.        |

    ![Captura de pantalla que muestra la pestaña Aspectos básicos del Asistente para agregar máquina virtual para Azure Stack Edge. La pestaña Aspectos básicos y el botón Siguiente: Discos están resaltados.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Seleccione **Siguiente: Discos**.

1. En la pestaña **Discos**, conectará discos a la máquina virtual. 
    
    1. Puede elegir **Crear y conectar un nuevo disco** o **Conectar un disco existente**.

        ![Captura de pantalla que muestra la pestaña Discos en el Asistente para agregar máquina virtual para Azure Stack Edge. La opción Crear y conectar un nuevo disco está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Seleccione **Crear y conectar un nuevo disco**. En la hoja **Crear un disco**, proporcione un nombre para el disco y el tamaño en GiB.

        ![Captura de pantalla que muestra la pantalla "Crear un nuevo disco" en "Agregar una máquina virtual" para Azure Stack Edge. El botón Aceptar está resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1. Repita el procedimiento anterior para agregar más discos. Una vez creados los discos, aparecen en la pestaña **Discos**. Seleccione **Siguiente: Redes**.

1. En la pestaña **Redes**, configurará la conectividad de red para la máquina virtual.

    |Parámetro  |Descripción |
    |---------|---------|
    |Virtual network    | En la lista desplegable, seleccione el conmutador virtual que se creó en el dispositivo Azure Stack Edge cuando se habilitó el proceso en la interfaz de red.    |
    |Subnet     | Este campo se rellena automáticamente con la subred asociada a la interfaz de red en la que se habilitó el proceso.         |
    |Dirección IP     | Proporcione una dirección IP estática o dinámica para la VM. La dirección IP estática debe ser una dirección IP libre disponible del intervalo de subred especificado.        |

    ![Captura de pantalla de la pestaña Redes en "Agregar una máquina virtual" para Azure Stack Edge. La pestaña Redes y el botón Siguiente: Opciones avanzadas están resaltados.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Seleccione **Siguiente: Opciones avanzadas**. En la pestaña **Opciones avanzadas**, puede seleccionar una extensión para instalar durante la implementación de la VM, y puede especificar un script `cloud-init` para personalizar la VM.

1. Si quiere instalar una extensión en la VM al crearla, elija **Seleccionar una extensión para instalarla**. En la pestaña **Agregar extensión**, seleccione la extensión.

    Para conocer los pasos detallados para instalar una extensión de GPU durante la implementación de VM, consulte [Implementación de máquinas virtuales con GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).

    ![Ilustración de dos pasos para agregar una extensión de GPU a la pestaña Opciones avanzadas de "Agregar una máquina virtual". Las opciones para seleccionar y agregar una extensión están resaltadas.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-extension-01.png)

1. Si quiere usar la utilidad `cloud-init` para personalizar la nueva VM en su primer arranque, en la pestaña **Opciones avanzadas**, pegue el script `cloud-init` en el cuadro **Datos personalizados** en **Custom data and cloud init** (Datos personalizados y cloud-init). 

    Para más información sobre `cloud-init`, consulte [Introducción a cloud-init](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview).

    ![Captura de pantalla que muestra la pestaña Opciones avanzadas del Asistente para crear una nueva máquina virtual, con un script cloud-init en el cuadro Datos personalizados resaltado.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-tab-with-cloud-init-script.png)

    Seleccione **Siguiente: Revisar y crear**.

1. En la pestaña **Revisar y crear**, revise las especificaciones de la máquina virtual. Seleccione **Crear**.

    ![Captura de pantalla que muestra la pestaña Revisar y crear del asistente "Agregar una máquina virtual" en Azure Stack Edge. La pestaña Revisar y crear está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Se inicia la creación de la VM y puede tardar hasta 20 minutos. Puede ir a **Implementaciones** para administrar la creación de la VM.

    ![Captura de pantalla que muestra el panel Implementaciones en la vista Máquinas virtuales de un dispositivo de Azure Stack Edge. La entrada de una VM implementada está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

1. Una vez que la VM se crea correctamente, verá la nueva VM en el panel **Información general**.

    ![Captura de pantalla que muestra el panel Información general para Máquinas virtuales de un dispositivo de Azure Stack Edge. Una nueva máquina virtual está resaltada en la lista.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Seleccione la VM recién creada para ir a **Máquinas virtuales**.

    ![Captura de pantalla que muestra el panel Máquinas virtuales de un dispositivo de Azure Stack Edge. La etiqueta Máquinas virtuales y una entrada de máquina virtual están resaltadas.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Seleccione la máquina virtual para ver los detalles.

    ![Captura de pantalla que muestra la pestaña Detalles en el panel Información general de una máquina virtual en Azure Stack Edge. La etiqueta Información general y la dirección IP en Redes están resaltadas.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

    Usará la dirección IP de la interfaz de red para conectarse a la máquina virtual.

## <a name="connect-to-a-vm"></a>Conexión a una máquina virtual

Los pasos para conectarse pueden ser diferentes dependiendo de si se ha creado una máquina virtual Windows o Linux. No se puede conectar a las máquinas virtuales implementadas en el dispositivo a través de Azure Portal. Siga los pasos para conectarse a la máquina virtual Linux o Windows.

### <a name="connect-to-a-linux-vm"></a>Conexión a una máquina virtual Linux

Siga estos pasos para conectarse a una máquina virtual Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Conexión a una máquina virtual Windows

Siga estos pasos para conectarse a una máquina virtual Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de una máquina virtual con GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)
- [Solución de problemas de implementación de máquinas virtuales](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Supervisión de la actividad de VM en el dispositivo](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Supervisión de CPU y memoria en una VM](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

