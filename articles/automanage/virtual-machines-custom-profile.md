---
title: Creación de un perfil personalizado en Azure Automanage para máquinas virtuales
description: Obtenga información sobre cómo crear un perfil personalizado en Azure Automanage y seleccionar los servicios y la configuración.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 821ad720645c433b9a487383e5ed9edb5bc21e61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478338"
---
# <a name="create-a-custom-profile-in-azure-automanage-for-vms"></a>Creación de un perfil personalizado en Azure Automanage para máquinas virtuales

Azure Automanage para procedimientos recomendados de máquina tiene perfiles predeterminados de procedimientos recomendados que no se pueden editar. Aun así, si necesita más flexibilidad, puede elegir el conjunto de servicios y la configuración mediante la creación de un perfil personalizado.

Se permite alternar la activación y la desactivación de los servicios. Actualmente también se admite la personalización de ajustes en [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) y [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


## <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.

> [!IMPORTANT]
> El siguiente permiso de Azure RBAC es necesario para habilitar Automanage por primera vez en una suscripción: rol **Propietario** o **Colaborador** junto con los roles **Administrador de acceso de usuario**.


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Habilitación de Automanage para máquinas virtuales en una máquina virtual existente

1. En la barra de búsqueda, busque y seleccione **Automanage: procedimientos recomendados para máquinas virtuales de Azure**.

2. Seleccione **Enable on existing VM** (Habilitar en una máquina virtual existente).

3. En **Perfil de configuración**, seleccione **Perfil personalizado**.

4. Seleccione un perfil personalizado existente en la lista desplegable, si existe uno, o haga clic en **Crear** para crear uno.

5. En la hoja **Crear perfil**, rellene los detalles:
    1. Nombre del perfil
    1. Subscription
    1. Grupo de recursos
    1. Region

    :::image type="content" source="media\virtual-machine-custom-profile\create-custom-profile.png" alt-text="Rellene los detalles del perfil personalizado.":::

6. Ajuste el perfil con los servicios y la configuración deseados y haga clic en **Crear**.

7. En la hoja **Seleccionar máquinas**:
    1. Filtre la lista de máquinas virtuales por su **Suscripción** y **Grupo de recursos**.
    1. Active la casilla de cada máquina virtual que desee incorporar.
    1. Haga clic en el botón **Seleccionar**.

    :::image type="content" source="media\virtual-machine-custom-profile\existing-vm-select-machine.png" alt-text="Seleccione una máquina virtual existente en la lista de máquinas virtuales disponibles.":::

    > [!NOTE]
    > Haga clic en **Show ineligible machines** (Mostrar máquinas virtuales no aptas) para ver una lista de las máquinas que no son compatibles y los motivos. 

## <a name="disable-automanage-for-vms"></a>Deshabilitación de Automanage para máquinas virtuales

Deje de usar rápidamente Azure Automanage para máquinas virtuales deshabilitando la administración automática.

:::image type="content" source="media\virtual-machine-custom-profile\disable-step-1.png" alt-text="Deshabilite Automanage en una máquina virtual.":::

1. Vaya a la página **Automanage – Azure machine best practices** (Automanage: procedimientos recomendados para máquinas de Azure) en la que se muestran todas las máquinas virtuales administradas automáticamente.
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

Consulte las preguntas más frecuentes respondidas en nuestra sección de preguntas más frecuentes. 

> [!div class="nextstepaction"]
> [Preguntas más frecuentes](faq.yml)