---
title: Administración de cuentas de laboratorio en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo crear o eliminar una cuenta de laboratorio y cómo ver todas las cuentas de laboratorio de una suscripción de Azure.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4de1948ecc240afecb658427975fc59228f4c82e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180293"
---
# <a name="create-and-manage-lab-accounts"></a>Creación y administración de cuentas de laboratorio
En Azure Lab Services, una cuenta de laboratorio es un contenedor para tipos de laboratorios administrados, como los laboratorios. Los administradores configuran una cuenta de laboratorio con Azure Lab Services y proporcionan acceso a los propietarios del laboratorio que pueden crear laboratorios en la cuenta. En este artículo se describe cómo crear o eliminar una cuenta de laboratorio y cómo ver todas las cuentas de laboratorio.

## <a name="create-a-lab-account"></a>Creación de una cuenta de laboratorio
Los pasos siguientes muestran cómo usar Azure Portal para crear una cuenta de laboratorio con Azure Lab Services. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** en el menú de la izquierda. Seleccione **Cuentas de laboratorio** en la sección **DevOps**. Si selecciona la estrella (`*`) junto a **Cuentas de laboratorio**, se agrega a la sección **FAVORITOS** en el menú de la izquierda. A partir de la próxima vez, seleccione **Cuentas de laboratorio** en **FAVORITOS**.

    ![Todos los servicios -> Cuentas de laboratorio](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. En la página **Cuentas de laboratorio**, seleccione **Agregar** en la barra de herramientas o seleccione **Crear una cuenta de laboratorio** en la página. 

    ![Seleccione Agregar en la página Cuentas de laboratorio](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. En la pestaña **Conceptos básicos** de la página **Crear una cuenta de laboratorio**, realice las siguientes acciones: 
    1. En **Lab account name** (Nombre de la cuenta de laboratorio), escriba un nombre. 
    2. Seleccione la **suscripción de Azure** donde desea crear la cuenta de laboratorio.
    3. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos.
    4. En **Ubicación**, seleccione la ubicación o región en la que desea crear la cuenta de laboratorio.
    5. En el campo **Allow lab creator to pick lab location** (Permitir que el creador del laboratorio seleccione su ubicación), especifique si quiere que los creadores del laboratorio puedan seleccionar una ubicación para el laboratorio. De forma predeterminada, la opción está deshabilitada. Cuando está deshabilitada, los creadores del laboratorio no pueden especificar una ubicación para el laboratorio que crean. Los laboratorios se crean en la ubicación geográfica más cercana a la cuenta del laboratorio. Cuando está habilitada, el creador del laboratorio puede seleccionar una ubicación en el momento de su creación. Para más información, consulte [Permitir al creador del laboratorio seleccionar la ubicación correspondiente](allow-lab-creator-pick-lab-location.md). 

        ![Crear una cuenta de laboratorio -> Conceptos básicos](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Seleccione **Siguiente: Avanzado** en la parte inferior de la página para ir a la pestaña **Avanzado** y, a continuación, siga estos pasos: 
    1. Seleccione una **galería de imágenes compartidas** existente o créela. Puede guardar la plantilla de máquina virtual en la galería de imágenes compartidas para que otros usuarios puedan usarla. Para más información acerca de las galerías de imágenes compartidas, consulte [Use una galería de imágenes compartidas en Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Especifique si desea **apagar automáticamente las máquinas virtuales Windows** cuando los usuarios se desconecten de ellas. Especifique cuánto tiempo deben esperar las máquinas virtuales por si el usuario se vuelve a conectar antes de que estas se apaguen automáticamente. 
    3. Para **Asociar red virtual**, seleccione una red virtual del mismo nivel como red de laboratorio. Los laboratorios creados en esta cuenta se conectan a la red virtual seleccionada y tienen acceso a los recursos de esta. Para más información, consulte [Conexión de la red virtual del laboratorio con una red virtual del mismo nivel](how-to-connect-peer-virtual-network.md).    
    8. Especifique un **rango de direcciones** para las máquinas virtuales en el laboratorio. Dicho rango debe estar en la notación de enrutamiento de interdominios sin clases (CIDR) (por ejemplo: 10.20.0.0/23). Las máquinas virtuales del laboratorio se crearán en este rango de direcciones. Para más información, consulte el apartado [Especifique un intervalo de direcciones para las máquinas virtuales en el laboratorio](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account).  

        > [!NOTE]
        > La propiedad **Intervalo de direcciones** solo se aplica si se ha habilitado **Emparejar Red virtual** para el laboratorio.

        ![Crear una cuenta de laboratorio -> Avanzado](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Seleccione **Siguiente: Etiquetas** en la parte inferior de la página para cambiar a la pestaña **Etiquetas**. Agregue las etiquetas que desee asociar a la cuenta de laboratorio. Las etiquetas son pares nombre-valor que permiten categorizar los recursos y ver una facturación consolidada mediante la aplicación de la misma etiqueta en varios recursos y grupos de recursos. Para más información, consulte [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/management/tag-resources.md).

    ![Captura de pantalla que muestra la página "Crear una cuenta de laboratorio" con la pestaña Etiquetas resaltada.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Seleccione **Revisar y crear** en la parte inferior de esta página para cambiar a la pestaña **Revisar y crear**. 
4. Revise la información de resumen de esta página y seleccione **Crear**. 

    ![Crear una cuenta de laboratorio -> Etiquetas](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Cuando la implementación se complete, expanda **Próximos pasos** y seleccione **Ir al recurso** como se indica en la imagen siguiente: 

    También puede seleccionar el **icono de campana** en la barra de herramientas (**Notificaciones**), confirmar que la implementación se realizó correctamente y, a continuación, seleccionar **Ir al recurso**. 

    Como alternativa, seleccione **Actualizar** en la página **Cuentas de laboratorio** y seleccione la cuenta de laboratorio que creó. 

    ![Ventana de creación de una cuenta de laboratorio](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá la siguiente página de a **cuenta de laboratorio**:

    ![Página de la cuenta de laboratorio](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Vista de las cuentas de laboratorio
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los recursos** en el menú. 
3. Seleccione **Cuentas de laboratorio** como **tipo**. 
    También puede filtrar por suscripción, grupo de recursos, ubicaciones y etiquetas. 

    ![Todos los recursos -> Cuentas de laboratorio](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Eliminación de una cuenta de laboratorio
Siga las instrucciones que aparecen en la sección anterior que muestra una lista de las cuentas de laboratorio. Use las instrucciones siguientes para eliminar una cuenta de laboratorio: 

1. Seleccione la **cuenta de laboratorio** que desee eliminar. 
2. Seleccione **Eliminar** en la barra de herramientas. 

    ![Cuentas de laboratorio -> botón Eliminar](./media/how-to-manage-lab-accounts/delete-button.png)
1. Escriba **Sí** para confirmar.
1. Seleccione **Eliminar**. 

    ![Eliminar cuenta de laboratorio - confirmación](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> También puede usar el módulo Az.LabServices de PowerShell (versión preliminar) para administrar cuentas de laboratorio. Para más información, consulte la [página principal de Az.LabServices en GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Pasos siguientes
Consulte otros artículos en la sección **Guías paso a paso** -> **Creación y configuración de cuentas de laboratorio (propietario de la cuenta de laboratorio)** de la tabla de contenido (TDC). 