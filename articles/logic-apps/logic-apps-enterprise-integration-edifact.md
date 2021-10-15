---
title: Intercambio de mensajes EDIFACT en flujos de trabajo B2B
description: Intercambio de mensajes EDIFACT entre asociados mediante la creación de flujos de trabajo con Azure Logic Apps y Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 19fea6a0405d1fcc4cfbc9b1aa7647c52b4459b6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401668"
---
# <a name="exchange-edifact-messages-using-workflows-in-azure-logic-apps"></a>Intercambio de mensajes EDIFACT mediante flujos de trabajo en Azure Logic Apps

Para enviar y recibir mensajes EDIFACT en los flujos de trabajo que crea mediante Azure Logic Apps, use el conector **EDIFACT** que ofrece desencadenadores y acciones que admiten y administran comunicaciones EDIFACT.

En este artículo se muestra cómo agregar la codificación EDIFACT y las acciones de descodificación a un flujo de trabajo de aplicación lógica existente. Aunque puede usar cualquier desencadenador para iniciar el flujo de trabajo, en los ejemplos se usa el desencadenador [Request](../connectors/connectors-native-reqres.md). Para obtener más información sobre los desencadenadores, acciones y límites de la versión **EDIFACT** original del conector, consulte la [página de referencia del conector](/connectors/edifact/) como se documenta en el archivo de Swagger del conector.

![Captura de pantalla de información general que muestra la operación "Descodificar mensaje EDIFACT" con las propiedades de descodificación de mensajes.](./media/logic-apps-enterprise-integration-edifact/overview-edifact-message-consumption.png)

## <a name="edifact-encoding-and-decoding"></a>Codificación y descodificación de EDIFACT

En las secciones siguientes se describen las tareas que puede completar mediante las acciones de codificación y codificación de EDIFACT.

### <a name="encode-to-edifact-message-action"></a>Codificación en la acción de mensaje EDIFACT

* Permite resolver el contrato haciendo coincidir el calificador y el identificador del remitente con el calificador y el identificador del receptor del receptor.

* Permite serializar el intercambio electrónico de datos (EDI), que convierte los mensajes codificados en XML en conjuntos de transacciones EDI del intercambio.

* Permite aplicar segmentos de encabezado y final del conjunto de transacciones.

* Permite generar un número de control de intercambio, un número de control de grupo y un número de control del conjunto de transacciones para cada intercambio de salida.

* Permite reemplazar los separadores en los datos de carga útil.

* Permite validar las propiedades EDI y específicas del asociado, como el esquema para los elementos de datos del conjunto de transacciones frente al esquema del mensaje, los elementos de datos del conjunto de transacciones y la validación extendida en los elementos de datos del conjunto de transacciones.

* Permite generar un documento XML para cada conjunto de transacciones.

* Permite solicitar una confirmación técnica, una funcional o ambas, si esta opción está configurada.

  * Como confirmación técnica, el mensaje CONTRL indica la recepción de un intercambio.

  * Como confirmación funcional, el mensaje CONTRL indica la aceptación o el rechazo del intercambio, el grupo o el mensaje recibido, y se incluye una lista de errores o una funcionalidad no admitida.

### <a name="decode-edifact-message-action"></a>Acción de descodificación del mensaje EDIFACT

* Permite validar el sobre según el acuerdo entre socios comerciales.

* Permite resolver el contrato haciendo coincidir el calificador y el identificador del remitente con el calificador y el identificador del receptor.

* Permite dividir un intercambio en varias transacciones cuando el intercambio tiene más de una transacción que se basa en la **configuración de recepción** del contrato.

* Desensamblar el intercambio.

* Valide el intercambio electrónico de datos (EDI) y las propiedades específicas del asociado, como la estructura de sobres de intercambio, el esquema de sobres con respecto al esquema de control, el esquema de los elementos de datos del conjunto de transacciones con respecto al esquema del mensaje y la validación extendida en los elementos de datos del conjunto de transacciones.

* Compruebe que los números de control de intercambio, grupo y conjunto de transacciones no estén duplicados (si está configurado); por ejemplo:

  * Comprueba el número de control del intercambio con los intercambios recibidos anteriormente.

  * Comprueba el número de control del grupo en relación con otros números de control de grupo en el intercambio.

  * Comprueba el número de control del conjunto de transacciones con otros números de control del conjunto de transacciones de dicho grupo.

* Permite dividir el intercambio en conjuntos de transacciones o conservar todo el intercambio; por ejemplo:

  * Permite dividir el intercambio como conjuntos de transacciones: suspender conjuntos de transacciones en caso de error.

    La acción de descodificación divide el intercambio en conjuntos de transacciones y analiza cada uno de ellos. La acción de descodificación solo genera los conjuntos de transacciones que no superan la validación para `badMessages` y los resultados de las transacciones restantes se establecen en `goodMessages`.

  * Permite dividir el intercambio como conjuntos de transacciones: suspender intercambio en caso de error.

    La acción de descodificación divide el intercambio en conjuntos de transacciones y analiza cada uno de ellos. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, la acción establece todos los conjuntos de transacciones del intercambio en `badMessages`.

  * Permite conservar el intercambio: suspender conjuntos de transacciones en caso de error.

    La acción de descodificación conserva el intercambio y lo procesa todo por lotes. La acción de descodificación solo genera los conjuntos de transacciones que no superan la validación para `badMessages` y los resultados de las transacciones restantes se establecen en `goodMessages`.

  * Permite conservar el intercambio: suspender intercambio en caso de error.

    La acción de descodificación conserva el intercambio y lo procesa todo por lotes. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, la acción establece todos los conjuntos de transacciones del intercambio en `badMessages`.

* Permite generar una confirmación técnica, una funcional o ambas, si esta opción está configurada.

  * Una confirmación técnica o CONTRL ACK informa de los resultados de una comprobación sintáctica de todo intercambio recibido.

  * Una confirmación funcional que confirma la aceptación o rechazo del intercambio o grupo recibido.

## <a name="connector-reference"></a>Referencia de conectores

Para obtener más información técnica sobre el conector **EDIFACT**, consulte la [página de referencia del conector](/connectors/edifact/) en la cual se describen los desencadenadores, acciones y límites que se documentan en el archivo de Swagger del conector. Igualmente, consulte los [límites de los protocolos B2B para los tamaños de mensaje](logic-apps-limits-and-config.md#b2b-protocol-limits) correspondientes a los flujos de trabajo que se ejecutan en [aplicaciones lógicas multiinquilino de Azure Logic Apps, aplicaciones lógicas de un solo inquilino o en el entorno del servicio de integración (ISE)](logic-apps-overview.md#resource-environment-differences). Por ejemplo, en un [entorno de servicio de integración (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), la versión de ISE de este conector usa los [límites de mensajes B2B del ISE](logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Cuando se usa el [tipo de recurso **Aplicación lógica (consumo)**](logic-apps-overview.md#resource-environment-differences) y las operaciones **EDIFACT**, el recurso de la aplicación lógica no necesita un vínculo a la cuenta de integración. Sin embargo, todavía necesita esta cuenta para almacenar artefactos como asociados, contratos y certificados, junto con el uso de las operaciones [X12](logic-apps-enterprise-integration-x12.md), [AS2](logic-apps-enterprise-integration-as2.md) o EDIFACT. La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

  * Cuando se usa el [tipo de recurso **Aplicación lógica (estándar)**](logic-apps-overview.md#resource-environment-differences) y las operaciones con **EDIFACT**, el flujo de trabajo requiere una conexión a la cuenta de integración que se crea directamente desde el flujo de trabajo al agregar la operación con AS2.

* Al menos dos [asociados comerciales](logic-apps-enterprise-integration-partners.md) en la cuenta de integración. Las definiciones de ambos asociados deben usar el mismo calificador de *identidad empresarial*, que es **ZZZ - Mutually Defined** en este escenario.

* Un [contrato EDIFACT](logic-apps-enterprise-integration-agreements.md) en la cuenta de integración entre las entidades que participan en el flujo de trabajo. En cada contrato hacen falta un asociado anfitrión y un asociado invitado. El contenido de los mensajes entre usted y el otro asociado debe coincidir con el tipo de contrato.

  > [!IMPORTANT]
  > El conector EDIFACT solo admite caracteres UTF-8. Si la salida contiene caracteres inesperados, compruebe que los mensajes EDIFACT utilizan el juego de caracteres UTF-8.

* El recurso de aplicación lógica y el flujo de trabajo en los que quiere usar las operaciones EDIFACT.

  Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) e [Inicio rápido: Creación de la primera aplicación lógica](quickstart-create-first-logic-app-workflow.md).

<a name="encode"></a>

## <a name="encode-edifact-messages"></a>Codificación de mensajes EDIFACT

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de EDIFACT, seleccione **Nuevo paso**.

1. En el cuadro de búsqueda **Choose an operation** (Elegir una operación), escriba **Todo**. En el cuadro de búsqueda, escriba `edifact encode`. En este ejemplo, seleccione la acción denominada **Codificar en mensaje EDIFACT por nombre de contrato**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujo de trabajo y la acción "Codificar en mensaje EDIFACT por nombre de contrato" seleccionada.](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-consumption.png)

   > [!NOTE]
   > Puede seleccionar la acción **Codificar en mensaje EDIFACT por identidades** en su lugar, pero más adelante tendrá que proporcionar valores diferentes, como el **identificador del remitente** y el **identificador de receptor** especificados por el contrato EDIFACT. También debe especificar el **mensaje XML para codificar**, que puede ser la salida del desencadenador o una acción anterior.

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione la siguiente información:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre de la conexión** | Sí | Un nombre para la conexión |
   | **cuenta de integración** | Sí | En la lista de cuentas de integración disponibles, seleccione la cuenta que desea usar. |
   ||||

   Por ejemplo:

   ![Captura de pantalla que muestra el panel de conexión "Codificar en el mensaje EDIFACT por nombre de contrato".](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-consumption.png)

1. Seleccione **Crear** cuando haya terminado.

1. Una vez que aparezca la operación EDIFACT en el diseñador, proporcione información para las siguientes propiedades específicas de esta operación:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre del contrato EDIFACT** | Sí | Contrato EDIFACT que se usará. |
   | **Mensaje XML que se va a codificar** | Sí | Identificador empresarial del remitente del mensaje según lo especificado por su contrato EDIFACT. |
   | Otros parámetros | No | Esta operación incluye los siguientes parámetros: <p>- **Separador de elementos de datos** <br>- **Indicador de versión** <br>- **Separador de componentes** <br>- **Separador de repeticiones** <br>- **Terminador de segmentos** <br>- **Sufijo de terminador de segmento** <br>- **Indicador decimal** <p>Para obtener más información, consulte la [configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Por ejemplo, la carga del mensaje XML puede ser la salida del contenido del **cuerpo** del desencadenador Request:

   ![Captura de pantalla que muestra la operación "Codificar en el mensaje EDIFACT por nombre de contrato" con las propiedades de codificación del mensaje.](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-consumption.png)

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de EDIFACT, seleccione **Permite insertar un nuevo paso** (signo más) y, a continuación, seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, seleccione **Azure**. En el cuadro de búsqueda, escriba `edifact encode`. Seleccione la acción denominada **Codificar en mensaje EDIFACT por nombre de contrato**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujo de trabajo y la operación "Codificar en mensaje EDIFACT por nombre de contrato" seleccionada.](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-standard.png)

   > [!NOTE]
   > Puede seleccionar la acción **Codificar en mensaje EDIFACT por identidades** en su lugar, pero más adelante tendrá que proporcionar valores diferentes, como el **identificador del remitente** y el **identificador de receptor** especificados por el contrato EDIFACT. También debe especificar el **mensaje XML para codificar**, que puede ser la salida del desencadenador o una acción anterior.

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione la siguiente información:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre de la conexión** | Sí | Un nombre para la conexión |
   | **cuenta de integración** | Sí | En la lista de cuentas de integración disponibles, seleccione la cuenta que desea usar. |
   ||||

   Por ejemplo:

   ![Captura de pantalla que muestra el panel de conexión "Codificar en el mensaje EDIFACT por nombre de parámetro".](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-standard.png)

1. Seleccione **Crear** cuando haya terminado.

1. Una vez que aparezca el panel de detalles de EDIFACT en el diseñador, proporcione la información de las siguientes propiedades:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre del contrato EDIFACT** | Sí | Contrato EDIFACT que se usará. |
   | **Mensaje XML que se va a codificar** | Sí | Identificador empresarial del remitente del mensaje según lo especificado por su contrato EDIFACT. |
   | Otros parámetros | No | Esta operación incluye los siguientes parámetros: <p>- **Separador de elementos de datos** <br>- **Indicador de versión** <br>- **Separador de componentes** <br>- **Separador de repeticiones** <br>- **Terminador de segmentos** <br>- **Sufijo de terminador de segmento** <br>- **Indicador decimal** <p>Para obtener más información, consulte la [configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Por ejemplo, la carga del mensaje es la salida del contenido del **cuerpo** del desencadenador Request:

   ![Captura de pantalla que muestra la operación "Codificar en el mensaje EDIFACT por nombre de parámetro" con las propiedades de codificación del mensaje.](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-standard.png)

---

<a name="decode"></a>

## <a name="decode-edifact-messages"></a>Descodificación de mensajes EDIFACT

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de EDIFACT, seleccione **Nuevo paso**.

1. En el cuadro de búsqueda **Choose an operation** (Elegir una operación), escriba **Todo**. En el cuadro de búsqueda, escriba `edifact encode`. Seleccione la acción denominada **Descodificar mensaje EDIFACT**.

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione la siguiente información:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre de la conexión** | Sí | Un nombre para la conexión |
   | **cuenta de integración** | Sí | En la lista de cuentas de integración disponibles, seleccione la cuenta que desea usar. |
   ||||

   Por ejemplo:

   ![Captura de pantalla que muestra el panel de conexión "Descodificar mensaje EDIFACT".](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-consumption.png)

1. Seleccione **Crear** cuando haya terminado.

1. Una vez que aparezca la operación EDIFACT en el diseñador, proporcione información para las siguientes propiedades específicas de esta operación:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Mensaje de archivo plano EDIFACT que se va a descodificar** | Sí | Mensaje de archivo plano XML que se va a descodificar. |
   | Otros parámetros | No | Esta operación incluye los siguientes parámetros: <p>- **Separador de componentes** <br>- **Separador de elementos de datos** <br>- **Indicador de versión** <br>- **Separador de repeticiones** <br>- **Terminador de segmentos** <br>- **Sufijo de terminador de segmento** <br>- **Indicador decimal** <br>- **Juego de caracteres de carga útil** <br>- **Sufijo de terminador de segmento** <br>- **Conservar intercambio** <br>- **Suspender intercambio en caso de error** <p>Para obtener más información, consulte la [configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Por ejemplo, la carga del mensaje XML para descodificar puede ser la salida del contenido del **cuerpo** del desencadenador Request:

   ![Captura de pantalla que muestra la operación "Descodificar mensaje EDIFACT" con las propiedades de descodificación de mensajes.](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-consumption.png)

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de EDIFACT, seleccione **Permite insertar un nuevo paso** (signo más) y, a continuación, seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, seleccione **Azure**. En el cuadro de búsqueda, escriba `edifact encode`. Seleccione la acción denominada **Descodificar mensaje EDIFACT**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la operación "Descodificar mensaje EDIFACT" seleccionada.](./media/logic-apps-enterprise-integration-edifact/select-decode-edifact-message-standard.png)

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione la siguiente información:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre de la conexión** | Sí | Un nombre para la conexión |
   | **cuenta de integración** | Sí | En la lista de cuentas de integración disponibles, seleccione la cuenta que desea usar. |
   ||||

   Por ejemplo:

   ![Captura de pantalla que muestra el panel de conexión "Descodificar mensaje EDIFACT".](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-standard.png)

1. Seleccione **Crear** cuando haya terminado.

1. Una vez que aparezca el panel de detalles de EDIFACT en el diseñador, proporcione la información de las siguientes propiedades:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre del contrato EDIFACT** | Sí | Contrato EDIFACT que se usará. |
   | **Mensaje XML que se va a codificar** | Sí | Identificador empresarial del remitente del mensaje según lo especificado por su contrato EDIFACT. |
   | Otros parámetros | No | Esta operación incluye los siguientes parámetros: <p>- **Separador de elementos de datos** <br>- **Indicador de versión** <br>- **Separador de componentes** <br>- **Separador de repeticiones** <br>- **Terminador de segmentos** <br>- **Sufijo de terminador de segmento** <br>- **Indicador decimal** <p>Para obtener más información, consulte la [configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Por ejemplo, la carga del mensaje es la salida del contenido del **cuerpo** del desencadenador Request:

   ![Captura de pantalla que muestra la operación "Descodificar mensaje EDIFACT" con las propiedades de descodificación de mensajes.](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-standard.png)

---

## <a name="handle-unh25-segments-in-edifact-documents"></a>Control de segmentos UNH2.5 en documentos EDIFACT

En un documento EDIFACT, el [segmento UNH2.5](logic-apps-enterprise-integration-edifact-message-settings.md#receive-settings-schemas) se usa para la búsqueda de esquemas. Por ejemplo, en este mensaje EDIFACT de ejemplo, el campo UNH es `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Para controlar un documento EDIFACT o procesar un mensaje EDIFACT que tiene un segmento UN2.5, siga estos pasos:

1. Actualice o implemente un esquema que tenga el nombre de nodo raíz UNH2.5.

   Por ejemplo, supongamos que el nombre de la raíz del esquema para el campo UNH del ejemplo es `EFACT_D03B_ORDERS_EAN008`. Para cada `D03B_ORDERS` con un segmento UNH2.5 diferente, tiene que implementar un esquema distinto.

1. En [Azure Portal](https://portal.azure.com), agregue el esquema al recurso de la cuenta de integración o al recurso de aplicación lógica, que se basa en si está trabajando con el tipo de recurso **Aplicación lógica (consumo)** o **Aplicación lógica (estándar)** , respectivamente.

1. Tanto si está usando la acción de descodificación o codificación EDIFACT, cargue el esquema y realice configuración del esquema en las secciones **Configuración de recepción** o **Configuración de envío** del contrato EDIFACT, respectivamente.

1. Para editar el contrato EDIFACT, en el panel **Contratos**, seleccione el contrato pertinente. En la barra de herramientas del panel **Contratos**, seleccione **Editar como JSON**.

   * En la sección `receiveAgreement` del contrato, busque la sección `schemaReferences` y agregue el valor UNH2.5.

     ![Captura de pantalla que muestra Azure Portal con la sección "receiveAgreement" de un contrato EDIFACT en el editor JSON y la sección "schemaReferences" resaltada.](./media/logic-apps-enterprise-integration-edifact/agreement-receive-schema-references.png)

   * En la sección `sendAgreement` del contrato, busque la sección `schemaReferences` y agregue el valor UNH2.5.

     ![Captura de pantalla que muestra Azure Portal con la sección "sendAgreement" de un contrato EDIFACT en el editor JSON y la sección "schemaReferences" resaltada.](./media/logic-apps-enterprise-integration-edifact/agreement-send-schema-references.png)

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact-message-settings.md)
