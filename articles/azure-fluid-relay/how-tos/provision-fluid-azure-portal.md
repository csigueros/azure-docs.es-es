---
title: 'Procedimiento: Aprovisionamiento de un servicio Azure Fluid Relay'
description: Aprovisionamiento de un servicio Azure Fluid Relay mediante Azure Portal
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 29397802e0f4e4c4a47e82db8c1ef17bc89c7d9a
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661643"
---
# <a name="how-to-provision-an-azure-fluid-relay-service"></a>Procedimiento: Aprovisionamiento de un servicio Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Para poder conectar la aplicación a un servidor de Azure Fluid Relay, debe aprovisionar un recurso de servidor de Azure Fluid Relay en su cuenta de Azure. Este artículo le guía por los pasos necesarios para aprovisionar el servicio Azure Fluid Relay y prepararlo para su uso. 

## <a name="prerequisites"></a>Prerrequisitos

Para crear un servicio Azure Fluid Relay, debe tener una cuenta de Azure. Si no tiene una cuenta, puede [probar Azure gratis](https://azure.com/free).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos es una recopilación lógica de recursos de Azure. Todos los recursos se implementan y administran en un grupo de recursos. Para crear un grupo de recursos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el panel de navegación izquierdo, seleccione **Grupos de recursos**. A continuación, seleccione **Agregar**.

    :::image type="content" source="../images/add-resource-group.png" alt-text="Captura de pantalla de la página Grupos de recursos en Azure Portal.":::

3. En Suscripción seleccione el nombre de la suscripción de Azure en la que desea crear el grupo de recursos.

    :::image type="content" source="../images/create-resource-group.png" alt-text="Captura de pantalla de la página Crear grupos de recursos en Azure Portal.":::

1. Escriba un nombre único para el grupo de recursos. El sistema comprueba de forma inmediata para ver si el nombre está disponible en la suscripción de Azure seleccionada actualmente.
1. Seleccione una región para el grupo de recursos.
1. Seleccione **Revisar + crear**.
1. En la página Revisar + crear, seleccione **Crear**.

## <a name="create-a-fluid-relay-resource"></a>Creación de un recurso de Fluid Relay
Cada recurso de servidor de Azure Fluid Relay proporciona un inquilino para que lo use en la aplicación Fluid. Dentro de ese inquilino, puede crear muchos contenedores o sesiones. Para crear una instancia de Fluid Relay en el grupo de recursos mediante el portal:

1. En Azure Portal, seleccione **Crear un recurso** en la parte superior izquierda de la pantalla.
2. Busque "Fluid".
 
    :::image type="content" source="../images/marketplace-fluid-relay.png" alt-text="Captura de pantalla de la página Crear recurso con los resultados de la búsqueda del término &quot;Fluid&quot;.":::

3. Seleccione **Fluid Relay** y **Crear.**
 
    :::image type="content" source="../images/fluid-relay-details-page.png" alt-text="Captura de pantalla de la página de detalles del marketplace de Azure Fluid Relay.":::

4. En la página Crear, siga estos pasos:

    :::image type="content" source="../images/create-fluid-relay-server.png" alt-text="Captura de pantalla de cómo configurar un nuevo servidor de Azure Fluid Relay.":::

    1. Seleccione la suscripción en la que desea crear el espacio de nombres.
    2. Seleccione el grupo de recursos que ha creado en el paso anterior.
    3. Especifique un nombre para el recurso de Fluid Relay.
    4. Seleccione una ubicación para el espacio de nombres.
    
    > [!NOTE]
    > Durante la versión preliminar pública, solo se admiten las regiones Oeste de EE. UU. 2, Oeste de Europa y Sudeste de Asia.

5. Haga clic en el botón **Revisar y crear** situado en la parte inferior de la página.

6. En la página Revisar y crear, examine la configuración y seleccione *Crear*. Espere a que la implementación se complete.

    :::image type="content" source="../images/create-server-validation-complete.png" alt-text="Captura de pantalla de la nueva página de servicio después de que la validación se haya completado correctamente.":::

7. En la página Implementación, seleccione **Ir al recurso** para ir a la página de su espacio de nombres.

    :::image type="content" source="../images/deployment-complete.png" alt-text="Captura de pantalla de Azure Portal que indica que la implementación se ha completado.":::

8. Confirme que ve una página de Fluid Relay parecida a la de este ejemplo.

    :::image type="content" source="../images/resource-details.png" alt-text="Captura de pantalla de una página de detalles de ejemplo para un recurso de Fluid Relay implementado.":::

## <a name="next-steps"></a>Pasos siguientes
Acaba de crear un grupo de recursos y de aprovisionar un recurso de Azure Fluid Relay en ese grupo. A continuación, puede [conectarse al servicio Azure Fluid Relay en la aplicación](../quickstarts/quickstart-dice-roll.md).
