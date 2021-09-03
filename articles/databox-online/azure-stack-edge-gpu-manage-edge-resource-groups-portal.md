---
title: Administración de grupos de recursos de Edge en su dispositivo Azure Stack Edge Pro GPU
description: Obtenga información sobre cómo administrar grupos de recursos de Edge en su dispositivo Azure Stack Edge Pro GPU, Azure Stack Edge Pro R y Azure Stack Edge Mini R a través de Azure Portal.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: c77fb42579af8d1e4bad7e55746ad36d16c983b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780661"
---
# <a name="manage-edge-resource-groups-on-azure-stack-edge-pro-gpu-devices"></a>Administración de grupos de recursos de Edge en dispositivos Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Los grupos de recursos de Edge contienen recursos que se crean en el dispositivo a través de la instancia local de Azure Resource Manager durante la creación e implementación de máquinas virtuales. Estos recursos locales pueden incluir máquinas virtuales, imágenes de máquina virtual, discos, interfaces de red y otros tipos de recursos, como cuentas de almacenamiento de Edge.

En este artículo se describe cómo ver y eliminar grupos de recursos de Edge en un dispositivo Azure Stack Edge Pro GPU.

## <a name="view-edge-resource-groups"></a>Visualización de grupos de recursos de Edge

Siga estos pasos para ver los grupos de recursos de Edge de la suscripción actual.

1. Vaya a **Máquinas virtuales** en el dispositivo y, a continuación, al panel **Recursos**. Seleccione **Grupos de recursos de Edge**.

    ![Captura de pantalla de la vista Recursos para máquinas virtuales en un dispositivo Azure Stack Edge. La pestaña Grupos de recursos de Edge se muestra y resalta.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

    > [!NOTE]
    > Puede obtener la misma lista mediante [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-6.1.0&preserve-view=true) en Azure PowerShell después de configurar el entorno Azure Resource Manager en el dispositivo. Para más información, consulte [Conexión a Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="delete-an-edge-resource-group"></a>Eliminación de un grupo de recursos de Edge

Siga estos pasos para eliminar un grupo de recursos de Edge que ya no está en uso.

> [!NOTE]
> - Un grupo de recursos debe estar vacío para eliminarse. 
> - No se puede eliminar el grupo de recursos ASERG. Ese grupo de recursos almacena la red virtual ASEVNET, que se crea automáticamente al habilitar el proceso en el dispositivo.

1. Vaya a **Máquinas virtuales** en el dispositivo y, a continuación, al panel **Recursos**. Seleccione **Grupos de recursos de Edge**.

    ![Captura de pantalla que muestra la vista Recursos para máquinas virtuales en un dispositivo Azure Stack Edge. La pestaña Grupos de recursos de Edge se muestra y resalta.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

1. Seleccione el grupo de recursos que desea eliminar. En el extremo derecho del grupo de recursos, seleccione el icono de eliminación (papelera).

   El icono de eliminación solo se muestra cuando un grupo de recursos no contiene ningún recurso.

    ![Captura de pantalla de la vista Recursos, pestaña Grupos de recursos de Edge, para máquinas virtuales en un dispositivo Azure Stack Edge. Un icono de papelera junto un grupo de recursos indica que se puede eliminar. El icono se resalta.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-02.png)

1. Verá un mensaje en el que se le pide que confirme que desea eliminar el grupo de recursos de Edge. La operación no se puede revertir. Seleccione **Sí**.

    ![Captura de pantalla de la pestaña Grupos de recursos de Edge en la vista Recursos para máquinas virtuales. El icono de papelera resaltado indica que se puede eliminar un grupo de recursos.](./media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-03.png)

    Una vez completada la eliminación, el grupo de recursos se quita de la lista.

## <a name="next-steps"></a>Pasos siguientes

- Para aprender a administrar el dispositivo de Azure Stack Edge Pro con GPU, consulte [Uso de la interfaz de usuario web local para administrar un dispositivo de Azure Stack Edge Pro con GPU](azure-stack-edge-manage-access-power-connectivity-mode.md).

- [Configuración del entorno de Azure Resource Manager en el dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).