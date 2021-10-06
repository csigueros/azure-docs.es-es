---
title: Validación de XML en flujos de trabajo de integración empresarial
description: Validación de XML usando esquemas en Azure Logic Apps con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 842b26502dcfa073bca21891eed44fe990037f06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660811"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>Validación de XML para flujos de trabajo en Azure Logic Apps

En los escenarios de negocio a negocio (B2B) de integración empresarial, a menudo las entidades de un contrato deben asegurarse de que los mensajes que intercambian sean válidos antes de que pueda empezar el procesamiento de datos. El flujo de trabajo de la aplicación lógica puede validar documentos y mensajes XML mediante la acción **Validación XML** y un [esquema](logic-apps-enterprise-integration-schemas.md) predefinido.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para obtener más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* El flujo de trabajo de aplicación lógica, en blanco o ya existente, donde quiera usar la acción **Validación de XML**.

  Si tiene un flujo de trabajo en blanco, use el desencadenador que desee. En este ejemplo se usa el desencadenador de solicitud.

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica donde piensa usar la acción **Validación XML***.

  * Si usa el [tipo de recurso **Aplicación lógica (consumo)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración necesita los elementos siguientes:

    * El [esquema](logic-apps-enterprise-integration-schemas.md) que se va a usar para validar el contenido XML.

    * Un [vínculo al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Si usa el [tipo de recurso **Aplicación lógica (estándar)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), no almacene los esquemas en la cuenta de integración. En su lugar, puede [agregar esquemas directamente al recurso de aplicación lógica](logic-apps-enterprise-integration-schemas.md) mediante Azure Portal o Visual Studio Code. Posteriormente puede usar dichas asignaciones en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*.

    Todavía necesita una cuenta de integración para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](logic-apps-enterprise-integration-edifact.md). Pero no es necesario vincular el recurso de aplicación lógica a la cuenta de integración, por lo que la funcionalidad de vinculación no existe. La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

    > [!NOTE]
    > Actualmente, solo el tipo de recurso **Aplicación lógica (consumo)** admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Aplicación lógica (estándar)** no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

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

   La acción **Validación XML** se ejecuta después de que se desencadene el flujo de trabajo y cuando el contenido XML está disponible para la validación.

## <a name="next-steps"></a>Pasos siguientes

* [Incorporación de esquemas para la validación de XML en Azure Logic Apps](logic-apps-enterprise-integration-schemas.md)
* [Transformación de XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-transform.md)