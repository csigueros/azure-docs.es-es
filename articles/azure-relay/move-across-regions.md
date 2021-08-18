---
title: Traslado de un espacio de nombres de Azure Relay a otra región
description: En este artículo se muestra cómo trasladar un espacio de nombres de Azure Relay desde la región actual a otra región.
ms.topic: how-to
ms.date: 06/03/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 2f612dc5318044fd539724c66b4aa37df6d3dafa
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667780"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Traslado de un espacio de nombres de Azure Relay a otra región
En este artículo se muestra cómo trasladar un espacio de nombres de Azure Relay desde una región a otra región. Los pasos generales son los siguientes:

1. **Exportar** el espacio de nombres de Relay a una plantilla de Azure Resource Manager.
1. **Actualizar la ubicación (región)** de los recursos de la plantilla. Además, eliminar todas las retransmisiones WCF **dinámicas** de la plantilla. 

    Las retransmisiones WCF tienen dos modos. En el primer modo, la retransmisión WCF se crea explícitamente con Azure Portal o la plantilla de Azure Resource Manager. En la página **Retransmisiones WCF** de Azure Portal, verá la propiedad **isDynamic** establecida en **false** para una retransmisión en este modo. 

    En el segundo modo, la retransmisión WCF se genera automáticamente cuando un agente de escucha (servidor) se conecta para una dirección de punto de conexión determinada. Siempre que el agente de escucha esté conectado a la retransmisión, el usuario verá esta en la lista de retransmisiones WCF en Azure Portal. En el caso de una retransmisión en este modo, la propiedad **isDynamic** está establecida en **true** porque se genera dinámicamente. La retransmisión WCF dinámica desaparece cuando el agente de escucha se desconecta. 
1. **Implementar** recursos con la plantilla en la región de destino.

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de que el servicio Azure Relay esté disponible en la región de destino. Consulte los [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Preparación
Para empezar, exporte una plantilla de Resource Manager. Esta plantilla contiene la configuración que describe el espacio de nombres de Azure Relay.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los recursos** y luego seleccione el espacio de nombres de Azure Relay.
3. Seleccione **Exportar plantilla** en **Configuración** en el menú de la izquierda.
4. Elija **Descargar** en la página **Exportar plantilla**.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Descarga de la plantilla de Resource Manager":::
5. Busque el archivo ZIP que descargó desde el portal y descomprímalo en la carpeta que prefiera. Este archivo ZIP contiene los archivos JSON de plantilla y parámetros. 
1. Abra el archivo **template.json** de la carpeta extraída en el editor que prefiera.
1. Busque `location` y reemplace el valor de la propiedad por el nuevo nombre de la región. Para obtener los códigos de ubicación, consulte [Ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/). El código de una región es el nombre de la región sin espacios, por ejemplo, `West US` es igual a `westus`.
1. Quite las definiciones de los recursos de **retransmisión WCF dinámica** recursos (tipo: `Microsoft.Relay/namespaces/WcfRelays`). Las retransmisiones WCF dinámicas son las que tienen la propiedad **isDynamic** establecida en **true** en la página **Retransmisiones**. En el ejemplo siguiente, **echoservice** es una retransmisión WCF dinámica y su definición se debe quitar de la plantilla. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Retransmisiones dinámicas":::

## <a name="move"></a>Mover
Implemente la plantilla para crear un espacio de nombres de Relay en la región de destino. 

1. En Azure Portal, haga clic en **Crear un recurso**.
2. En **Buscar en Marketplace**, escriba **implementación de plantillas** para el texto de búsqueda, seleccione **Template Deployment (implementar mediante plantillas personalizadas)** y, a continuación, presione **Entrar**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Nueva implementación de plantilla":::    
1. En la página **Implementación de plantillas**, seleccione **Crear**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Nueva implementación de plantilla, botón Crear":::        
1. En la página **Implementación personalizada**, seleccione **Cree su propia plantilla en el editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Cree su propia plantilla en el editor, vínculo":::            
1. En la página **Editar plantilla**, seleccione **Cargar archivo** en la barra de herramientas y, después, siga las instrucciones para cargar el archivo **template.json** que descargó en la última sección.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Seleccionar plantilla":::                
1. Seleccione **Guardar** para guardar la plantilla. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Guardar plantilla":::                    
1. En la página **Implementación personalizada**, siga estos pasos: 
    1. Seleccione una **suscripción** de Azure. 
    2. Seleccione un **grupo de recursos** existente o cree uno. 
    3. Seleccione la **ubicación** o región de destino. Si ha seleccionado un grupo de recursos existente, esta configuración es de solo lectura. 
    4. Escriba un nuevo **nombre para el espacio de nombres**.
    1. Seleccione **Revisar + crear**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Implementación de la plantilla de Resource Manager":::
    1. En la página **Revisar y crear**, seleccione **Crear** en la parte inferior de la página. 
    
## <a name="verify"></a>Comprobar
1. Cuando la implementación sea correcta, seleccione **Ir al grupo de recursos**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Vínculo Ir al grupo de recursos":::    
1. En la página **Grupo de recursos**, seleccione el espacio de nombres de Azure Relay. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Selección de un espacio de nombres de Azure Relay":::    
1. En la página de **espacio de nombres de Azure Relay**, seleccione **Conexiones híbridas** o **Retransmisiones WCF** en el menú de la izquierda para comprobar que se crean conexiones híbridas y retransmisiones WCF. Si olvidó eliminar las definiciones de retransmisiones WCF dinámicas antes de importar la plantilla, elimínelas en la página **Retransmisiones WCF**. Las retransmisiones WCF dinámicas se crean automáticamente cuando los clientes se conectan al espacio de nombres de Relay. 

## <a name="discard-or-clean-up"></a>Descarte o limpieza
Después de la implementación, si quiere empezar de nuevo, puede eliminar el **espacio de nombres de Azure Relay de destino** y repetir los pasos descritos en las secciones [Preparación](#prepare) y [Mover](#move) de este artículo.

Para confirmar los cambios y completar el traslado de un espacio de nombres, elimine el **espacio de nombres de Azure Relay** en la región de origen. 

Para eliminar un espacio de nombres de Azure Relay (origen o destino) mediante Azure Portal:

1. En la ventana de búsqueda situada en la parte superior de Azure Portal, escriba **Retransmisiones** y seleccione **Retransmisiones** en **Servicios** en los resultados de la búsqueda. Verá todos los espacios de nombres de Azure Relay en una lista.
2. Seleccione el espacio de nombres de destino que desea eliminar para abrir la página **Retransmisión**. 
1. En la página **Retransmisión**, seleccione **Eliminar** en la barra de herramientas. 

    ![Eliminar espacio de nombres - botón](./media/move-across-regions/delete-namespace-button.png)
3. En la página **Eliminar espacio de nombres**, escriba el nombre del espacio de nombres de Azure Relay para confirmar la eliminación y, a continuación, seleccione **Eliminar**. 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha trasladado un espacio de nombres de Azure Relay desde una región a otra. Para obtener más información sobre cómo trasladar recursos entre regiones y la recuperación ante desastres en Azure, consulte:

- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/management/move-resource-group-and-subscription.md)
