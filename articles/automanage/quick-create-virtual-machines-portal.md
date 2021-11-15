---
title: 'Guía de inicio rápido: habilitación de la administración automática de Azure para máquinas virtuales en Azure Portal'
description: Obtenga información sobre cómo habilitar rápidamente la administración automática para máquinas virtuales en una VM nueva o existente en Azure Portal.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom: mode-portal
ms.openlocfilehash: ab9a6f5ff85fe0b5b2d1df4d2a3f64bb4cb21207
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465724"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Inicio rápido: Habilitación de la administración automática para máquinas virtuales en Azure Portal

Introducción a la administración automática para máquinas virtuales mediante Azure Portal para habilitar la administración automática en una VM nueva o existente.


## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.

> [!IMPORTANT]
> Debe tener el rol **Colaborador** en el grupo de recursos que contiene las máquinas virtuales para habilitar Automanage. Si está habilitando Automanage por primera vez en una suscripción necesita los siguientes permisos: rol **Propietario** o **Colaborador** junto con los roles **Administrador de acceso de usuario** en la suscripción.


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en el [Portal de Azure](https://portal.azure.com)

## <a name="enable-automanage-on-existing-machines"></a>Habilitación de Automanage en máquinas existentes

1. En la barra de búsqueda, busque y seleccione **Automanage: procedimientos recomendados para máquinas de Azure**.

2. Seleccione **Enable on existing VM** (Habilitar en una máquina virtual existente).

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Habilitación en una máquina virtual existente.":::

4. En **Perfil de configuración**, seleccione el tipo de perfil: **Procedimientos recomendados de Azure: Producción**, **Procedimientos recomendados de Azure: desarrollo/pruebas** o [**Perfil personalizado**](virtual-machines-custom-profile.md).

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Selección de entornos.":::

   Haga clic en **Ver perfiles de procedimientos recomendados de Azure** para ver las diferencias entre los entornos.
    1. Seleccione un entorno en la lista desplegable: *Desarrollo/pruebas* o *Producción*.
    1. Haga clic en el botón **Aceptar** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Browse production environment (Explorar entorno de producción).":::

5. En la hoja **Seleccionar máquinas**:
    1. Filtre la lista por su **Suscripción** y **Grupo de recursos**.
    1. Active la casilla de cada máquina virtual que desee incorporar.
    1. Haga clic en el botón **Seleccionar**.
    > [!NOTE]
    > Puede seleccionar máquinas virtuales de Azure y servidores habilitados para Azure Arc.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Seleccione una máquina virtual existente en la lista de máquinas virtuales disponibles.":::


6. Haga clic en el botón **Habilitar**.


## <a name="disable-automanage-for-vms"></a>Deshabilitación de Automanage para máquinas virtuales

Deje de usar rápidamente Azure Automanage para máquinas virtuales deshabilitando la administración automática.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Deshabilite Automanage en una máquina virtual.":::

1. Vaya a la página **Automanage – Azure virtual machine best practices** (Automanage: procedimientos recomendados para máquinas virtuales de Azure) en la que se muestran todas las máquinas virtuales administradas automáticamente.
1. Active la casilla situada junto a la máquina virtual que desea deshabilitar.
1. Haga clic en el botón **Disable automanagement** (Deshabilitar administración automática).
1. Lea detenidamente los mensajes del elemento emergente resultante antes de aceptar **Deshabilitar**.


## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha creado un nuevo grupo de recursos para probar Azure Automanage para máquinas virtuales y ya no lo necesita, puede eliminar el grupo de recursos. Al eliminar el grupo también se eliminan la máquina virtual y todos los recursos del grupo de recursos.

Azure Automanage crea grupos de recursos predeterminados en los que almacenar los recursos. Compruebe los grupos de recursos que tienen la convención de nomenclatura "DefaultResourceGroupRegionName" y "AzureBackupRGRegionName" para limpiar todos los recursos.

1. Seleccione el **Grupo de recursos**.
1. En la página del grupo de recursos, seleccione **Eliminar**.
1. Cuando se le solicite, escriba el nombre del grupo de recursos y, después, seleccione **Eliminar**.


## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, habilitó la administración automática de Azure para máquinas virtuales.

Descubra cómo puede crear y aplicar perfiles personalizados al habilitar Automanage en la máquina virtual.

> [!div class="nextstepaction"]
> [Perfiles personalizados de Azure Automanage](virtual-machines-custom-profile.md)
