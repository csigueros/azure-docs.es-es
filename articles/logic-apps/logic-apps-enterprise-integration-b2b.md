---
title: Intercambio de mensajes B2B mediante flujos de trabajo
description: Intercambio de mensajes entre asociados comerciales mediante la creación de flujos de trabajo con Azure Logic Apps y Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/17/2021
ms.openlocfilehash: 6c9580a051a7473e7009cf8df8d2ea8759834683
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351162"
---
# <a name="exchange-b2b-messages-between-partners-using-workflows-in-azure-logic-apps"></a>Intercambio de mensajes B2B entre asociados que utilizan flujos de trabajo en Azure Logic Apps

Si tiene una cuenta de integración que define entidades y contratos, puede crear un flujo de trabajo de negocio a negocio (B2B) automatizado que intercambie mensajes entre asociados comerciales mediante Azure Logic Apps. El flujo de trabajo puede utilizar conectores que admiten protocolos estándar del sector, como AS2, X12, EDIFACT y RosettaNet. También puede incluir operaciones proporcionadas por otros [conectores en Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors), como Office 365 Outlook, SQL Server y Salesforce.

En este artículo se muestra cómo crear un ejemplo de flujo de trabajo de aplicación lógica que puede recibir solicitudes HTTP mediante un desencadenador **Solicitud**, descodificar el contenido del mensaje mediante las acciones **Descodificación AS2** y **Descodificar X12** y devolver luego una respuesta mediante la acción **Respuesta**. En este ejemplo se usa el diseñador de flujos de trabajo en Azure Portal, pero puede seguir pasos similares para el diseñador de flujos de trabajo en Visual Studio.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Si usa el [tipo de recurso **Aplicación lógica (consumo)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración necesita un [vínculo al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account) para poder utilizar los artefactos en el flujo de trabajo.

  * Si utiliza el [tipo de recurso **Aplicación lógica (estándar)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración no necesita un vínculo al recurso de aplicación lógica, pero sigue siendo necesaria para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) o [EDIFACT](logic-apps-enterprise-integration-edifact.md). La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

  > [!NOTE]
  > Actualmente, solo el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [Consumo]) admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Logic App (Standard)** (Aplicación lógica [Estándar]) no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Al menos dos [asociados comerciales](logic-apps-enterprise-integration-partners.md) en la cuenta de integración. Las definiciones de ambos asociados deben usar el mismo calificador de *identidad empresarial*, que es AS2, X12, EDIFACT o RosettaNet.

* Un [contrato AS2 y un contrato X12](logic-apps-enterprise-integration-agreements.md) para los asociados que se utilizan en este flujo de trabajo. En cada contrato hacen falta un asociado anfitrión y un asociado invitado.

* Un recurso de aplicación lógica con un flujo de trabajo en blanco donde puede agregar el desencadenador [Solicitud](../connectors/connectors-native-reqres.md) y luego las siguientes acciones:

  * [Descodificación con AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condición](../logic-apps/logic-apps-control-flow-conditional-statement.md), que envía una [Respuesta](../connectors/connectors-native-reqres.md) en función de si la acción de descodificación de AS2 se realiza correctamente o no.

  * [Descodificar mensaje X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

<a name="add-request-trigger"></a>

## <a name="add-the-request-trigger"></a>Adición del desencadenador Request

Para iniciar el flujo de trabajo en este ejemplo, agregue el desencadenador Solicitud.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en blanco en el diseñador de flujos de trabajo.

1. En el cuadro de búsqueda del diseñador, seleccione **Todo** si no está seleccionado. En el cuadro de búsqueda, escriba `when a http request`. Seleccione el desencadenador Solicitud denominado **Cuando se recibe una solicitud HTTP**.

   ![Captura de pantalla que muestra Azure Portal y el diseñador multiinquilino con "Cuando se recibe una solicitud HTTP" en el cuadro de búsqueda y el desencadenador Solicitud seleccionado.](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-consumption.png)

1. En el desencadenador, deje vacío el cuadro **Esquema JSON de cuerpo de solicitud**.

   El motivo es que el desencadenador recibirá un mensaje X12 en formato de archivo plano.

   ![Captura de pantalla que muestra las propiedades del diseñador multiinquilino y del desencadenador Solicitud.](./media/logic-apps-enterprise-integration-b2b/request-trigger-consumption.png)

1. Cuando esté listo, seleccione **Guardar** en la barra de herramientas del diseñador.

   Este paso genera la **dirección URL de HTTP POST** que usará más adelante para enviar una solicitud que desencadena el flujo de trabajo de la aplicación lógica.

   ![Captura de pantalla que muestra el diseñador multiinquilino y la dirección URL generada para el desencadenador Solicitud.](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-consumption.png)

1. Copie y guárdela para usarla más adelante.

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en blanco en el diseñador de flujos de trabajo.

1. En el diseñador, seleccione **Elegir una operación**. En el cuadro de búsqueda, seleccione **Integrado**, si no está seleccionado. En el cuadro de búsqueda, escriba `when a http request`. Seleccione el desencadenador Solicitud denominado **Cuando se recibe una solicitud HTTP**.

   ![Captura de pantalla que muestra Azure Portal y el diseñador de inquilino único con "Cuando se recibe una solicitud HTTP" en el cuadro de búsqueda y el desencadenador Solicitud seleccionado.](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-standard.png)

1. En el desencadenador, deje vacío el cuadro **Esquema JSON de cuerpo de solicitud**.

   El motivo es que el desencadenador recibirá un mensaje X12 en formato de archivo plano.

   ![Captura de pantalla que muestra las propiedades del diseñador de inquilino único y del desencadenador Solicitud.](./media/logic-apps-enterprise-integration-b2b/request-trigger-standard.png)

1. Cuando esté listo, seleccione **Guardar** en la barra de herramientas del diseñador.

   Este paso genera la **dirección URL de HTTP POST** que usará más adelante para enviar una solicitud que desencadena el flujo de trabajo de la aplicación lógica.

   ![Captura de pantalla que muestra el diseñador de inquilino único y la dirección URL generada para el desencadenador Solicitud.](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-standard.png)

1. Copie y guárdela para usarla más adelante.

---

<a name="add-decode-as2-trigger"></a>

## <a name="add-the-decode-as2-action"></a>Incorporación de la acción Descodificación AS2

Ahora agregue las acciones B2B para este ejemplo, que utiliza las acciones AS2 y X12.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En el desencadenador, seleccione **Nuevo paso**.

   > [!TIP]
   > Para ocultar los detalles del desencadenador Solicitud, seleccione la barra de título del desencadenador.

   ![Captura de pantalla que muestra el diseñador multiinquilino y el desencadenador con "Nuevo paso" seleccionado.](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-consumption.png)

1. En el cuadro de búsqueda **Elegir una operación**, seleccione **Todo**, si no está seleccionado. En el cuadro de búsqueda, escriba `as2` y seleccione **Descodificación AS2**.

   ![Captura de pantalla que muestra el diseñador multiinquilino con la acción "Descodificación AS2" seleccionada.](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-consumption.png)

1. En la propiedad **Mensaje que se descodificará** de la acción, escriba la entrada que desea que descodifique la acción AS2, que es la salida `body` del desencadenador Solicitud. Hay varias maneras de especificar este contenido como entrada de la acción, ya sea seleccionándolo en la lista de contenido dinámico o como expresión:

   * Para seleccionar de una lista que muestre las salidas de desencadenador disponibles, haga clic dentro del cuadro **Mensaje que se descodificará**. Cuando aparezca la lista de contenido dinámico, en **Cuando se recibe una solicitud HTTP**, seleccione el valor de propiedad **Cuerpo**, por ejemplo:

     ![Captura de pantalla que muestra el diseñador multiinquilino con la lista de contenido dinámico y la propiedad "Cuerpo" seleccionada.](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-consumption.png)

     > [!TIP]
     > Si no aparece ninguna salida de desencadenador, en la lista de propiedades dinámicas, en **Cuando se recibe una solicitud HTTP**, seleccione **Ver más**.

   * Para especificar una expresión que haga referencia a la salida `body` del desencadenador, haga clic dentro del cuadro **Mensaje que se descodificará**. Después de que aparezca la lista de contenido dinámico, seleccione **Expresión**. En el editor de expresiones, escriba la expresión siguiente y seleccione **Aceptar**:

     `triggerOutputs()['body']`

     O bien, en el cuadro **Mensaje que se descodificará**, escriba directamente la expresión siguiente:

     `@triggerBody()`

     La expresión se resuelve en el token **Cuerpo**.

     ![Captura de pantalla que muestra el diseñador multiinquilino con la salida de la propiedad "Cuerpo" resuelta.](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-consumption.png)

1. En la propiedad **Encabezados de mensaje** de la acción, escriba los encabezados necesarios para la acción AS2, que se encuentran en la salida `headers` del desencadenador Solicitud.

   1. Para especificar una expresión que haga referencia a la salida `headers` del desencadenador, seleccione la opción para **cambiar los encabezados del mensaje al modo de texto**.

      ![Captura de pantalla que muestra el diseñador multiinquilino con la opción "Switch Message headers to text mode&quot; (&quot;Cambiar encabezados de mensaje al modo de texto") seleccionada.](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-consumption.png)

   1. Haga clic dentro del cuadro **Encabezados de mensaje**. Después de que aparezca la lista de contenido dinámico, seleccione **Expresión**. En el editor de expresiones, escriba la expresión siguiente y seleccione **Aceptar**:

      `triggerOutputs()['Headers']`

      En la acción **Descodificación AS2**, la expresión aparece ahora como un token:

      ![Captura de pantalla que muestra el diseñador multiinquilino y el cuadro "Encabezados de mensaje" con el token "@triggerOutputs()['Headers']".](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-consumption.png)

   1. Para que este token de expresión se resuelva en el token **Headers**, cambie entre la vista del diseñador y la vista de código. Después de este paso, la acción **Descodificación AS2** se muestra similar a la de este ejemplo:

      ![Captura de pantalla que muestra el diseñador multiinquilino y la salida de los encabezados resueltos del desencadenador.](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-consumption.png)

### <a name="standard"></a>[Estándar](#tab/standard)

1. En el desencadenador, seleccione **Permite agregar un nuevo paso** (signo más) y, a continuación, **Agregar una acción**.

   ![Captura de pantalla que muestra el diseñador de inquilino único y el desencadenador con el signo más seleccionado.](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-standard.png)

1. En el panel **Elegir una operación**, seleccione **Azure**, si no está seleccionado. En el cuadro de búsqueda, escriba `as2` y seleccione **Descodificar mensaje AS2**.

   ![Captura de pantalla que muestra el diseñador de inquilino único con la acción "Descodificar mensaje AS2" seleccionada.](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-standard.png)

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione un nombre para la conexión, seleccione la cuenta de integración y, luego, **Crear**.

1. En la propiedad **cuerpo** de la acción, escriba la entrada que desea que descodifique la acción AS2, que es la salida `body` del desencadenador Solicitud. Hay varias maneras de especificar este contenido como entrada de la acción, ya sea seleccionándolo en la lista de contenido dinámico o como expresión:

   * Para seleccionar de una lista que muestre las salidas de desencadenador disponibles, haga clic dentro del cuadro de la propiedad **cuerpo**. Cuando aparezca la lista de contenido dinámico, en **Cuando se recibe una solicitud HTTP**, seleccione el valor de propiedad **Cuerpo**, por ejemplo:

     ![Captura de pantalla que muestra el diseñador de inquilino único con la lista de contenido dinámico y la propiedad "Cuerpo" seleccionada.](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-standard.png)

     > [!TIP]
     > Si no aparece ninguna salida de desencadenador, en la lista de propiedades dinámicas, en **Cuando se recibe una solicitud HTTP**, seleccione **Ver más**.

   * Para especificar una expresión que haga referencia a la salida `body` del desencadenador, haga clic dentro del cuadro de la propiedad **cuerpo**. Después de que aparezca la lista de contenido dinámico, seleccione **Expresión**. En el editor de expresiones, escriba la expresión siguiente y seleccione **Aceptar**:

     `triggerOutputs()['body']`

     O bien, en el cuadro **cuerpo**, escriba directamente la expresión siguiente:

     `@triggerBody()`

     La expresión se resuelve en el token **Cuerpo**.

     ![Captura de pantalla que muestra el diseñador de inquilino único con la salida de la propiedad "Body" resuelta.](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-standard.png)

1. En el cuadro de la propiedad **Encabezados**, escriba los encabezados necesarios para la acción AS2, que se encuentran en la salida `headers` del desencadenador Solicitud.

   1. Para especificar una expresión que haga referencia a la salida `headers` del desencadenador, seleccione la opción para **cambiar los encabezados del mensaje al modo de texto**.

      ![Captura de pantalla que muestra el diseñador de inquilino único con la opción "Switch Message headers to text mode&quot; (&quot;Cambiar encabezados de mensaje al modo de texto") seleccionada.](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-standard.png)

   1. Haga clic dentro del cuadro de la propiedad **Encabezados**. Después de que aparezca la lista de contenido dinámico, seleccione **Expresión**. En el editor de expresiones, escriba la expresión siguiente y seleccione **Aceptar**:

      `triggerOutputs()['Headers']`

      En la acción **Descodificar mensaje AS2**, la expresión aparece ahora como un token:

      ![Captura de pantalla que muestra el diseñador de inquilino único y la propiedad "Encabezados" con el token "@triggerOutputs()['Headers']".](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-standard.png)

   1. Para que este token de expresión se resuelva en el token **Headers**, cambie entre la vista del diseñador y la vista de código. Después de este paso, la acción **Descodificación AS2** se muestra similar a la de este ejemplo:

      ![Captura de pantalla que muestra el diseñador de inquilino único y la salida de los encabezados resueltos del desencadenador.](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-standard.png)

---

<a name="add-response-action"></a>

## <a name="add-the-response-action-as-a-message-receipt"></a>Incorporación de la acción Respuesta como confirmación de mensaje

Para notificar al asociado comercial que se ha recibido el mensaje, puede volver a enviar una respuesta que contenga una notificación de disposición del mensaje (MDN) AS2 mediante las acciones Condición y Respuesta. Al agregar estas acciones inmediatamente después de la acción AS2, el flujo de trabajo de la aplicación lógica puede continuar el procesamiento si la acción AS2 se realiza correctamente. De lo contrario, si se produce un error en la acción AS2, el flujo de trabajo de la aplicación lógica deja de procesarse.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En la acción **Descodificación AS2**, seleccione **Nuevo paso**.

1. En el panel **Elegir una operación**, seleccione **Integrado**, si no está seleccionado. En el cuadro de búsqueda, escriba `condition`. Seleccione la acción **Condición**.

   ![Captura de pantalla que muestra el diseñador multiinquilino y la acción "Condición".](./media/logic-apps-enterprise-integration-b2b/add-condition-action-consumption.png)

   Ahora aparece la forma de la condición, incluidas las rutas de acceso que determinan si se cumple la condición.

   ![Captura de pantalla que muestra el diseñador multiinquilino y la forma de la condición con rutas de acceso vacías.](./media/logic-apps-enterprise-integration-b2b/added-condition-action-consumption.png)

1. Ahora especifique la condición que se va a evaluar. En el cuadro **Elegir un valor**, escriba la expresión siguiente:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   En el cuadro central, asegúrese de que la operación de comparación esté establecida en `is equal to`. En el cuadro de la derecha, escriba el valor `Expected`.

1. Guarde el flujo de trabajo de la aplicación lógica. Para que esta expresión se resuelva como este token, cambie entre la vista del diseñador y la vista de código.

   ![Captura de pantalla que muestra el diseñador multiinquilino y la forma de la condición con una operación.](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-consumption.png)

1. Ahora, especifique las respuestas para devolver en función de si la acción **Descodificación AS2** se realiza correctamente o no.

   1. En el caso de que la acción **Descodificación AS2** sea correcta, en la forma **True**, seleccione **Agregar una acción**. En el cuadro de búsqueda **Elegir una operación**, escriba `response` y seleccione **Respuesta**.

      ![Captura de pantalla que muestra el diseñador multiinquilino y la acción "Respuesta".](./media/logic-apps-enterprise-integration-b2b/select-response-consumption.png)

   1. Para acceder a MDN AS2 desde la salida de la acción **Descodificación AS2**, especifique las expresiones siguientes:

      * En la propiedad **Encabezados** de la acción **Respuesta**, escriba la expresión siguiente:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * En la propiedad **Cuerpo** de la acción **Respuesta**, escriba la expresión siguiente:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Para que esta expresión se resuelva como token, cambie entre la vista del diseñador y la vista de código:

      ![Captura de pantalla que muestra el diseñador multiinquilino y la expresión resuelta para acceder a MDN AS2.](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-consumption.png)

   1. En el caso de que la acción **Descodificación AS2** genere un error, en la forma **False**, seleccione **Agregar una acción**. En el cuadro de búsqueda **Elegir una operación**, escriba `response` y seleccione **Respuesta**. Configure la acción **Respuesta** para devolver el estado y el error que desea.

1. Guarde el flujo de trabajo de la aplicación lógica.

### <a name="standard"></a>[Estándar](#tab/standard)

1. En la acción **Descodificar mensaje AS2**, seleccione **Permite agregar un nuevo paso** (signo más) y, luego, **Agregar una acción**.

1. En el panel **Elegir una operación**, seleccione **Integrado**, si no está seleccionado. En el cuadro de búsqueda, escriba `condition`. Seleccione la acción **Condición**.

   ![Captura de pantalla que muestra el diseñador de inquilino único con la acción "Condición" seleccionada.](./media/logic-apps-enterprise-integration-b2b/add-condition-action-standard.png)

   Ahora aparece la forma de la condición, incluidas las rutas de acceso que determinan si se cumple la condición.

   ![Captura de pantalla que muestra el diseñador de inquilino único y la forma "Condición" con rutas de acceso vacías.](./media/logic-apps-enterprise-integration-b2b/added-condition-action-standard.png)

1. Ahora especifique la condición que se va a evaluar. Seleccione la forma **Condición** para que aparezca el panel de detalles. En el cuadro **Elegir un valor**, escriba la expresión siguiente:

   `@body('Decode_AS2_message')?['AS2Message']?['MdnExpected']`

   En el cuadro central, asegúrese de que la operación de comparación esté establecida en `is equal to`. En el cuadro de la derecha, escriba el valor `Expected`.

1. Guarde el flujo de trabajo de la aplicación lógica. Para que esta expresión se resuelva como este token, cambie entre la vista del diseñador y la vista de código.

   ![Captura de pantalla que muestra el diseñador de inquilino único y la forma de la condición con una operación.](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-standard.png)

1. Ahora, especifique las respuestas para devolver en función de si la acción **Descodificar mensaje AS2** se realiza correctamente o no.

   1. En el caso de que la acción **Descodificar mensaje AS2** sea correcta, en la forma **True**, seleccione el signo más y, luego, **Agregar una acción**. En el panel **Agregar una acción**, en el cuadro de búsqueda **Elegir una operación**, escriba `response` y seleccione **Respuesta**.

      ![Captura de pantalla que muestra el diseñador de inquilino único y la acción "Respuesta".](./media/logic-apps-enterprise-integration-b2b/select-response-standard.png)

   1. Para acceder a MDN AS2 desde la salida de la acción **Descodificación AS2**, especifique las expresiones siguientes:

      * En la propiedad **Encabezados** de la acción **Respuesta**, escriba la expresión siguiente:

        `@body('Decode_AS2_message')?['OutgoingMdn']?['OutboundHeaders']`

      * En la propiedad **Cuerpo** de la acción **Respuesta**, escriba la expresión siguiente:

        `@body('Decode_AS2_message')?['OutgoingMdn']?['Content']`

   1. Para que esta expresión se resuelva como token, cambie entre la vista del diseñador y la vista de código:

      ![Captura de pantalla que muestra el diseñador de inquilino único y la expresión resuelta para acceder a MDN AS2.](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-standard.png)

   1. En el caso de que se produzca un error en la acción **Descodificar mensaje AS2**, en la forma **False**, seleccione el signo más y, luego, **Agregar una acción**. En el panel **Agregar una acción**, en el cuadro de búsqueda **Elegir una operación**, escriba `response` y seleccione **Respuesta**. Configure la acción **Respuesta** para devolver el estado y el error que desea.

1. Guarde el flujo de trabajo de la aplicación lógica.

---

<a name="add-decode-x12-action"></a>

## <a name="add-the-decode-x12-message-action"></a>Incorporación de la acción Descodificar mensaje X12

Ahora agregue la acción **Descodificar mensaje X12**.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En la acción **Respuesta**, seleccione **Agregar una acción**.

1. En **Elegir una operación**, en el cuadro de búsqueda, escriba `x12 decode` y seleccione **Descodificar mensaje X12**.

   ![Captura de pantalla que muestra el diseñador multiinquilino y la acción "Descodificar mensaje X12" seleccionada.](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-consumption.png)

1. Si la acción X12 solicita información de conexión, proporcione el nombre de la conexión, seleccione la cuenta de integración que desee usar y, a continuación, seleccione **Crear**.

   ![Captura de pantalla que muestra el diseñador multiinquilino y la conexión a la cuenta de integración.](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-consumption.png)

1. Ahora, especifique la entrada para la acción X12. En este ejemplo se usa la salida de la acción AS2, que es el contenido del mensaje, pero tenga en cuenta que este contenido está en formato de objeto JSON y está codificado en base64. Por lo tanto, tiene que convertir este contenido en una cadena.

   En el cuadro **Mensaje de archivo plano X12 que se va a descodificar**, escriba la expresión siguiente para convertir la salida AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

1. Guarde el flujo de trabajo de la aplicación lógica. Para que esta expresión se resuelva como este token, cambie entre la vista del diseñador y la vista de código.

    ![Captura de pantalla que muestra el diseñador multiinquilino y la conversión de contenido codificado en base64 a una cadena.](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-consumption.png)

1. Guarde el flujo de trabajo de la aplicación lógica.

   Si necesita pasos adicionales para este flujo de trabajo de aplicación lógica, por ejemplo, para descodificar el contenido del mensaje y generar ese contenido en formato de objeto JSON, continúe agregando las acciones necesarias para el flujo de trabajo de la aplicación lógica.

### <a name="standard"></a>[Estándar](#tab/standard)

1. En la acción **Respuesta**, seleccione el signo más y, luego, **Agregar una acción**. En el panel **Agregar una acción**, en el cuadro de búsqueda **Elegir una operación**, seleccione **Azure** si no está seleccionado. En el cuadro de búsqueda, escriba `x12 decode` y seleccione **Descodificar mensaje X12**.

   ![Captura de pantalla que muestra el diseñador de inquilino único y la acción "Descodificar mensaje X12" seleccionada.](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-standard.png)

1. Si la acción X12 solicita información de conexión, proporcione el nombre de la conexión, seleccione la cuenta de integración que desee usar y, a continuación, seleccione **Crear**.

   ![Captura de pantalla que muestra el diseñador de inquilino único y la conexión a la cuenta de integración.](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-standard.png)

1. Ahora, especifique la entrada para la acción X12. En este ejemplo se usa la salida de la acción AS2, que es el contenido del mensaje, pero tenga en cuenta que este contenido está en formato de objeto JSON y está codificado en base64. Por lo tanto, tiene que convertir este contenido en una cadena.

   En el cuadro **Mensaje de archivo plano X12 que se va a descodificar**, escriba la expresión siguiente para convertir la salida AS2:

   `@base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])`

1. Guarde el flujo de trabajo de la aplicación lógica. Para que esta expresión se resuelva como este token, cambie entre la vista del diseñador y la vista de código.

    ![Captura de pantalla que muestra el diseñador de inquilino único y la conversión de contenido codificado en base64 a una cadena.](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-standard.png)

1. Guarde el flujo de trabajo de la aplicación lógica de nuevo.

   Si necesita pasos adicionales para este flujo de trabajo de aplicación lógica, por ejemplo, para descodificar el contenido del mensaje y generar ese contenido en formato de objeto JSON, siga agregando las acciones necesarias para el flujo de trabajo de la aplicación lógica.

---

Con esto, ya ha terminado de configurar el flujo de trabajo de la aplicación lógica B2B. En una aplicación real, puede almacenar los datos X12 descodificados en un almacén de datos o una aplicación de línea de negocio (LOB). Por ejemplo, revise la siguiente documentación:

* [Conexión a sistemas SAP desde Azure Logic Apps](logic-apps-using-sap-connector.md)

* [Supervisión, creación y administración de archivos SFTP mediante SSH y Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Puede agregar más acciones o [escribir API personalizadas](logic-apps-create-api-app.md) que se conecten a sus propias aplicaciones LOB, así como emplear estas API en su aplicación lógica.

## <a name="next-steps"></a>Pasos siguientes

* [Recepción de llamadas HTTPS entrantes y respuesta a ellas](../connectors/connectors-native-reqres.md)
* [Intercambio de mensajes AS2 para la integración empresarial B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Intercambio de mensajes X12 para la integración empresarial B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Obtenga más información acerca de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
