---
title: Acerca del diseñador de flujos de trabajo de inquilino único
description: Obtenga información sobre cómo el diseñador en el entorno de Azure Logic Apps de inquilino único le ayuda a crear visualmente flujos de trabajo desde Azure Portal. Descubra las ventajas y características de esta versión más reciente.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: ad280e8c052b14ce64734012c3677f8fd2ce5096
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129404"
---
# <a name="about-the-workflow-designer-in-single-tenant-azure-logic-apps"></a>Acerca del diseñador de flujos de trabajo en el entorno de Azure Logic Apps de inquilino único

Cuando trabaja con Azure Logic Apps en Azure Portal, puede editar los [*flujos de trabajo*](logic-apps-overview.md#workflow) de forma visual o mediante programación. Después de abrir un [recurso de *aplicación lógica*](logic-apps-overview.md#logic-app) en el portal, en el menú de recursos bajo **Desarrollador**, puede seleccionar entre la vista [**Código**](#code-view) y la vista **Diseñador**. Si desea desarrollar, editar y ejecutar visualmente el flujo de trabajo, seleccione la vista de diseñador. Puede cambiar entre la vista de diseñador y la vista de código en cualquier momento.

> [!IMPORTANT]
> Actualmente, la versión más reciente del diseñador solo está disponible para los recursos de aplicación lógica *estándar*, que se ejecutan en el entorno de Azure Logic Apps de *inquilino único*. Para obtener más información sobre los distintos tipos de recursos y entornos en tiempo de ejecución en Logic Apps, revise [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](single-tenant-overview-compare.md).

:::image type="content" source="./media/designer-overview/choose-developer-view.png" alt-text="Captura de pantalla de una página de recursos de aplicación lógica en Azure Portal, en la que se muestran las opciones de barra lateral para ver un flujo de trabajo en la vista Código o Diseñador.":::

Al seleccionar la vista **Diseñador**, el flujo de trabajo se abre en el diseñador de flujos de trabajo.

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="Captura de pantalla donde se muestra el diseñador de flujos de trabajo y un flujo de trabajo empresarial de ejemplo que usa modificadores y condiciones." lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta y una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/).
- Un recurso de aplicación lógica *estándar* [en un entorno de inquilino único](single-tenant-overview-compare.md). Para más información, consulte [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md).
- Un flujo de trabajo para la aplicación lógica de inquilino único.

## <a name="latest-version-features"></a>Características de la versión más reciente

El diseñador de flujos de trabajo más reciente ofrece una nueva experiencia con destacadas características y ventajas; por ejemplo:

- Un nuevo motor de diseño que admite flujos de trabajo más complicados. 
- Puede crear y ver flujos de trabajo complicados de forma limpia y sencilla, gracias al nuevo motor de diseño, un lienzo más compacto y actualizaciones del diseño basado en tarjetas.
- Los pasos se agregan y se editan mediante paneles independientes del diseño del flujo de trabajo. Este cambio proporciona un lienzo más limpio y claro para ver el diseño del flujo de trabajo. Para obtener más información, consulte [Incorporación de pasos a los flujos de trabajo](#add-steps-to-workflows).
- Puede moverse entre los pasos del flujo de trabajo en el diseñador mediante la navegación con teclado.
  - Para desplazarse a la tarjeta siguiente: **Ctrl** + **Flecha abajo (&darr;)**
  - Para desplazarse a la tarjeta anterior: **Ctrl** + **Flecha arriba (&uarr;)**

## <a name="add-steps-to-workflows"></a>Incorporación de pasos a los flujos de trabajo

El diseñador de flujos de trabajo proporciona una manera visual de agregar, editar y eliminar pasos en el flujo de trabajo. Como primer paso del flujo de trabajo, agregue siempre un [*desencadenador*](logic-apps-overview.md#trigger). Después, complete el flujo de trabajo agregando una o varias [*acciones*](logic-apps-overview.md#action).

Para agregar el desencadenador o una acción al flujo de trabajo, siga estos pasos:

1. Abra el flujo de trabajo en el diseñador.
1. En el diseñador, seleccione **Elegir una operación**, que abre un panel denominado **Agregar un desencadenador** o **Agregar una acción**. 
1. En el panel abierto, busque una operación filtrando la lista de las maneras siguientes:
    1. Escriba un servicio, conector o categoría en la barra de búsqueda para mostrar las operaciones relacionadas. Por ejemplo, `Azure Cosmos DB` o `Data Operations`. 
    1. Si sabe cuál es la operación específica que desea usar, introduzca el nombre en la barra de búsqueda. Por ejemplo, `Call an Azure function` o `When an HTTP request is received`.
    1. Seleccione la pestaña **Integrado** para mostrar solo las categorías de [*operaciones integradas*](logic-apps-overview.md#built-in-operations). O bien, seleccione la pestaña **Azure** para mostrar otras categorías de operaciones disponibles por medio de Azure.
    1. Solo puede ver desencadenadores o acciones seleccionando las pestañas **Desencadenadores** o **Acciones**. Sin embargo, solo puede agregar un desencadenador como primer paso y una acción como paso siguiente. En función de la categoría de operación, pueden que solo estén disponibles desencadenadores o acciones.
    :::image type="content" source="./media/designer-overview/designer-add-operation.png" alt-text="Captura de pantalla del diseñador de Logic Apps en Azure Portal, en la que se muestra un flujo de trabajo que se está editando para agregar una nueva operación." lightbox="./media/designer-overview/designer-add-operation.png":::
1. Seleccione la operación que desea usar. 
    :::image type="content" source="./media/designer-overview/designer-filter-operations.png" alt-text="Captura de pantalla del diseñador de Logic Apps, que muestra un panel de posibles operaciones que se pueden filtrar por servicio o nombre." lightbox="./media/designer-overview/designer-filter-operations.png":::
1. Configure el desencadenador o la acción según sea necesario.
    1. Los campos obligatorios tienen un asterisco rojo (&ast;) delante del nombre.
    1. Algunos desencadenadores y acciones pueden requerir que cree una conexión a otro servicio. Es posible que tenga que iniciar sesión en una cuenta o escribir las credenciales de un servicio. Por ejemplo, si desea usar el conector de Office 365 Outlook para enviar un correo electrónico, debe autorizar su cuenta de correo electrónico de Outlook.
    1. Algunos desencadenadores y acciones usan contenido dinámico, en el que puede seleccionar variables en lugar de escribir información o expresiones.
1. Para guardar los cambios, seleccione **Guardar** en la barra de herramientas. Este paso también comprueba que el flujo de trabajo es válido. 

## <a name="code-view"></a>vista Código

La vista **Código** permite editar directamente el archivo de definición de flujo de trabajo en formato JSON. Asegúrese de seleccionar **Guardar** para guardar los cambios que realice en esta vista. 

> [!TIP]
> La vista **Código** también es una manera sencilla de buscar y copiar la definición de flujo de trabajo, en lugar de usar la interfaz de la línea de comandos de Azure (CLI de Azure) u otros métodos.

:::image type="content" source="./media/designer-overview/code-view.png" alt-text="Captura de pantalla de un flujo de trabajo de Logic Apps en la vista Código, en la que se muestra la definición de flujo de trabajo JSON que se está editando en Azure Portal.":::


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md)
