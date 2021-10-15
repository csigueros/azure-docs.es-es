---
title: Intercambio de mensajes AS2 en flujos de trabajo B2B
description: Intercambio de mensajes AS2 entre asociados mediante la creación de flujos de trabajo con Azure Logic Apps y Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8023073aecae3a1f97c82a16a5be952df7425186
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399664"
---
# <a name="exchange-as2-messages-using-workflows-in-azure-logic-apps"></a>Intercambio de mensajes AS2 mediante flujos de trabajo en Azure Logic Apps

Para enviar y recibir mensajes AS2 en los flujos de trabajo que crea mediante Azure Logic Apps, use el conector **AS2** que ofrece desencadenadores y acciones que admiten y administran comunicaciones AS2 (versión 1.2).

* Si está trabajando con el tipo de recurso **Aplicación lógica (consumo)** y no necesita funcionalidades de seguimiento, use el conector **AS2 (v2)** en lugar del conector **AS2** original, que está en desuso.

  Excepto para el seguimiento, **AS2 (v2)** proporciona mejor rendimiento, las mismas funcionalidades que la versión original, es nativo del entorno de ejecución de Azure Logic Apps y presenta importantes mejoras de rendimiento en cuanto al tamaño de los mensajes, la capacidad de proceso y la latencia. Además, el conector v2 no requiere la creación de una conexión a la cuenta de integración. En su lugar, tal y como se describe en los requisitos previos, asegúrese de vincular la cuenta de integración al recurso de la aplicación lógica donde planea usar el conector.

* Si trabaja con el tipo de recurso **Aplicación lógica (estándar)** , actualmente solo está disponible el conector **AS2** original.

  Para más información técnica sobre esta versión del conector **AS2** original, consulte la [página de referencia del conector](/connectors/as2/) en la cual se describen los desencadenadores, acciones y límites que se documentan en el archivo de Swagger del conector.

### <a name="consumption"></a>[Consumo](#tab/consumption)

En las listas siguientes se describen las acciones que proporciona el conector **AS2 (v2)** para establecer la seguridad y confiabilidad al transmitir mensajes:

* La acción [**Codificar con AS2**](#encode) para proporcionar firmas digitales, cifrado y confirmaciones mediante las notificaciones de disposición del mensaje (MDN), lo que ayuda a admitir la recepción sin rechazo. Por ejemplo, esta acción se aplica a los encabezados AS2/HTTP y realiza estas tareas cuando se configura:

  * Firma los mensajes salientes.
  * Cifra los mensajes salientes.
  * Comprime el mensaje.
  * Transmite el nombre de archivo en el encabezado MIME.

* La acción [**Descodificar con AS2**](#decode) para proporcionar firmas digitales, descifrado y confirmaciones a través de las notificaciones de disposición de mensajes (MDN). Por ejemplo, esta acción lleva a cabo estas tareas:

  * Procesa los encabezados AS2/HTTP.
  * Reconcilia los MDN recibidos con los mensajes de salida originales.
  * Actualiza y correlaciona registros en la base de datos sin rechazo.
  * Escribe registros para los informes de estado de AS2.
  * Devuelve el contenido de la carga útil con codificación base64.
  * Determina si el MDN es necesario. En función del contrato AS2, determina si el MDN deben ser sincrónico o asincrónico.
  * Genera el MDN sincrónico o asincrónico según el contrato AS2.
  * Establece las propiedades y los token de correlación en los MDN.

  Esta acción también realiza estas tareas cuando se configura:

  * Comprueba la firma.
  * Descifra los mensajes.
  * Descomprime el mensaje.
  * Comprueba y prohíbe duplicados de identificador de mensaje.

### <a name="standard"></a>[Estándar](#tab/standard)

Para más información sobre los desencadenadores, acciones y límites de la versión **AS2** original del conector, consulte la [página de referencia del conector](/connectors/as2/) como se documenta en el archivo de Swagger del conector.

---

En este artículo se muestra cómo agregar la codificación AS2 y las acciones de descodificación a un flujo de trabajo de aplicación lógica existente. Aunque puede usar cualquier desencadenador para iniciar el flujo de trabajo, en los ejemplos se usa el desencadenador [Request](../connectors/connectors-native-reqres.md).

## <a name="limits"></a>Límites

Para más información sobre los límites del conector AS2 para los flujos de trabajo que se ejecutan en [aplicaciones lógicas multiinquilino de Azure Logic Apps, aplicaciones lógicas de un solo inquilino o en el entorno del servicio de integración (ISE)](logic-apps-overview.md#resource-environment-differences), consulte los [límites de los protocolos B2B para los tamaños de mensaje](logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Cuando se usa el [tipo de recurso **Aplicación lógica (consumo)**](logic-apps-overview.md#resource-environment-differences) y las operaciones **AS2 (v2)** , el recurso de la aplicación lógica no necesita un vínculo a la cuenta de integración. Sin embargo, todavía necesita esta cuenta para almacenar artefactos como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-x12.md), [X12](logic-apps-enterprise-integration-edifact.md) y EDIFACT. La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

  * Cuando se usa el [tipo de recurso **Aplicación lógica (estándar)**](logic-apps-overview.md#resource-environment-differences) y las operaciones con **AS2** original, el flujo de trabajo requiere una conexión a la cuenta de integración que se crea directamente desde el flujo de trabajo al agregar la operación con AS2.

* Al menos dos [asociados comerciales](logic-apps-enterprise-integration-partners.md) en la cuenta de integración. Las definiciones de ambos asociados deben usar el mismo calificador de *identidad empresarial*, que es **AS2Identity** en este escenario.

* Un [contrato AS2](logic-apps-enterprise-integration-agreements.md) en la cuenta de integración entre las entidades que participan en el flujo de trabajo. En cada contrato hacen falta un asociado anfitrión y un asociado invitado. El contenido de los mensajes entre usted y el otro asociado debe coincidir con el tipo de acuerdo.

* El recurso de aplicación lógica y el flujo de trabajo en los que quiere usar las operaciones AS2.

  > [!NOTE]
  > El conector **AS2 (v2)** proporciona únicamente las acciones, no los desencadenadores. En este artículo, los ejemplos de este conector usan el desencadenador [Request](../connectors/connectors-native-reqres.md). El conector **AS2** original incluye desencadenadores y acciones. Para más información sobre los desencadenadores, acciones y límites de la versión **AS2** original del conector, consulte la [página de referencia del conector](/connectors/as2/) como se documenta en el archivo de Swagger del conector.

  Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) e [Inicio rápido: Creación de la primera aplicación lógica](quickstart-create-first-logic-app-workflow.md).

* Si usa [Azure Key Vault](../key-vault/general/overview.md) para la administración de certificados, compruebe que las claves de almacén permitan las operaciones **Cifrar** y **Descifrar**. En caso contrario, las acciones de codificación y descodificación producirán un error.

  1. En Azure portal, abra el almacén de claves. En el menú del almacén de claves, en **Configuración**, seleccione **Claves**.

  1. En el panel **Claves**, seleccione la clave. En el panel **Versiones**, seleccione la versión de la clave que está utilizando.

  1. En el panel **Versión de la clave**, en **Operaciones permitidas**, confirme que las operaciones **Cifrar** y **Descifrar** están seleccionadas, por ejemplo:

     ![Captura de pantalla que muestra Azure Portal con los paneles de almacén de claves, clave y versión de la clave abiertos, que tiene las operaciones "Cifrar" y "Descifrar" seleccionadas.](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificación de mensajes AS2

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de AS2, seleccione **Nuevo paso**.

1. En el cuadro de búsqueda **Choose an operation** (Elegir una operación), escriba **Todo**. En el cuadro de búsqueda, escriba `as2 encode`. Seleccione la acción denominada **Codificación AS2**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la acción "Codificación AS2" seleccionada.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Una vez que aparezca la operación AS2 en el diseñador, proporcione la información de las siguientes propiedades:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Mensaje que se codificará** | Sí | Carga útil del mensaje. |
   | **AS2 desde** | Sí | Identificador empresarial del remitente del mensaje según lo especificado por su contrato AS2. |
   | **AS2 hasta** | Sí | Identificador empresarial del receptor del mensaje según lo especificado por su contrato AS2. |
   ||||

   Por ejemplo, la carga del mensaje es la salida del contenido del **cuerpo** del desencadenador Request:

   ![Captura de pantalla que muestra la acción "Codificación AS2" con las propiedades de codificación de mensajes.](./media/logic-apps-enterprise-integration-as2/as2-message-encode-details.png)

   > [!TIP]
   > Si experimenta problemas al enviar mensajes firmados o cifrados, considere la posibilidad de probar distintos formatos de algoritmo SHA256. La especificación AS2 no proporciona ninguna información sobre los formatos SHA256, por lo que cada proveedor usa su propia implementación o formato.

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de AS2, seleccione **Permite insertar un nuevo paso** (signo más) y, a continuación, seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, seleccione **Azure**. En el cuadro de búsqueda, escriba `as2 encode`. Seleccione la acción denominada **Codificar en mensaje AS2**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la operación "Codificar en mensaje AS2" seleccionada.](./media/logic-apps-enterprise-integration-as2/select-encode-as2-message.png)

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione la siguiente información:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre de la conexión** | Sí | Un nombre para la conexión |
   | **cuenta de integración** | Sí | En la lista de cuentas de integración disponibles, seleccione la cuenta que desea usar. |
   ||||

   Por ejemplo:

   ![Captura de pantalla que muestra el panel de conexión "Codificar en mensaje AS2".](./media/logic-apps-enterprise-integration-as2/create-as2-encode-connection-standard.png)

1. Seleccione **Crear** cuando haya terminado.

1. Una vez que aparezca el panel de detalles de AS2 en el diseñador, proporcione la información de las siguientes propiedades:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Mensaje que se codificará** | Sí | Carga útil del mensaje. |
   | **AS2 desde** | Sí | Identificador empresarial del remitente del mensaje según lo especificado por su contrato AS2. |
   | **AS2 hasta** | Sí | Identificador empresarial del receptor del mensaje según lo especificado por su contrato AS2. |
   ||||

   Por ejemplo, la carga del mensaje es la salida del contenido del **cuerpo** del desencadenador Request:

   ![Captura de pantalla que muestra la acción "Codificación con AS2" con las propiedades de codificación del mensaje.](./media/logic-apps-enterprise-integration-as2/encode-as2-message-details.png)

   > [!TIP]
   > Si experimenta problemas al enviar mensajes firmados o cifrados, considere la posibilidad de probar distintos formatos de algoritmo SHA256. La especificación AS2 no proporciona ninguna información sobre los formatos SHA256, por lo que cada proveedor usa su propia implementación o formato.

---

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Descodificación de mensajes AS2

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de AS2, seleccione **Nuevo paso**. En este ejemplo se usa el desencadenador [Request](../connectors/connectors-native-reqres.md).

1. En el cuadro de búsqueda **Choose an operation** (Elegir una operación), escriba **Todo**. En el cuadro de búsqueda, escriba `as2 decode`. Seleccione la acción denominada **Descodificación con AS2**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la operación "Codificación con AS2" seleccionada.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. En la forma de operación AS2, seleccione los valores de las propiedades **Mensaje que se codificará** y **Encabezados del mensaje** de las salidas del desencadenador o acción anterior.

   En este ejemplo, puede seleccionar las salidas del desencadenador Request.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la operación "Descodificación con AS2" con la salida "Cuerpo" y "Encabezados" seleccionada en el desencadenador Request.](media/logic-apps-enterprise-integration-as2/as2-message-decode-details.png)

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de la aplicación lógica y el flujo de trabajo en el diseñador.

1. En el diseñador, en el desencadenador o la acción donde quiera agregar la acción de AS2, seleccione **Permite insertar un nuevo paso** (signo más) y, a continuación, seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, seleccione **Azure**. En el cuadro de búsqueda, escriba `as2 decode`. Seleccione la acción denominada **Descodificar mensaje AS2**.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la operación "Descodificar mensaje AS2" seleccionada.](./media/logic-apps-enterprise-integration-as2/select-decode-as2-message.png)

1. Cuando se le pida que cree una conexión a la cuenta de integración, proporcione la siguiente información:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre de la conexión** | Sí | Un nombre para la conexión |
   | **cuenta de integración** | Sí | En la lista de cuentas de integración disponibles, seleccione la cuenta que desea usar. |
   ||||

   Por ejemplo:

   ![Captura de pantalla que muestra el panel de conexión "Descodificar mensaje AS2".](./media/logic-apps-enterprise-integration-as2/create-as2-decode-connection-standard.png)

1. Seleccione **Crear** cuando haya terminado.

1. En el panel de detalles AS2, seleccione los valores de las propiedades **Mensaje que se codificará** y **Encabezados del mensaje** de las salidas del desencadenador o acción anterior.

   En este ejemplo, puede seleccionar las salidas del desencadenador Request.

   ![Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo y la operación "Descodificar mensaje AS2" con la salida "Cuerpo" y "Encabezados" seleccionada en el desencadenador Request.](media/logic-apps-enterprise-integration-as2/decode-as2-message-details.png)

---

## <a name="sample"></a>Muestra

Para intentar implementar una aplicación lógica totalmente operativa y un escenario de ejemplo de AS2 (v2), consulte el artículo sobre [escenario y plantilla de aplicaciones lógicas de AS2 (v2)](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/).

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre otros [conectores para Azure Logic Apps](../connectors/apis-list.md).
