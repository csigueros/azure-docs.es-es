---
title: Habilitación de la identidad administrada en el tema del sistema de Azure Event Grid
description: En este artículo se describe cómo habilitar la identidad de servicio administrada para un tema del sistema de Azure Event Grid.
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: d5d8c15c818f1a9735b6cf32fe48276fb311e8ea
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633576"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Asignación de una identidad administrada por el sistema a un tema del sistema de Event Grid
En este artículo, aprenderá a asignar la identidad administrada por el sistema o por el usuario para un tema del sistema de Event Grid existente. Para más información sobre las identidades administradas, consulte [¿Qué son las identidades administradas de recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Puede habilitar la identidad asignada por el sistema o la identidad asignada por el usuario para un tema del sistema, pero no ambas. Puede tener como máximo dos identidades asignadas por el usuario asignadas a un tema del sistema. 

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>Habilitación de la identidad administrada para un tema del sistema existente
En esta sección se muestra cómo habilitar una identidad administrada para un tema del sistema existente. 

1. Vaya a [Azure Portal](https://portal.azure.com).
2. En la barra de búsqueda de la parte superior, busque **Temas del sistema de Event Grid**.
3. Seleccione el **tema del sistema** para el que quiere habilitar la identidad administrada. 
4. Seleccione **Identidad** en el menú de la izquierda. Esta opción no es visible para un tema del sistema que se encuentra en la ubicación global. 

### <a name="enable-system-assigned-identity"></a>Activar una identidad asignada por el sistema
1. **Active** el conmutador para habilitar la identidad. 
1. Seleccione **Guardar** en la barra de herramientas para guardar la configuración. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Página de identidad para un tema del sistema."::: 
1. Seleccione **Sí** en el mensaje de confirmación. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Asignación de una identidad a un tema del sistema: confirmación."::: 
1. Confirme que ve el id. de objeto de la identidad administrada asignada por el sistema y un vínculo para asignar roles. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Asignación de una identidad a un tema del sistema: completado."::: 

### <a name="enable-user-assigned-identity"></a>Habilitar la identidad asignada por el usuario

1. En primer lugar, cree una identidad asignada por el usuario siguiendo las instrucciones del artículo [Administración de identidades administradas asignadas por el usuario](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md). 
1. En la página **Identidad**, cambie a la pestaña **Asignada por el usuario** del panel derecho y seleccione **+ Agregar** en la barra de herramientas.

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="Imagen en la que se muestra el botón Agregar situado en la pestaña Asignado por el usuario de la página Identidad.":::
1. En la ventana para **agregar una identidad administrada por el usuario**, siga estos pasos:
    1. Seleccione la **Suscripción de Azure** que tiene la identidad por el usuario. 
    1. Seleccione la **identidad asignada por el usuario**. 
    1. Seleccione **Agregar**. 
1. Actualice la lista en la pestaña **Asignada por el usuario** para ver la identidad asignada por el usuario que ha agregado.

## <a name="enable-managed-identity-when-creating-a-system-topic"></a>Habilitación de la identidad administrada al crear un tema del sistema

1. En Azure Portal, en la barra de búsqueda, busque y seleccione **Temas del sistema de Event Grid**. 
1. En la página **Temas del sistema de Event Grid**, seleccione **+ Agregar** en la barra de herramientas. 
1. En la página **Aspectos básicos** del asistente para la creación, siga estos pasos: 
    1. En **Tipos de temas**, seleccione el tipo de tema que admite un tema del sistema. En el ejemplo siguiente, se selecciona la opción **Cuentas de almacenamiento**. 
    2. En **Suscripción**, seleccione la suscripción de Azure que contiene el recurso de Azure. 
    1. En **Grupo de recursos**, seleccione el grupo de recursos que contiene el recurso de Azure. 
    1. En **Recurso**, seleccione el recurso. 
    1. Especifique un **nombre** para el tema del sistema.
    1. Habilitación de una entidad administrada:
        1. Para habilitar la identidad asignada por el sistema, seleccione **Habilitar la identidad asignada por el sistema**. 
        
            :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-managed-identity.png" alt-text="Imagen que muestra la captura de pantalla del Asistente para la creación de temas del sistema con la opción de identidad asignada por el sistema seleccionada.":::            
        1. Para habilitar la identidad asignada por el sistema: 
            1. Seleccione **Identidad asignada por el usuario** y, después, seleccione **Agregar identidad asignada por el usuario**. 
        
                :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-user-identity.png" alt-text="Imagen que muestra la captura de pantalla del Asistente para la creación de tema del sistema con la opción de identidad asignada por el usuario seleccionada.":::            
            1. En la ventana para **agregar una identidad administrada por el usuario**, siga estos pasos:
                1. Seleccione la **Suscripción de Azure** que tiene la identidad por el usuario. 
                1. Seleccione la **identidad asignada por el usuario**. 
                1. Seleccione **Agregar**.                         

> [!NOTE]
> Actualmente, no puede habilitar una identidad administrada para un nuevo tema del sistema al crear una suscripción de eventos en un recurso de Azure que admite temas del sistema. 


## <a name="global-azure-sources"></a>Orígenes globales de Azure
Solo puede habilitar la identidad administrada por el sistema para los recursos regionales de Azure. No se puede habilitar para los temas del sistema asociados a recursos globales de Azure, como suscripciones de Azure, grupos de recursos o Azure Maps. Los temas del sistema para estos orígenes globales tampoco están asociados a una región específica. La página **Identidad** del tema del sistema cuya ubicación está establecida en **Global** no se ve. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Tema del sistema con la ubicación establecida en Global"::: 



## <a name="next-steps"></a>Pasos siguientes
Agregue la identidad a un rol adecuado (por ejemplo, Remitente de los datos de Service Bus) en el destino (por ejemplo, una cola de Service Bus). Para los pasos detallados, consulte [Concesión a la identidad administrada el acceso al destino de Event Grid](add-identity-roles.md). 