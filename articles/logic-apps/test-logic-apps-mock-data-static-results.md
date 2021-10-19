---
title: Simulación de flujos de trabajo de prueba
description: Configure datos ficticios para probar flujos de trabajo en Azure Logic Apps sin que ello afecte a los entornos de producción.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/08/2021
ms.openlocfilehash: 4167dcffe0a1b4db50f6d1580ad6284f2cf9321d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712618"
---
# <a name="test-workflows-with-mock-data-in-azure-logic-apps-preview"></a>Prueba de flujos de trabajo con datos ficticios en Azure Logic Apps (versión preliminar)

> [!NOTE]
> Esta funcionalidad está en versión preliminar y está sujeta a las [Condiciones de uso complementarias para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para probar los flujos de trabajo sin llamar o acceder realmente a aplicaciones, datos, servicios o sistemas en directo, puede configurar y devolver valores ficticios de las acciones. Por ejemplo, es posible que quiera probar distintas rutas de acceso de acción en función de varias condiciones, forzar errores, proporcionar cuerpos de respuesta de mensajes específicos o incluso intentar omitir algunos pasos. Configurar las pruebas de datos ficticios de una acción no hace que esta se ejecute, sino que devuelve datos ficticios.

Por ejemplo, si configura datos ficticios para la acción de envío de correo electrónico de Outlook 365, Azure Logic Apps solo devuelve los datos ficticios que proporcionó, en lugar de llamar a Outlook y enviar un correo electrónico.

En este artículo se muestra cómo configurar datos ficticios en una acción en un flujo de trabajo para el tipo de recurso [**Aplicación lógica (consumo)** y **Aplicación lógica (estándar)** ](logic-apps-overview.md#resource-environment-differences). Puede encontrar ejecuciones de flujo de trabajo anteriores que usan estos datos ficticios y reutilizar las salidas de acción existentes como datos ficticios.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta y una suscripción de Azure. Si aún no tiene una, <a href="https://azure.microsoft.com/free/?WT.mc_id=A261C142F" target="_blank">regístrese para obtener una cuenta de Azure gratuita</a>.

* El recurso de aplicación lógica y el flujo de trabajo en los que quiere configurar datos ficticios. En este artículo se usa un desencadenador **Periodicidad** y una acción **HTTP** como flujo de trabajo de ejemplo.

  Si nunca trabajó con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps](logic-apps-overview.md) y el artículo sobre [Inicio rápido: Creación de su primer flujo de trabajo de aplicación lógica](quickstart-create-first-logic-app-workflow.md).

<a name="enable-mock-data"></a>

## <a name="enable-mock-data-output"></a>Habilitación de la salida de datos ficticios

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. Haga lo siguiente en la acción donde quiera devolver los datos ficticios:

   1. En la esquina superior derecha de la acción, seleccione el botón de puntos suspensivos ( *...* ) y, a continuación, seleccione **Pruebas**, por ejemplo:

      ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo, el menú de acceso directo de acciones y la opción "Pruebas" seleccionada.](./media/test-logic-apps-mock-data-static-results/select-testing.png)

   1. En el panel **Pruebas,** seleccione **Habilitar el resultado estático (versión preliminar)** . Cuando aparezcan las propiedades necesarias (*) de la acción, especifique los valores de salida ficticios que desea devolver como respuesta de la acción.

      Las propiedades difieren en función del tipo de acción seleccionado. Por ejemplo, la acción HTTP tiene las siguientes propiedades necesarias:

      | Propiedad | Descripción |
      |----------|-------------|
      | **Estado** | Estado de la acción que se va a devolver. |
      | **Código de estado** | Código de estado específico que se va a devolver como salida. |
      | **Encabezados** | Contenido de encabezado que se va a devolver. |
      |||

      ![Captura de pantalla que muestra el panel "Pruebas" después de seleccionar "Habilitar el resultado estático".](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      > [!TIP]
      > Para escribir los valores con formato de notación de objetos JavaScript (JSON), seleccione **Cambiar al modo JSON** (![Icono para "Cambiar al modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. En las propiedades opcionales, abra la lista **Seleccionar campos opcionales** y elija las propiedades que quiera simular.

      ![Captura de pantalla que muestra el panel "Pruebas" con la lista "Seleccionar campos opcionales" abierta.](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Cuando lo tenga todo preparado, seleccione **Listo**.

   En la esquina superior derecha de la acción, la barra de título ahora muestra un icono de vaso de precipitados de prueba (![icono del resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), que indica que ha habilitado los resultados estáticos.

   ![Captura de pantalla que muestra una acción con el icono de resultados estáticos.](./media/test-logic-apps-mock-data-static-results/static-result-enabled.png)

   Para buscar ejecuciones de flujo de trabajo donde se hayan usado datos simulados, revise [Buscar ejecuciones donde se usan resultados estáticos](#find-runs-mock-data) más adelante en este tema.

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el diseñador, seleccione la acción en la que desea devolver datos ficticios para que aparezca el panel de detalles de la acción.

1. Una vez que se abra el panel de detalles de la acción en el lado derecho, seleccione **Pruebas**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo, el panel de detalles de acción y la opción "Pruebas" seleccionada.](./media/test-logic-apps-mock-data-static-results/select-testing-standard.png)

1. En la pestaña **Pruebas,** seleccione **Habilitar el resultado estático (versión preliminar)** . Cuando aparezcan las propiedades necesarias (*) de la acción, especifique los valores de salida ficticios que desea devolver como respuesta de la acción.

   Las propiedades difieren en función del tipo de acción seleccionado. Por ejemplo, la acción HTTP tiene las siguientes propiedades necesarias:

   | Propiedad | Descripción |
   |----------|-------------|
   | **Estado** | Estado de la acción que se va a devolver. |
   | **Código de estado** | Código de estado específico que se va a devolver como salida. |
   | **Encabezados** | Contenido de encabezado que se va a devolver. |
   |||

   ![Captura de pantalla que muestra la pestaña "Pruebas" después de seleccionar "Habilitar el resultado estático".](./media/test-logic-apps-mock-data-static-results/enable-static-result-standard.png)

   > [!TIP]
   > Para escribir los valores con formato de notación de objetos JavaScript (JSON), seleccione **Cambiar al modo JSON** (![Icono para "Cambiar al modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

1. En las propiedades opcionales, abra la lista **Seleccionar campos opcionales** y elija las propiedades que quiera simular.

   ![Captura de pantalla que muestra el panel "Pruebas" con la lista "Seleccionar campos opcionales" abierta.](./media/test-logic-apps-mock-data-static-results/optional-properties-standard.png)

1. Cuando lo tenga todo preparado, seleccione **Listo**.

   La esquina inferior derecha de la acción ahora muestra un icono de vaso de precipitados de prueba (![icono del resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), que indica que ha habilitado los resultados estáticos.

   ![Captura de pantalla que muestra una acción con el icono de resultados estáticos en el diseñador.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-standard.png)

   Para buscar ejecuciones de flujo de trabajo donde se hayan usado datos simulados, revise [Buscar ejecuciones donde se usan resultados estáticos](#find-runs-mock-data) más adelante en este tema.

---

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-mock-data"></a>Buscar ejecuciones que usan datos ficticios

### <a name="consumption"></a>[Consumo](#tab/consumption)

Para buscar ejecuciones de flujo de trabajo anteriores en las que las acciones usan datos ficticios, revise el historial de ejecución de ese flujo de trabajo.

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el menú de recursos de la aplicación lógica, seleccione **Introducción**.

1. En la sección **Essentials,** seleccione **Historial de ejecuciones**, si aún no está seleccionado.

1. En la tabla, **Historial de ejecuciones**, busque la columna **Resultados estáticos**.

   Las ejecuciones que incluyan acciones con salida de datos ficticios tienen la columna **Resultados estáticos** establecida en **Habilitado**, por ejemplo:

   ![Captura de pantalla que muestra el historial de ejecución del flujo de trabajo con la columna "Resultados estáticos".](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para ver esas acciones en una ejecución que usa datos ficticios, seleccione la ejecución que quiera donde la columna **Resultados estáticos** esté establecida en **Habilitado**.

   Las acciones que utilizan resultados estáticos muestran el icono del vaso de precipitados de prueba (![icono del resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), por ejemplo:

   ![Captura de pantalla que muestra el historial de ejecución del flujo de trabajo con acciones que usan resultados estáticos.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-run-details.png)

### <a name="standard"></a>[Estándar](#tab/standard)

Para buscar otras ejecuciones de flujo de trabajo en las que las acciones usan datos ficticios, debe comprobar cada ejecución.

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el menú del flujo de trabajo, seleccione **Información general**.

1. En la sección **Essentials,** seleccione **Historial de ejecuciones**, si aún no está seleccionado.

1. En la tabla **Historial de ejecuciones**, seleccione la ejecución que desee revisar.

   ![Captura de pantalla que muestra el historial de ejecución del flujo de trabajo.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. En el panel de detalles de ejecución, compruebe si alguna acción muestra el icono del vaso de precipitados de prueba (![icono del resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), por ejemplo:

   ![Captura de pantalla que muestra el historial de ejecución del flujo de trabajo con acciones que usan resultados estáticos.](./media/test-logic-apps-mock-data-static-results/run-history-static-result-standard.png)

---

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs-as-mock-data"></a>Reutilización de salidas anteriores como datos ficticios

Si tiene una ejecución de flujo de trabajo anterior con salidas, puede reutilizar estas salidas como datos ficticios copiando y pegando esas salidas de esa ejecución.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el menú de recursos de la aplicación lógica, seleccione **Introducción**.

1. En la sección **Essentials,** seleccione **Historial de ejecuciones**, si aún no está seleccionado. Seleccione la ejecución del flujo de trabajo que desee en la lista que aparece.

   ![Captura de pantalla que muestra el historial de ejecución del flujo de trabajo.](./media/test-logic-apps-mock-data-static-results/select-run.png)

1. Una vez que se abra el panel de detalles de ejecución, expanda la acción que tenga las salidas que desee.

1. En la sección **Salidas**, seleccione **Mostrar salidas sin procesar**.

1. En el panel **Salidas**, copie el objeto de notación de objetos JavaScript (JSON) completo o la subsección específica que quiera usar, por ejemplo, la sección de salidas o, incluso, únicamente la sección de encabezados.

1. Revise la sección anterior sobre cómo [configurar datos ficticios](#enable-mock-data) para una acción y siga los pasos para abrir el panel **Pruebas** de la acción.

1. Una vez que se abra el panel **Pruebas**, elija cualquiera de los pasos:

   * Para pegar un objeto JSON completo, junto a la etiqueta **Pruebas,** seleccione **Cambiar al modo JSON** (![icono para "Cambiar al modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Captura de pantalla que muestra el icono "Cambiar al modo JSON" seleccionado para pegar el objeto JSON completo.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Para pegar simplemente una sección JSON, junto a la etiqueta de la sección en cuestión, por ejemplo **Salida** or **Encabezados**, seleccione **Cambiar al modo JSON**, por ejemplo:

     ![Captura de pantalla que muestra el icono "Cambiar al modo JSON" seleccionado para pegar una sección de un objeto JSON.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output.png)

1. En el editor de JSON, pegue el JSON copiado previamente.

   ![Captura de pantalla que muestra el objeto JSON pegado en el editor.](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Cuando haya finalizado, seleccione **Listo**. Si lo que quiere es volver al diseñador, seleccione **Cambiar el modo de editor** (![icono "Cambiar el modo de editor"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el menú del flujo de trabajo, seleccione **Información general**.

1. En la sección **Essentials,** seleccione **Historial de ejecuciones**, si aún no está seleccionado.

1. En la tabla **Historial de ejecuciones**, seleccione la ejecución que desee revisar.

   ![Captura de pantalla que muestra el historial de ejecución del flujo de trabajo.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. Una vez que se abra el panel de detalles de ejecución, seleccione la acción que tenga las salidas que desee.

1. En la sección **Salidas**, seleccione **Mostrar salidas sin procesar**.

1. En el panel **Salidas**, copie el objeto de notación de objetos JavaScript (JSON) completo o la subsección específica que quiera usar, por ejemplo, la sección de salidas o, incluso, únicamente la sección de encabezados.

1. Revise la sección anterior sobre cómo [configurar datos ficticios](#enable-mock-data) para una acción y siga los pasos para abrir la pestaña **Pruebas** de la acción.

1. Una vez que se abra la pestaña **Pruebas**, elija cualquiera de los pasos:

   * Para pegar un objeto JSON completo, junto a la etiqueta **Pruebas,** seleccione **Cambiar al modo JSON** (![icono para "Cambiar al modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Captura de pantalla que muestra el icono "Cambiar al modo JSON" seleccionado para pegar el objeto JSON completo.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete-standard.png)

   * Para pegar simplemente una sección JSON, junto a la etiqueta de la sección en cuestión, por ejemplo **Salida** or **Encabezados**, seleccione **Cambiar al modo JSON**, por ejemplo:

     ![Captura de pantalla que muestra el icono "Cambiar al modo JSON" seleccionado para pegar una sección de un objeto JSON.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output-standard.png)

1. En el editor de JSON, pegue el JSON copiado previamente.

   ![Captura de pantalla que muestra el objeto JSON pegado en el editor.](./media/test-logic-apps-mock-data-static-results/json-editing-mode-standard.png)

1. Cuando haya finalizado, seleccione **Listo**. Si lo que quiere es volver al diseñador, seleccione **Cambiar el modo de editor** (![icono "Cambiar el modo de editor"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

---

## <a name="disable-mock-data"></a>Deshabilitación de datos ficticios

Al desactivar los resultados estáticos en una acción no se quitan los valores de la última configuración. Por lo tanto, si vuelve a activar el resultado estático en la misma acción, puede seguir usando los valores anteriores.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador. Busque la acción donde quiera deshabilitar los datos ficticios.

1. En la esquina superior derecha de la acción, seleccione el icono del vaso de precipitados (![icono del resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)).

   ![Captura de pantalla que muestra la acción y el icono del vaso de precipitados de prueba seleccionados.](./media/test-logic-apps-mock-data-static-results/disable-static-result.png)

1. Seleccione **Deshabilitar el resultado estático** > **Hecho**.

   ![Captura de pantalla que muestra la opción "Deshabilitar el resultado estático" seleccionada.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button.png)

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador. Seleccione la acción donde quiera deshabilitar los datos ficticios.

1. En el panel de detalles de la acción, seleccione la pestaña **Pruebas**.

1. Seleccione **Deshabilitar el resultado estático** > **Hecho**.

   ![Captura de pantalla que muestra la opción "Deshabilitar el resultado estático" seleccionada para Estándar.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button-standard.png)

---

## <a name="reference"></a>Referencia

Para obtener más información sobre esta configuración en las definiciones de flujo de trabajo subyacentes, vea [Resultados estáticos: referencia de esquema de lenguaje de definición de flujo de trabajo](logic-apps-workflow-definition-language.md#static-results) y [runtimeConfiguration.staticResult: opciones de configuración en tiempo de ejecución](logic-apps-workflow-actions-triggers.md#runtime-configuration-settings).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre [Azure Logic Apps](logic-apps-overview.md).