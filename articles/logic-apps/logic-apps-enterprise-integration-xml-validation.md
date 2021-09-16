---
title: Validación de documentos XML para la integración empresarial B2B
description: Validación de XML usando esquemas en Azure Logic Apps con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 87650a1ab950f8e88fe08a1c4555c98652776730
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099309"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>Validación de XML para flujos de trabajo en Azure Logic Apps

A menudo, en los escenarios de negocio a negocio (B2B) de integración empresarial, las entidades de un acuerdo deben asegurarse de que los mensajes que intercambian sean válidos antes de que pueda empezar el procesamiento de datos. Puede validar documentos con respecto a un esquema predefinido mediante la acción **Validación de XML** de Azure Logic Apps.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* El flujo de trabajo de aplicación lógica, en blanco o ya existente, donde quiera usar la acción **Validación de XML**.

  Si tiene un flujo de trabajo en blanco, use el desencadenador que desee. En este ejemplo se usa el desencadenador de solicitud.

  Si no conoce las aplicaciones lógicas, consulte la siguiente documentación:

  * [¿Qué es Azure Logic Apps?](logic-apps-overview.md)

  * [Inicio rápido: Creación del primer flujo de trabajo de Logic Apps en Azure Portal](quickstart-create-first-logic-app-workflow.md)

  * [Creación de flujos de trabajo de aplicación lógica de un solo inquilino](create-single-tenant-workflows-azure-portal.md)

  * [Modelos de medición, facturación y precios de uso de Azure Logic Apps](logic-apps-pricing.md)

* Si usa el tipo de recurso de **aplicación lógica (consumo)** , debe tener una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que cumpla los siguientes requisitos:

  * Está asociada a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica donde piensa usar la acción **Validación de XML**.

  * Está [vinculada](logic-apps-enterprise-integration-create-integration-account.md#link-account) al recurso de aplicación lógica.

  * Contiene el [esquema](logic-apps-enterprise-integration-schemas.md) que se va a usar para validar el contenido XML.

  Si usa el tipo de recurso de **aplicación lógica (estándar)** , no necesita una cuenta de integración. Sin embargo, tiene que agregar de todos modos al recurso de la aplicación lógica el [esquema](logic-apps-enterprise-integration-schemas.md) que se va a usar para validar el contenido XML. Puede completar esta tarea en el menú del recurso de aplicación lógica, en **Configuración**, en la sección **Esquemas**.

## <a name="add-xml-validation-action"></a>Incorporación de la acción de validación XML

1. En [Azure Portal](https://portal.azure.com), abra la aplicación lógica y el flujo de trabajo en la vista del diseñador.

1. Si tiene una aplicación lógica en blanco sin desencadenador, agregue el que desee. En este ejemplo se usa el desencadenador de solicitud. De lo contrario, continúe con el paso siguiente.

   Para agregar el desencadenador de solicitud, escriba `HTTP request` en el cuadro de búsqueda del diseñador y seleccione el desencadenador de solicitud denominado **Cuando se recibe una solicitud HTTP**.

1. En el paso del flujo de trabajo donde quiera agregar la acción **Validación de XML**, seleccione uno de los siguientes:

   En el caso de una aplicación lógica basada en el plan ISE o de consumo, elija un paso:

   * Para agregar la acción **Validación de XML** al final del flujo de trabajo, seleccione **Nuevo paso**.

   * Para agregar la acción **Validación de XML** entre pasos existentes, mueva el puntero del ratón sobre la flecha que conecta los pasos para que aparezca el signo más ( **+** ). Haga clic en el signo más y seleccione **Agregar una acción**.

   En el caso de una aplicación lógica basada en el plan estándar, elija un paso:

   * Para agregar la acción **Validación de XML** al final del flujo de trabajo, seleccione el signo más ( **+** ) y, a continuación, **Agregar una acción**.

   * Para agregar la acción **Validación de XML** entre pasos existentes, seleccione el signo más ( **+** ) que aparece entre los pasos y, a continuación, **Agregar una acción**.

1. En **Choose an operation** (Elegir una operación), seleccione **Built-in** (Integrada). En el cuadro de búsqueda, escriba `xml validation`. En la lista de acciones, seleccione **Validación XML**.

1. Para especificar el contenido XML que desea validar, haga clic dentro del cuadro **Contenido** para que aparezca la lista de contenido dinámico.

   La lista de contenido dinámico muestra los tokens en propiedad que representan las salidas de los pasos anteriores en el flujo de trabajo. Si la lista no muestra una propiedad que se espera, compruebe el encabezado de desencadenador o acción en la lista y vea si puede seleccionar **Ver más**.

   En el caso de una aplicación lógica basada en el plan ISE o de consumo, el diseñador tiene el aspecto de este ejemplo:

   ![Captura de pantalla en la que se muestra el diseñador multiinquilino con la lista de contenido dinámico abierta, el cursor en el cuadro "Contenido" y la lista de contenido dinámico abierta](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   En el caso de una aplicación lógica basada en el plan estándar, el diseñador tiene el aspecto de este ejemplo:

   ![Captura de pantalla en la que se muestra el diseñador de un solo inquilino con la lista de contenido dinámico abierta, el cursor en el cuadro "Contenido" y la lista de contenido dinámico abierta](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. En la lista de contenido dinámico, seleccione el token de propiedad del contenido que quiera validar.

   En este ejemplo se selecciona el token **Cuerpo** del desencadenador.

1. Con el fin de especificar el esquema que se va a usar para la validación, abra la lista **Nombre de esquema** y seleccione el esquema que ha agregado anteriormente.

1. Cuando haya terminado, asegúrese de guardar el flujo de trabajo de aplicación lógica.

   Ha terminado de configurar la acción **Validación de XML**. En una aplicación real, podría almacenar los datos validados en una aplicación de línea de negocio (LOB) como SalesForce. Para enviar la salida validada a Salesforce, agregue una acción Salesforce.

1. Para probar la acción de validación, desencadene y ejecute el flujo de trabajo. Por ejemplo, en el caso del desencadenador de solicitud, envíe una solicitud a la dirección URL del punto de conexión del desencadenador.

## <a name="next-steps"></a>Pasos siguientes

* [Incorporación de esquemas para la validación de XML en Azure Logic Apps](logic-apps-enterprise-integration-schemas.md)
* [Transformación de XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-transform.md)