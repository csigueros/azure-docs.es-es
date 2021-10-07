---
title: Transformación de XML en flujos de trabajo de integración empresarial
description: Transforme XML mediante asignaciones en Azure Logic Apps con Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 027c1f44d756494432a076ec32f06e627f916b99
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553739"
---
# <a name="transform-xml-for-workflows-in-azure-logic-apps"></a>Transformación XML para flujos de trabajo en Azure Logic Apps

En escenarios de integración empresarial de negocio a negocio (B2B), puede que tenga que convertir XML entre formatos. El flujo de trabajo de la aplicación lógica puede transformar XML mediante la acción **Transformar XML** y una [*asignación*](logic-apps-enterprise-integration-maps.md) predefinida. Por ejemplo, imagine que recibe periódicamente pedidos o facturas B2B de un cliente que usa el formato de fecha añoMesDía (AAAAMMDD), mientras que su organización emplea el formato de fecha mesDíaAño (MMDDAAAA). Puede crear y usar una asignación que transforme el formato de fecha añoMesDía en mesDíaAño antes de almacenar los detalles del pedido o la factura en la base de datos de actividad de los clientes.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para obtener más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un flujo de trabajo de aplicaciones lógicas que ya se inicie con un desencadenador para poder agregar la acción **Transformar XML** donde sea necesario en el flujo de trabajo.

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existir en la misma ubicación o región de Azure que el recurso de aplicación lógica donde piensa usar la acción **Transformar XML**.

  * Si usa el [tipo de recurso **Aplicación lógica (consumo)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración necesita los elementos siguientes:

    * La [asignación](logic-apps-enterprise-integration-maps.md) que se va a usar para transformar el contenido XML.

    * Un [vínculo al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Si usa el [tipo de recurso **Aplicación lógica (estándar)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), no almacene las asignaciones en la cuenta de integración. En su lugar, puede [agregar las asignaciones directamente al recurso de aplicación lógica](logic-apps-enterprise-integration-maps.md) mediante Azure Portal o Visual Studio Code. Actualmente solo se admite XSLT 1.0. Luego puede usar estas asignaciones en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*.

    Todavía necesita una cuenta de integración para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](logic-apps-enterprise-integration-edifact.md). Pero no es necesario vincular el recurso de aplicación lógica a la cuenta de integración, por lo que la funcionalidad de vinculación no existe. La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

    > [!NOTE]
    > Actualmente, solo el tipo de recurso **Aplicación lógica (consumo)** admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Aplicación lógica (estándar)** no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

## <a name="add-transform-xml-action"></a>Incorporación de una acción Transformar XML

1. En [Azure Portal](https://portal.azure.com), abra la aplicación lógica y el flujo de trabajo en la vista del diseñador.

1. Si tiene una aplicación lógica en blanco sin desencadenador, agregue el que desee. En este ejemplo se usa el desencadenador de solicitud. De lo contrario, continúe con el paso siguiente.

   Para agregar el desencadenador de solicitud, escriba `HTTP request` en el cuadro de búsqueda del diseñador y seleccione el desencadenador de solicitud denominado **Cuando se recibe una solicitud HTTP**.

1. En el paso del flujo de trabajo donde quiere agregar la acción **Transformar XML** seleccione uno de los siguientes pasos:

   En el caso de una aplicación lógica basada en el plan ISE o de consumo, elija un paso:

   * Para agregar la acción **Transformar XML** al final del flujo de trabajo, seleccione **Nuevo paso**.

   * Para agregar la acción **Transformar XML** entre pasos existentes, mueva el puntero del mouse sobre la flecha que conecta esos pasos para que aparezca el signo más ( **+** ). Haga clic en el signo más y seleccione **Agregar una acción**.

   En el caso de una aplicación lógica basada en el plan estándar, elija un paso:

   * Para agregar la acción **Transformar XML** al final del flujo de trabajo, seleccione el signo más ( **+** ) y luego **Agregar una acción**.

   * Para agregar la acción **Transformar XML** entre pasos existentes, seleccione el signo más ( **+** ) que aparece entre esos pasos y luego **Agregar una acción**.

1. En **Choose an operation** (Elegir una operación), seleccione **Built-in** (Integrada). En el cuadro de búsqueda, escriba `transform xml`. En la lista de acciones, seleccione **Transformar XML**.

1. Para especificar el contenido XML para la transformación, puede usar los datos XML recibidos en la solicitud HTTP. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico.

   La lista de contenido dinámico muestra los tokens en propiedad que representan las salidas de los pasos anteriores en el flujo de trabajo. Si la lista no muestra una propiedad que se espera, compruebe el encabezado de desencadenador o acción en la lista y vea si puede seleccionar **Ver más**.

   En el caso de una aplicación lógica basada en el plan ISE o de consumo, el diseñador tiene el aspecto de este ejemplo:

   ![Captura de pantalla en la que se muestra el diseñador multiinquilino con la lista de contenido dinámico abierta, el cursor en el cuadro "Contenido" y la lista de contenido dinámico abierta](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-multi-tenant.png)

   En el caso de una aplicación lógica basada en el plan estándar, el diseñador tiene el aspecto de este ejemplo:

   ![Captura de pantalla en la que se muestra el diseñador de un solo inquilino con la lista de contenido dinámico abierta, el cursor en el cuadro "Contenido" y la lista de contenido dinámico abierta](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-single-tenant.png)

1. En la lista de contenido dinámico, seleccione el token de propiedad del contenido que quiera validar.

   En este ejemplo se selecciona el token **Cuerpo** del desencadenador.

   > [!NOTE]
   > Asegúrese de que el contenido que selecciona sea XML. Si el contenido no es XML o no tiene codificación base64, debe especificar una expresión que procese el contenido. Por ejemplo, puede usar [funciones de expresión](workflow-definition-language-functions-reference.md) como `base64ToBinary()` para descodificar el contenido o `xml()` para procesarlo como XML.

1. Para especificar la asignación que se va a usar para la transformación, abra la lista **Asignar** y seleccione la asignación que ha agregado anteriormente.

1. Cuando haya terminado, asegúrese de guardar el flujo de trabajo de aplicaciones lógicas.

   Ha terminado de configurar la acción **Transformar XML**. En una aplicación real, es posible que quiera almacenar los datos transformados en una aplicación de línea de negocio (LOB) como SalesForce. Para enviar la salida transformada a Salesforce, agregue una acción Salesforce.

1. Para probar la acción de transformación, desencadene y ejecute el flujo de trabajo. Por ejemplo, en el caso del desencadenador de solicitud, envíe una solicitud a la dirección URL del punto de conexión del desencadenador.

   La acción **Transformar XML** se ejecuta después de que se desencadene el flujo de trabajo y cuando el contenido XML está disponible para la transformación.

## <a name="advanced-capabilities"></a>Capacidades avanzadas

### <a name="reference-assembly-or-custom-code-from-maps"></a>Ensamblado de referencia o código personalizado de asignaciones

En los flujos de trabajo **Logic App (consumo)** , la acción **Transformar XML** admite asignaciones que hacen referencia a un ensamblado externo. Para obtener más información, vea [Incorporación de asignaciones XSLT para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-maps.md#add-assembly).

### <a name="byte-order-mark"></a>Marca de orden de bytes

De manera predeterminada, la respuesta de la transformación se inicia con la marca BOM. Puede acceder a esta funcionalidad solo mientras trabaja en el editor de la vista Código. Para deshabilitar esta funcionalidad, establezca la propiedad `transformOptions` en `disableByteOrderMark`:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Incorporación de asignaciones XSLT para la transformación XML en Azure Logic Apps](logic-apps-enterprise-integration-maps.md)
* [Validación de XML para flujos de trabajo en Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)