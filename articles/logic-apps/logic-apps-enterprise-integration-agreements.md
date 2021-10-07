---
title: Definición de contratos entre asociados en los flujos de trabajo
description: Agregue contratos a la cuenta de integración en los flujos de trabajo de Azure Logic Apps mediante Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 3a1b714be1f6eb70a4780c7abf58f13a45eb3f3f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584170"
---
# <a name="add-agreements-between-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Adición de contratos entre asociados a cuentas de integración en los flujos de trabajo de Azure Logic Apps

Después de agregar asociados a la cuenta de integración, especifique cómo estos intercambian los mensajes mediante la definición de [*contratos*](logic-apps-enterprise-integration-agreements.md) en la cuenta de integración. Los contratos ayudan a las organizaciones a comunicarse entre sí sin problemas, ya que definen el protocolo estándar específico del sector para intercambiar mensajes y proporcionan las siguientes ventajas compartidas:

* Permiten a las organizaciones intercambiar información con un formato conocido.

* Mejoran la eficacia al realizar transacciones de negocio a negocio (B2B).

* Facilitan la creación, la administración y el uso de contratos para crear soluciones de integración empresariales.

Un contrato requiere un *asociado anfitrión*, que siempre es su organización, y un *asociado invitado*, que es la organización que intercambia mensajes con la suya. El asociado invitado puede ser otra compañía o incluso un departamento de su organización. Con este contrato, se especifica cómo controlar los mensajes entrantes y salientes desde la perspectiva del asociado anfitrión.

En este artículo se muestra cómo crear y administrar un contrato, que luego puede usar para intercambiar mensajes B2B con otro asociado mediante las operaciones AS2, X12, EDIFACT o RosettaNet.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Si usa el tipo de recurso [**Logic App (Consumption)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) (Aplicación lógica [Consumo]), la cuenta de integración necesita un [vínculo al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account) para poder utilizar los artefactos en el flujo de trabajo.

  * Si usa el tipo de recurso [**Logic App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) (Aplicación lógica [Estándar]), la cuenta de integración no necesita un vínculo al recurso de aplicación lógica, pero sigue siendo necesario para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](logic-apps-enterprise-integration-edifact.md). La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

  > [!NOTE]
  > Actualmente, solo el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [Consumo]) admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Logic App (Standard)** (Aplicación lógica [Estándar]) no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Al menos dos [asociados comerciales](logic-apps-enterprise-integration-partners.md) en la cuenta de integración. En un contrato hacen falta un asociado anfitrión y un asociado invitado. Además, en un contrato es necesario que ambos asociados usen el mismo calificador de *identidad empresarial* o uno compatible que sea adecuado para un contrato AS2, X12, EDIFACT o RosettaNet.

* Opcionalmente, el recurso de aplicación lógica y el flujo de trabajo donde quiere usar el contrato para intercambiar mensajes. El flujo de trabajo requiere cualquier desencadenador que inicie el flujo de trabajo de la aplicación lógica.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) e [Inicio rápido: Creación de la primera aplicación lógica](quickstart-create-first-logic-app-workflow.md).

## <a name="add-an-agreement"></a>Adición de un contrato

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione la cuenta de integración en la que va a agregar los asociados.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Acuerdos**.

1. En el panel **Contratos**, seleccione **Agregar**.

1. En el panel **Agregar**, proporcione la siguiente información sobre el contrato:

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Nombre** | Sí | <*agreement-name*> | Escriba el nombre del acuerdo. |
   | **Tipo de contrato** | Sí | **AS2**, **X12**, **EDIFACT** o **RosettaNet** | El tipo de protocolo para el acuerdo. Al crear el archivo de contrato, el contenido del archivo debe coincidir con el tipo de contrato. |
   | **Asociado host** | Sí | <*host-partner-name*> | El asociado anfitrión representa a su organización. |
   | **Identidad del host** | Sí | <*host-partner-identifier*> | El identificador del asociado host. |
   | **Asociado invitado** | Sí | <*guest-partner-name*> | El asociado invitado representa a la organización que se comunica con la suya. |
   | **Identidad del invitado** | Sí | <*guest-partner-identifier*> | El identificador del asociado invitado. |
   | **Configuración de recepción** | Varía | Varía | Estas propiedades especifican de qué manera el asociado anfitrión recibe todos los mensajes entrantes del asociado invitado en el contrato. Para más información, revise el tipo de contrato correspondiente: <p>- [Configuración de mensajes AS2](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configuración de mensajes X12](logic-apps-enterprise-integration-x12.md) |
   | **Configuración de envío** | Varía | Varía | Estas propiedades especifican de qué manera el asociado anfitrión envía los mensajes salientes al asociado invitado en el contrato. Para más información, revise el tipo de contrato correspondiente: <p>- [Configuración de mensajes AS2](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configuración de mensajes EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configuración de mensajes X12](logic-apps-enterprise-integration-x12.md) |
   | **Referencias de PIP de RosettaNet** | Varía | Varía | En este panel se especifica información sobre uno o varios procesos de interfaz de asociado (PIP) para usar mensajes de RosettaNet. Para más información, revise [Intercambio de mensajes de RosettaNet](logic-apps-enterprise-integration-rosettanet.md). |
   |||||

   > [!IMPORTANT]
   > La resolución de un contrato depende de la coincidencia de los siguientes elementos definidos en el asociado y el mensaje entrante:
   >
   > * El calificador y el identificador del remitente
   > * El calificador y el identificador del receptor
   >
   > Si estos valores cambian para el asociado, no olvide actualizar también el contrato.

1. Cuando finalice, seleccione **Aceptar**.

   El contrato aparece ahora en la lista **Contratos**.

## <a name="edit-an-agreement"></a>Edición de un contrato

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione la cuenta de integración en la que va a agregar los asociados.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Acuerdos**.

1. En el panel **Contratos**, seleccione el contrato, después elija **Editar** y realice los cambios.

1. Cuando finalice, seleccione **Aceptar**.

## <a name="delete-an-agreement"></a>Eliminación de un contrato

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione la cuenta de integración en la que va a agregar los asociados.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Acuerdos**.

1. En el panel **Contratos**, seleccione el contrato que quiera eliminar y, después, elija **Eliminar**.

1. Seleccione **Sí** para confirmar que quiere eliminar el contrato.

## <a name="next-steps"></a>Pasos siguientes

* [Intercambio de mensajes AS2](logic-apps-enterprise-integration-as2.md)
* [Intercambio de mensajes EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Intercambio de mensajes X12](logic-apps-enterprise-integration-x12.md)
* [Intercambio de mensajes de RosettaNet](logic-apps-enterprise-integration-rosettanet.md)
