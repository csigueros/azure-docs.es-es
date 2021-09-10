---
title: Transformación XML para la integración empresarial B2B
description: Transforme XML mediante asignaciones en Azure Logic Apps con Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 30895da003122b760d6437b3cd14a270482f7a0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105264"
---
# <a name="transform-xml-for-workflows-in-azure-logic-apps"></a>Transformación XML para flujos de trabajo en Azure Logic Apps

En escenarios de integración empresarial de negocio a negocio (B2B), puede que tenga que convertir XML entre formatos. En Azure Logic Apps, el flujo de trabajo de aplicaciones lógicas puede transformar XML mediante una asignación de Lenguaje de transformación basado en hojas de estilo (XSLT) y la acción **Transformar XML**. Una asignación es un documento XML que describe cómo convertir datos de XML a otro formato. Este documento consta de un esquema XML de origen como entrada y un esquema XML de destino como salida.  Puede utilizar las diferentes funciones integradas para administrar o controlar los datos, incluidos aspectos como las manipulaciones de cadenas, las asignaciones condicionales, las expresiones aritméticas, los formateadores de tiempo y fecha e, incluso, las construcciones en bucle.

Por ejemplo, imagine que recibe periódicamente pedidos o facturas B2B de un cliente que usa el formato de fecha añoMesDía (AAAAMMDD), mientras que su organización emplea el formato de fecha mesDíaAño (MMDDAAAA). Puede crear y usar una asignación que transforme el formato de fecha añoMesDía en mesDíaAño antes de almacenar los detalles del pedido o la factura en la base de datos de actividad de los clientes.

Después de [crear una asignación](logic-apps-enterprise-integration-maps.md#create-maps) y confirmar que funciona, agréguela a la cuenta de integración vinculada a la aplicación lógica basada en el plan de consumo multiinquilino o la aplicación lógica basada en el plan ISE, o bien puede agregarla directamente a la aplicación lógica basada en el plan estándar de un solo inquilino. Para obtener más información, vea [Incorporación de asignaciones XSLT para la transformación XML en Azure Logic Apps](logic-apps-enterprise-integration-maps.md). Siempre que el flujo de trabajo incluye la acción **Transformar XML**, la acción se ejecuta cuando se desencadena el flujo de trabajo y cuando el contenido XML está disponible para la transformación.

Si no está familiarizado con las aplicaciones lógicas, vea la siguiente documentación:

* [¿Qué es Azure Logic Apps? Diferencias entre el tipo de recurso y el entorno de host](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md)

* [Creación de flujos de trabajo de un solo inquilino](create-single-tenant-workflows-azure-portal.md)

* [Modelos de medición, facturación y precios de uso de Azure Logic Apps](logic-apps-pricing.md)

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un flujo de trabajo de aplicaciones lógicas que ya se inicie con un desencadenador para poder agregar la acción **Transformar XML** donde sea necesario en el flujo de trabajo.

* Si usa el tipo de recurso **Logic App (estándar)** , no necesita una cuenta de integración, sino que puede agregar asignaciones directamente al recurso de aplicación lógica en Azure Portal o Visual Studio Code. Actualmente solo se admite XSLT 1.0. Luego puede usar estas asignaciones en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*.

* Si usa el tipo de recurso **Logic App (consumo)** , debe tener un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) donde poder almacenar las asignaciones y otros artefactos que va a usar en las soluciones de integración empresarial y de negocio a negocio (B2B). Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existir en la misma ubicación o región de Azure que el recurso de aplicación lógica donde piensa usar la acción **Transformar XML**.

  * Estar [vinculado](logic-apps-enterprise-integration-create-integration-account.md#link-account) al recurso de aplicación lógica donde quiere usar la acción **Transformar XML**.

## <a name="add-transform-xml-action"></a>Incorporación de una acción Transformar XML

1. En [Azure Portal](https://portal.azure.com), abra la aplicación lógica y el flujo de trabajo en la vista del diseñador.

1. Si tiene una aplicación lógica en blanco sin desencadenador, agregue cualquier desencadenador que quiera. En este ejemplo se usa el desencadenador de solicitud. De lo contrario, continúe con el paso siguiente.

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

   La lista de contenido dinámico muestra los tokens en propiedad que representan las salidas de los pasos anteriores en el flujo de trabajo. Si la lista no muestra una propiedad que se espera, compruebe el encabezado del desencadenador o la acción en la lista y vea si puede seleccionar **Ver más**.

   En el caso de una aplicación lógica basada en el plan ISE o de consumo, el diseñador tiene el aspecto de este ejemplo:

   ![Captura de pantalla que muestra el diseñador multiinquilino con la lista de contenido dinámico abierta, el cursor en el cuadro "Contenido" y la lista de contenido dinámico abierta.](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-multi-tenant.png)

   En el caso de una aplicación lógica basada en el plan estándar, el diseñador tiene el aspecto de este ejemplo:

   ![Captura de pantalla que muestra el diseñador de un solo inquilino con la lista de contenido dinámico abierta, el cursor en el cuadro "Contenido" y la lista de contenido dinámico abierta](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-single-tenant.png)

1. En la lista de contenido dinámico, seleccione el token de propiedad del contenido que quiere validar.

   En este ejemplo se selecciona el token **Cuerpo** del desencadenador.

   > [!NOTE]
   > Asegúrese de que el contenido que selecciona sea XML. Si el contenido no es XML o no tiene codificación base64, debe especificar una expresión que procese el contenido. Por ejemplo, puede usar [funciones de expresión](workflow-definition-language-functions-reference.md) como `base64ToBinary()` para descodificar el contenido o `xml()` para procesarlo como XML.

1. Para especificar la asignación que se va a usar para la transformación, abra la lista **Asignar** y seleccione la asignación que ha agregado anteriormente.

1. Cuando haya terminado, asegúrese de guardar el flujo de trabajo de aplicaciones lógicas.

   Ha terminado de configurar la acción **Transformar XML**. En una aplicación real, es posible que quiera almacenar los datos transformados en una aplicación de línea de negocio (LOB) como SalesForce. Para enviar la salida transformada a Salesforce, agregue una acción Salesforce.

1. Para probar la acción de transformación, desencadene y ejecute el flujo de trabajo. Por ejemplo, en el caso del desencadenador de solicitud, envíe una solicitud a la dirección URL del punto de conexión del desencadenador.

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