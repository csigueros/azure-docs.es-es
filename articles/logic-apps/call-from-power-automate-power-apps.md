---
title: Llamada a aplicaciones lógicas desde Power Automate y Power Apps
description: Llame a las aplicaciones lógicas desde los flujos de Microsoft Power Automate mediante la exportación de aplicaciones lógicas como conectores.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 0c66f4bd7e311bdaedefe68327fc34d373fc6fec
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301661"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Llamada a aplicaciones lógicas desde Power Automate y Power Apps

Para llamar a las aplicaciones lógicas desde Microsoft Power Automate y Microsoft Power Apps, puede exportar las aplicaciones lógicas como conectores. Al exponer una aplicación lógica como un conector personalizado en un entorno de Power Automate o Power Apps, puede llamar a la aplicación lógica desde los flujos allí contenidos.

Si por el contrario desea migrar el flujo desde Power Automate o Power Apps a Logic Apps, consulte [Exportación de flujos desde Power Automate y su implementación en Azure Logic Apps](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> No todos los conectores de Power Automate están disponibles en Azure Logic Apps. Solo se pueden migrar flujos de Power Automate que tengan conectores equivalentes en Azure Logic Apps. Por ejemplo, el desencadenador Button, el conector Approvals y el conector Notification son específicos de Power Automate. En la actualidad, los flujos basados en OpenAPI de Power Automate no se admiten para la exportación e implementación como plantillas de aplicación lógica.
>
> * Para saber qué conectores de Power Automate carecen de equivalente en Logic Apps, vea [Conectores de Power Automate](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Para saber qué conectores de Logic Apps carecen de equivalente en Power Automate, vea [Conectores de Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* Una licencia de Power Automate o Power Apps.

* Una aplicación lógica con un desencadenador de solicitud para exportar.

* Un flujo de Power Automate o Power Apps desde el que desea llamar a la aplicación lógica.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Exportación de la aplicación lógica como un conector personalizado

Antes de poder llamar a la aplicación lógica desde Power Automate o Power Apps, primero debe exportar la aplicación lógica como un conector personalizado.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda de Azure Portal, escriba `Logic Apps`. En los resultados, en **Servicios**, seleccione **Logic Apps**.

1. Seleccione la aplicación lógica que desea exportar.

1. En el menú de la aplicación lógica, seleccione **Exportar**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Captura de pantalla de la página de la aplicación lógica en Azure Portal que muestra el menú con el botón &quot;Exportar&quot; seleccionado.":::

1. En el panel **Exportar**, en **Nombre**, escriba un nombre para el conector personalizado a la aplicación lógica. En la lista **Entorno**, seleccione el entorno Power Automate o Power Apps desde el que desea llamar a la aplicación lógica. Cuando finalice, seleccione **Aceptar**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Captura de pantalla del panel de exportación de la aplicación lógica que muestra los campos obligatorios para el entorno y el nombre del conector personalizado.":::

1. Para confirmar que la aplicación lógica se exportó correctamente, compruebe el panel de notificaciones.

### <a name="exporting-errors"></a>Exportación de errores

Estos son los errores que pueden producirse al exportar la aplicación lógica como un conector personalizado y sus soluciones sugeridas:

* **No se pudieron obtener los entornos. Asegúrese de que su cuenta está configurada para Power Automate y vuelva a intentarlo.** : compruebe que la cuenta de Azure tiene un plan de Power Automate.

* **No se puede exportar la aplicación lógica actual. Para exportar, seleccione una aplicación lógica que tenga un desencadenador de solicitud.** : compruebe que la aplicación lógica comienza con un [desencadenador de solicitud](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Conexión a la aplicación lógica desde Power Automate

Para conectarse a la aplicación lógica que exportó con el flujo de Power Automate:

1. Inicie sesión en [Power Automate](https://flow.microsoft.com).

1. En el menú de la página principal de **Power Automate**, seleccione **Mis flujos**.

1. En la página **Flujos**, seleccione el flujo que desea conectar a la aplicación lógica.

1. En el menú de la página de flujo, seleccione **Editar**.

1. En el editor de flujos, seleccione **&#43; Nuevo paso**.

1. En **Elegir una acción**, en el cuadro de búsqueda, escriba el nombre del conector de la aplicación lógica. Opcionalmente, para mostrar solo los conectores personalizados de su entorno, filtre los resultados seleccionando la pestaña **Personalizado**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Captura de pantalla del editor de flujos de Power Automate que muestra la asignación de un nuevo paso para el conector personalizado y las acciones disponibles.":::

1. Seleccione la acción que desea realizar con el conector de la aplicación lógica. 

1. Proporcione la información que la acción pasa al conector de la aplicación lógica.

1. Para guardar los cambios, en el menú del editor de Power Automate, seleccione **Guardar**.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el servicio Logic Apps, busque la aplicación lógica que exportó.

1. Confirme que la aplicación lógica funciona de la forma esperada en el flujo de Power Automate.

## <a name="delete-logic-app-connector-from-power-automate"></a>Eliminación del conector de la aplicación lógica de Power Automate

1. Inicie sesión en [Power Automate](https://flow.microsoft.com).

1. En la página principal de **Power Automate**, seleccione **Datos** &gt; **Conectores personalizados** en el menú.

1. En la lista, busque el conector personalizado y seleccione el botón de puntos suspensivos ( **...** ) &gt; **Eliminar**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Captura de pantalla de la página &quot;Conectores personalizados&quot; de Power Automate que muestra los botones de administración de conectores personalizados de la aplicación lógica.":::

1. Para confirmar la eliminación, haga clic en **Aceptar**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Conexión a la aplicación lógica desde Power Apps

Para conectarse a la aplicación lógica que exportó con el flujo de Power Apps:

1. Inicie sesión en [Power Apps](https://powerapps.microsoft.com/).

1. En la página principal de **Power Apps**, seleccione **Flujos** en el menú.

1. En la página **Flujos**, seleccione el flujo que desea conectar a la aplicación lógica.

1. En la página del flujo, seleccione **Editar** en el menú del flujo.

1. En el editor de flujos, seleccione el botón **&#43; Nuevo paso**.

1. En **Elegir una acción**, en el nuevo paso, escriba el nombre del conector de la aplicación lógica en el cuadro de búsqueda. Opcionalmente, filtre los resultados mediante la pestaña **Personalizado** para ver solamente los conectores personalizados de su entorno.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Captura de pantalla del editor de flujos de Power Apps que muestra la asignación de un nuevo paso para el conector personalizado y las acciones disponibles.":::

1. Seleccione la acción que desea realizar con el conector. 

1. Configure qué información pasa la acción al conector de la aplicación lógica.

1. En el menú del editor de Power Apps, seleccione **Guardar** para guardar los cambios. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el servicio Logic Apps, busque la aplicación lógica que exportó.

1. Confirme que la aplicación lógica funciona según lo previsto con el flujo de Power Apps.

## <a name="delete-logic-app-connector-from-power-apps"></a>Eliminación del conector de la aplicación lógica de Power Apps

1. Inicie sesión en [Power Apps](https://powerapps.microsoft.com).

1. En la página principal de **Power Apps**, seleccione **Datos** &gt; **Conectores personalizados** en el menú.

1. En la lista, busque el conector personalizado y seleccione el botón de puntos suspensivos ( **...** ) &gt; **Eliminar**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Captura de pantalla de la página &quot;Conectores personalizados&quot; de Power Apps que muestra los botones de administración de conectores personalizados de la aplicación lógica.":::

1. Para confirmar la eliminación, haga clic en **Aceptar**.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [conectores para Azure Logic Apps](../connectors/apis-list.md)
* Obtenga más información sobre [Azure Logic Apps](../logic-apps/logic-apps-overview.md).
