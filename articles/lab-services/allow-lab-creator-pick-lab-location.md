---
title: Selección de la ubicación por parte del creador del laboratorio en Azure Lab Services
description: En este artículo se explica cómo un administrador de cuentas de laboratorio puede permitir que los creadores de laboratorios seleccionen las ubicaciones de sus laboratorios.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f9dbafe6b297d7b94b1d1ec5eb419345cb140457
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177258"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Selección de la ubicación del laboratorio por parte del creador de este en Azure Lab Services
En Azure Lab Services, el propietario de una cuenta de laboratorio puede permitir a los creadores de laboratorios (educadores) seleccionar una ubicación para el laboratorio que crean. Esta ubicación puede ser diferente a la de la cuenta de laboratorio. Una ubicación es un grupo de regiones de Azure. Por ejemplo, la ubicación Estados Unidos es un grupo de regiones como Este de EE. UU., Oeste de EE. UU., etc. 

Como propietario de una cuenta de laboratorio, puede seleccionar la opción **Permitir al creador del laboratorio seleccionar la ubicación correspondiente** al crear una cuenta de laboratorio y después de crear la cuenta de laboratorio (o una cuenta de laboratorio existente). 

## <a name="at-the-time-of-lab-account-creation"></a>Al crear la cuenta de laboratorio
Al crear una cuenta de laboratorio, aparece esta opción en la primera pantalla (pestaña **Conceptos básicos**). 

![Habilitación de la opción al crear el laboratorio](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Esta opción está deshabilitada si se selecciona una red virtual del mismo nivel para la cuenta de laboratorio en la pestaña **Opciones avanzadas**.  

![Cuando la red virtual del mismo nivel está habilitada](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Después de crear la cuenta de laboratorio
Después de crear la cuenta de laboratorio, puede habilitar o deshabilitar esta opción si sigue estos pasos: 

1. En la página **Cuenta de laboratorio**, seleccione **Configuración del laboratorio** en el menú de la izquierda.
2. Seleccione la opción **Permitir al creador del laboratorio seleccionar la ubicación correspondiente** si quiere permitir al creador del laboratorio seleccionar una ubicación para este. Si se deshabilita, los laboratorios se crean de forma automática en la misma ubicación en la que existe la cuenta de laboratorio. 
    
    Este campo está deshabilitado cuando se selecciona una red virtual para el campo **Red virtual del mismo nivel**. Se debe a que los laboratorios de la cuenta de laboratorio deben estar en la misma región que esta para que puedan acceder a los recursos de la red virtual del mismo nivel. 
1. Seleccione **Guardar** en la barra de herramientas. 

    ![Configuración del laboratorio](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Sin red virtual ni permiso para seleccionar ubicación
En este escenario no se ha habilitado la opción **Permitir al creador del laboratorio seleccionar la ubicación correspondiente**. 

![Sin ubicación de laboratorio](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Así, los creadores de laboratorios (educadores) no ven ninguna opción para seleccionar una ubicación para el laboratorio. Ven el precio por hora de cada opción de tamaño que tienen a su disposición. Cuando crean un laboratorio, se crea en una región de Azure que se encuentra en la misma ubicación que la región de Azure en la que se encuentra su cuenta de laboratorio. Por ejemplo, si la cuenta de laboratorio está en **Oeste de EE. UU.** , el laboratorio podría crearse en **Centro-sur de EE. UU.** , pero no en **Este de Canadá**. No se garantiza nada con respecto a la región seleccionada, aparte de que se encuentra en la ubicación. Si un tamaño está restringido en ese momento, el creador del laboratorio ve una casilla donde puede observar los tamaños que normalmente se admiten pero que en ese punto no están disponibles. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>En red virtual y sin permiso para seleccionar ubicación
En este escenario, la opción **Permitir al creador del laboratorio seleccionar la ubicación correspondiente** está deshabilitada porque se ha seleccionado una red virtual del mismo nivel para la cuenta de laboratorio. Así, los creadores de laboratorios ven la misma pantalla que con la opción anterior. Dado que todas las máquinas virtuales deben estar en la misma región de Azure que la red virtual, el laboratorio se crea en la misma región de Azure en la que se encuentra la red virtual. Si esa región determinada tiene una restricción de tamaño, el tamaño aparece como no disponible. 

## <a name="location-selection-is-enabled"></a>Selección de ubicación habilitada
Al seleccionar **Permitir al creador del laboratorio seleccionar la ubicación correspondiente**, los creadores de laboratorios (educadores) ven una opción para seleccionar una ubicación al crear un laboratorio. 

![Selección de una ubicación de laboratorio](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Los creadores de laboratorios ven el rango de precios de todas las ubicaciones en las que se encuentra el tamaño y pueden seleccionar una. El laboratorio se crea en cualquier región de Azure que se asigne a esa ubicación.

Si una ubicación está restringida, no aparece de forma predeterminada en la lista. Expanda la lista desplegable y seleccione **Mostrar ubicaciones no disponibles para este tamaño**. 

![Mostrar ubicaciones no disponibles](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Coste
Anteriormente, los precios se basaban en el tamaño de la máquina virtual seleccionada para el laboratorio. Ahora el precio se basa en la combinación de sistema operativo (SO), tamaño y ubicación. 

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Conexión de la red del laboratorio con una red virtual del mismo nivel en Azure Lab Services](how-to-connect-peer-virtual-network.md)
- [Asociación o desasociación de una galería de imágenes compartidas en Azure Lab Services](how-to-attach-detach-shared-image-gallery.md)
- [Cómo agregar un usuario como propietario de un laboratorio de clase en Azure Lab Services](how-to-add-user-lab-owner.md)
- [Configuración del firewall para Azure Lab Services](how-to-configure-firewall-settings.md)
- [Configuración de cuentas de laboratorio en Azure Lab Services](how-to-configure-lab-accounts.md)