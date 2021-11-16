---
title: Actualización de las máquinas de Azure Automanage a la última versión de Automanage
description: Obtenga información sobre cómo actualizar las máquinas a la última versión de Azure Automanage.
author: mmccrory
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: memccror
ms.openlocfilehash: d7cc53c30ead02a2ca4fc40fe00ae2235016336f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478346"
---
# <a name="upgrade-your-machines-to-the-latest-automanage-version"></a>Actualización de las máquinas a la última versión de Automanage

Automanage publicó una nueva versión de la oferta de procedimientos recomendados para máquinas en noviembre de 2021. La nueva API ahora admite la creación de perfiles personalizados en los que puede elegir los servicios y la configuración que quiere aplicar a las máquinas. Además, con esta nueva versión, la cuenta de Automanage ya no es necesaria. En este artículo se describen las diferencias entre las versiones y cómo actualizar. 

## <a name="how-to-upgrade-your-machines"></a>Actualización de las máquinas

A continuación, se muestra el conjunto de instrucciones sobre cómo actualizar las máquinas a la versión de API más reciente de Automanage. 

### <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.

> [!IMPORTANT]
> El siguiente permiso de Azure RBAC es necesario para habilitar Automanage por primera vez en una suscripción con la nueva versión de Automanage: rol **Propietario** o **Colaborador** junto con los roles **Administrador de acceso de usuarios**.

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com/).


### <a name="check-to-see-which-machines-need-to-be-upgraded"></a>Comprobación de las máquinas virtuales que necesitan actualizarse

Todas las máquinas que necesiten actualizarse tendrán el estado **Necesita actualización**. También verá un banner en la página de información general de Automanage que indica que necesita actualizar las máquinas. 

:::image type="content" source="media\automanage-upgrade\overview-blade.png" alt-text="Estado Necesita actualización":::

### <a name="disable-automanage-machines-that-need-to-be-upgrade"></a>Deshabilitación de las máquinas de Automanage que necesitan actualizarse

Para que una máquina pueda actualizarse a la nueva versión de Automanage, es necesario deshabilitarla en la versión anterior de Automanage. Para deshabilitar las máquinas, siga estos pasos:
1. Active la casilla situada junto a la máquina virtual que desea deshabilitar.
1. Haga clic en el botón **Deshabilitar**.
1. Lea detenidamente los mensajes del elemento emergente resultante antes de aceptar **Disable** (Deshabilitar).

:::image type="content" source="media\automanage-upgrade\disable-automanage.png" alt-text="Deshabilitación de Automanage":::

### <a name="re-enable-automanage-on-your-machines"></a>Rehabilitación de Automanage en las máquinas

Una vez desactivadas las máquinas en Automanage, ahora puede volver a habilitar Automanage. Al volver a habilitar Automanage, Automanage usará automáticamente su última versión. 

1. Seleccione **Enable on existing VM** (Habilitar en una máquina virtual existente).

    :::image type="content" source="media\automanage-upgrade\zero-vm-list-view.png" alt-text="Habilitación en una máquina virtual existente.":::

2. En **Perfil de configuración**, seleccione el tipo de perfil: **Procedimientos recomendados de Azure: Producción**, **Procedimientos recomendados de Azure: desarrollo/pruebas** o [**Perfil personalizado**](virtual-machines-custom-profile.md).

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Selección de entornos.":::

    > [!NOTE]
    > El entorno de **Producción** se asocia con el perfil de configuración **Procedimientos recomendados de Azure: Producción**. El entorno de **Desarrollo/pruebas** se asocia con el perfil de configuración **Procedimientos recomendados de Azure: desarrollo/pruebas**. Si ha utilizado las **Preferencias de configuración**, puede crear un **Perfil personalizado** con esas mismas modificaciones. 

3. En la hoja **Seleccionar máquinas**:
    1. Filtre la lista por su **Suscripción** y **Grupo de recursos**.
    1. Active la casilla de cada máquina virtual que desee incorporar.
    1. Haga clic en el botón **Seleccionar**.
    > [!NOTE]
    > Puede seleccionar máquinas virtuales de Azure y servidores habilitados para Azure Arc.

    :::image type="content" source="media\automanage-upgrade\existing-vm-select-machine.png" alt-text="Seleccione una máquina virtual existente en la lista de máquinas virtuales disponibles.":::

4. Haga clic en el botón **Habilitar**.

Ahora, las máquinas se incorporarán a la última versión de Automanage.

## <a name="differences-in-the-automanage-versions"></a>Diferencias entre las versiones de Automanage

### <a name="environment-and-configuration-profiles"></a>Entorno y perfiles de configuración
En la versión anterior de Automanage, seleccionó el tipo de entorno: Desarrollo/pruebas o Producción. En la nueva versión de Automanage, el entorno se asigna a perfiles de configuración. Las opciones del perfil de configuración son Procedimientos recomendados de Azure: desarrollo/pruebas, Procedimientos recomendados de Azure: producción y Perfil personalizado. El conjunto de servicios y configuraciones del entorno de **Desarrollo/pruebas** es el mismo que el del perfil de configuración **Procedimientos recomendados de Azure: desarrollo/pruebas**. El conjunto de servicios y configuraciones del entorno de **Producción** es el mismo que el del perfil de configuración **Procedimientos recomendados de Azure: producción**. 

### <a name="configuration-preferences-and-custom-profiles"></a>Preferencias de configuración y perfiles personalizados
En la versión anterior de Automanage, podía personalizar un subconjunto de configuraciones en **Preferencias de configuración**. En la última versión de Automanage, hemos mejorado la personalización para que pueda elegir y seleccionar cada servicio que quiera incorporar y admitir la modificación de algunas configuraciones en los servicios desde **Perfiles personalizados**. 

### <a name="automanage-account-and-first-party-application"></a>Cuenta de Automanage y aplicación propia
En la versión anterior de Automanage, la cuenta de Automanage se usaba como un archivo MSI para realizar acciones en el equipo. Sin embargo, en la última versión de Automanage, Automanage usa una aplicación propia (Id. de aplicación: d828acde-4b48-47f5-a6e8-52460104a052) para realizar acciones en las máquinas de Automanage. 

Tanto en la versión anterior como en la nueva versión de Automanage, necesita los siguientes permisos:
* Si incorpora Automanage por primera vez en una suscripción, necesita el rol **Propietario** o **Colaborador** junto con los roles **Administrador de acceso de usuarios**.
* Si incorpora Automanage en una suscripción que ya tiene máquinas de Automanage, necesita el rol **Colaborador** en el grupo de recursos donde reside la máquina. 
> [!NOTE]
> Si la máquina que va a incorporar a Automanage ya está conectada a un área de trabajo de Log Analytics en una suscripción diferente a la de la máquina, también necesita los permisos descritos anteriormente en la suscripción del área de trabajo de Log Analytics.

## <a name="next-steps"></a>Pasos siguientes 

Consulte las preguntas más frecuentes respondidas en nuestra sección de preguntas más frecuentes. 

> [!div class="nextstepaction"]
> [Preguntas más frecuentes](faq.yml)

