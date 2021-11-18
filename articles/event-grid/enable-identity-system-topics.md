---
title: Habilitación de la identidad administrada en el tema del sistema de Azure Event Grid
description: En este artículo se describe cómo habilitar la identidad de servicio administrada para un tema del sistema de Azure Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 825b1d22568b110f22e596d424a2f781b65de625
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157150"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Asignación de una identidad administrada por el sistema a un tema del sistema de Event Grid
En este artículo, aprenderá a asignar la identidad asignada por el sistema o por el usuario a un tema del sistema de Event Grid. Para obtener información sobre las identidades administradas, consulte [¿Qué son las identidades administradas de recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md).  

> [!NOTE]
> - A cada tema del sistema se le pueden asignar una identidad asignada por el sistema y un máximo de dos identidades asignadas por el usuario. 
> - Se pueden habilitar identidades para los temas del sistema asociados a recursos globales de Azure, como suscripciones de Azure, grupos de recursos o Azure Maps. Los temas del sistema para estos orígenes globales tampoco se asocian a ninguna región concreta.

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>Habilitación de la identidad administrada para un tema del sistema existente
En esta sección se muestra cómo habilitar una identidad administrada para un tema del sistema existente. 

1. Vaya a [Azure Portal](https://portal.azure.com).
2. En la barra de búsqueda de la parte superior, busque **Temas del sistema de Event Grid**.
3. Seleccione el **tema del sistema** para el que quiere habilitar la identidad administrada. 
4. Seleccione **Identidad** en el menú de la izquierda.  

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

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="Imagen en la que se muestra el botón Agregar seleccionado en la pestaña Asignado por el usuario de la página Identidad.":::
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
> - Actualmente, Azure Portal no permite asignar identidades asignadas por el sistema y asignadas por el usuario al crear temas del sistema. Sin embargo, ambos se pueden asignar después de crear el tema del sistema. 
> - Actualmente, no puede habilitar una identidad administrada para un nuevo tema del sistema al crear una suscripción de eventos en un recurso de Azure que admite temas del sistema. 


## <a name="next-steps"></a>Pasos siguientes
Agregue la identidad a un rol adecuado (por ejemplo, Remitente de los datos de Service Bus) en el destino (por ejemplo, una cola de Service Bus). Para los pasos detallados, consulte [Concesión a la identidad administrada el acceso al destino de Event Grid](add-identity-roles.md). 