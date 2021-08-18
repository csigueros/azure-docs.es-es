---
title: Creación de una preferencia personalizada en Azure Automanage para máquinas virtuales
description: Aprenda a ajustar la configuración del entorno de Azure Automanage y a establecer sus propias preferencias.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: edd82086dba6f603c50edd23c3f3757b95dcdb9d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468370"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Creación de una preferencia personalizada en Azure Automanage para máquinas virtuales

Azure Automanage con la funcionalidad de procedimientos recomendados para máquinas virtuales tiene entornos predeterminados que se pueden ajustar si es necesario. En este artículo se explica cómo puede establecer sus propias preferencias al habilitar la administración automática en una máquina virtual nueva o existente.

Actualmente admitimos las personalizaciones en [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) y [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Si Automanage está habilitado, no puede cambiar el entorno ni la preferencia de configuración de la máquina virtual. Tendrá que deshabilitar Automanage en esa máquina virtual y habilitarlo de nuevo con el entorno y las preferencias de configuración que desee.


## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.

> [!IMPORTANT]
> El siguiente permiso de Azure RBAC es necesario para habilitar Automanage: el rol **Propietario** o **Colaborador** junto con el rol **Administrador de acceso de usuario**.


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Habilitación de Automanage para máquinas virtuales en una máquina virtual existente

1. En la barra de búsqueda, busque y seleccione **Automanage: procedimientos recomendados para máquinas virtuales de Azure**.

2. Seleccione **Enable on existing VM** (Habilitación en una máquina virtual existente).

3. En la hoja **Seleccionar máquinas**:
    1. Filtre la lista de máquinas virtuales por su **Suscripción** y **Grupo de recursos**.
    1. Active la casilla de cada máquina virtual que desee incorporar.
    1. Haga clic en el botón **Seleccionar**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Seleccione una máquina virtual existente en la lista de máquinas virtuales disponibles.":::

    > [!NOTE]
    > Haga clic en **Show ineligible machines** (Mostrar máquinas virtuales no aptas) para ver una lista de las máquinas que no son compatibles y los motivos. 

4. En **Configuración**, haga clic en **Compare Environments** (Comparar entornos).

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Compare Environments (Comparar entornos).":::

5. En la hoja **Detalles del entorno**, seleccione un entorno en la lista desplegable (*Desarrollo/pruebas* para pruebas o *Producción* para producción) y haga clic en **Aceptar**.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Browse production environment (Explorar entorno de producción).":::

6. Después de seleccionar el entorno, puede elegir las **preferencias de configuración**. De forma predeterminada, se utilizará la preferencia Procedimientos recomendados de Azure. Esta preferencia contiene la configuración recomendada para cada servicio. Puede modificar estos valores y crear una preferencia personalizada: 
    1. Haga clic en **Create new preferences** (Crear nuevas preferencias).
    1. En la hoja **Create a configuration preference** (Crear una preferencia de configuración), rellene la pestaña Conceptos básicos:
        1. Subscription
        1. Resource group
        1. Nombre de preferencia
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Rellene las preferencias de configuración.":::

7. Ajuste las preferencias de configuración que desee.
        
    > [!NOTE]
    > Cuando se modifiquen las configuraciones de los entornos, solo se permitirán los ajustes que estén comprendidos entre los límites superior e inferior de nuestros procedimientos recomendados.

8. Revise los detalles de la configuración.
9. Haga clic en el botón **Crear**.

10. Haga clic en el botón **Habilitar**.


## <a name="disable-automanage-for-vms"></a>Deshabilitación de Automanage para máquinas virtuales

Deje de usar rápidamente Azure Automanage para máquinas virtuales deshabilitando la administración automática.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Deshabilite Automanage en una máquina virtual.":::

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

Consulte las preguntas más frecuentes respondidas en nuestra sección de preguntas más frecuentes. 

> [!div class="nextstepaction"]
> [Preguntas más frecuentes](faq.yml)