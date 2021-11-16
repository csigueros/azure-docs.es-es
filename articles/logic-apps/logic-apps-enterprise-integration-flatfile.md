---
title: Codificación o descodificación de archivos planos
description: Codificación o descodificación de archivos planos para integración empresarial en Azure Logic Apps mediante Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 5956f8fb7a1de3baaac0a69ef9e5b63d445416eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327127"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Codificación y descodificación de archivos planos en Azure Logic Apps

Antes de enviar contenido XML a un socio comercial en un escenario de negocio a negocio (B2B), le recomendamos que primero codifique el contenido. Al crear el flujo de trabajo de una aplicación lógica, puede codificar y descodificar archivos planos mediante las acciones [integradas](../connectors/built-in.md#integration-account-built-in-actions) de **archivos planos**.

Aunque no hay desencadenadores de **archivo plano** disponibles, puede usar otro desencadenador o acción para obtener o alimentar el contenido XML de varios orígenes en el flujo de trabajo para codificar o codificar. Por ejemplo, puede usar el desencadenador de solicitud, otra aplicación u otros [conectores compatibles con Azure Logic Apps](../connectors/apis-list.md). Puede usar acciones de **archivo plano** con flujos de trabajo en los tipos de recurso [**Aplicación lógica (Consumo)** y **Aplicación lógica (Estándar)** ](single-tenant-overview-compare.md).

> [!NOTE]
> En el caso de la **Aplicación lógica (Estándar)** , las acciones de **archivo plano** están actualmente en versión preliminar. 

En este artículo, se muestra cómo agregar las acciones de codificación y descodificación de archivo plano a un flujo de trabajo de aplicación lógica existente. Si no conoce las aplicaciones lógicas, consulte la siguiente documentación:

* [¿Qué es Azure Logic Apps?](logic-apps-overview.md)
* [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Si usa el [tipo de recurso **Aplicación lógica (consumo)**](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración necesita los elementos siguientes:

    * El [esquema](logic-apps-enterprise-integration-schemas.md) de archivo plano que se va a usar para codificar o descodificar el contenido XML.

    * Un [vínculo al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Si usa el [tipo de recurso **Aplicación lógica (estándar)**](logic-apps-overview.md#resource-type-and-host-environment-differences), no almacene los esquemas en la cuenta de integración. En su lugar, puede [agregar esquemas directamente al recurso de aplicación lógica](logic-apps-enterprise-integration-schemas.md) mediante Azure Portal o Visual Studio Code. Posteriormente puede usar dichas asignaciones en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*.

    Todavía necesita una cuenta de integración para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](logic-apps-enterprise-integration-edifact.md). Pero no es necesario vincular el recurso de aplicación lógica a la cuenta de integración, por lo que la funcionalidad de vinculación no existe. La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

    > [!NOTE]
    > Actualmente, solo el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [Consumo]) admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Logic App (Standard)** (Aplicación lógica [Estándar]) no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* El flujo de trabajo de aplicación lógica, en blanco o ya existente, en el que quiere usar la acción de **archivo plano**.

  Si tiene un flujo de trabajo en blanco, use el desencadenador que desee para iniciar el flujo de trabajo. En este ejemplo se usa el desencadenador de solicitud.

## <a name="limits"></a>Límites

Asegúrese de que los grupos XML contenidos en el esquema de archivo plano que genere no tengan números excesivos de la propiedad `max count` establecida en un valor *mayor que 1*. Evite anidar un grupo XML con un valor de propiedad `max count` mayor que 1 dentro de otro grupo  XML con una propiedad `max count` mayor que 1.

Cada vez que el esquema de archivo plano permite elegir el fragmento siguiente, el motor de Azure Logic Apps que analiza el esquema genera un *símbolo* y una *predicción* para ese fragmento. Si el esquema permite demasiadas construcciones de este tipo, por ejemplo, más de 100 000, la expansión del esquema se vuelve excesivamente grande, lo que consume demasiados recursos y tiempo.

## <a name="add-flat-file-encoding-action"></a>Adición de la acción de codificación de archivo plano

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. Si tiene una aplicación lógica en blanco que no tiene un desencadenador, agregue el desencadenador que desee. De lo contrario, continúe con el paso siguiente.

   En este ejemplo, se usa el desencadenador de solicitud, llamado **Cuando se recibe una solicitud HTTP**, que controla las solicitudes entrantes desde fuera del flujo de trabajo de la aplicación lógica. Para agregar el desencadenador de solicitud, siga estos pasos:

   1. En el cuadro de búsqueda del diseñador, seleccione **Integrado**. En el cuadro de búsqueda del diseñador, escriba `HTTP request`.

   1. En la lista de desencadenadores, seleccione el desencadenador de solicitud llamado **Cuando se recibe una solicitud HTTP**.

   > [!TIP]
   > Proporcionar un esquema JSON es opcional. Si tiene una carga de ejemplo de la solicitud entrante, seleccione **Usar una carga de ejemplo para generar el esquema**, ingrese la carga de ejemplo y seleccione **Listo**. El esquema aparece en el cuadro **Esquema JSON del cuerpo de la solicitud**.

1. En el paso del flujo de trabajo en el que quiere agregar la acción **Codificación de archivo plano**, elija una opción:

   * Para agregar la acción **Codificación de archivo plano** al final del flujo de trabajo, seleccione **Nuevo paso**.

   * Para agregar la acción **Codificación de archivo plano** entre pasos existentes, mueva el puntero del ratón sobre la flecha que conecta dichos pasos para que aparezca el signo más ( **+** ). Haga clic en el signo más y seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, escriba `flat file`. En la lista de acciones, seleccione la acción llamada **Codificación de archivo plano**.

   ![Captura de pantalla que muestra Azure Portal y el diseñador del plan de consumo con el texto "archivo plano" en el cuadro de búsqueda y la acción "Codificación de archivo plano" seleccionada.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-consumption.png)

1. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico. En la lista, dentro de la sección **Cuando se recibe una solicitud HTTP**, seleccione la propiedad **Cuerpo**, que contiene la salida del cuerpo de la solicitud del desencadenador y el contenido que se va a codificar.

   ![Captura de pantalla que muestra el diseñador del plan de consumo y la propiedad "Contenido" con la lista de contenido dinámico y el contenido seleccionados para la codificación.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-consumption.png)

   > [!TIP]
   > Si no aparece la propiedad **Cuerpo** en la lista de contenido dinámico, seleccione **Ver más** junto a la etiqueta de la sección **Cuando se recibe una solicitud HTTP**.
   > También puede especificar directamente el contenido que desea descodificar en el cuadro **Contenido**.

1. En la lista **Nombre del esquema**, seleccione el esquema que se encuentra en la cuenta de integración vinculada para usarlo para la codificación, por ejemplo:

   ![Captura de pantalla que muestra el diseñador del plan de consumo y la lista "Nombre de esquema" abierta con el esquema seleccionado que se usará para la codificación.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-consumption.png)

   > [!NOTE]
   > Si no aparece ningún esquema en la lista, la cuenta de integración no contiene ningún archivo de esquema que se pueda usar para la codificación. Cargue el esquema que desea usar a su cuenta de integración.

1. Guarde el flujo de trabajo. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Para probar el flujo de trabajo, envíe una solicitud al punto de conexión HTTPS que aparece en la propiedad **Dirección URL de HTTP POST** del desencadenador de solicitud e incluya el contenido XML que desea codificar en el cuerpo de la solicitud.

Ahora ya ha terminado de configurar la acción de codificación de archivos planos. En una aplicación real, podría almacenar los datos codificados en una aplicación de línea de negocio (LOB) como Salesforce. O bien, puede enviar los datos codificados a un socio comercial. Para enviar el resultado de la acción de codificación a Salesforce o a su socio comercial, use los otros [conectores disponibles en Azure Logic Apps](../connectors/apis-list.md).

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. Si tiene una aplicación lógica en blanco que no tiene un desencadenador, agregue el desencadenador que desee. De lo contrario, continúe con el paso siguiente.

   En este ejemplo, se usa el desencadenador de solicitud, llamado **Cuando se recibe una solicitud HTTP**, que controla las solicitudes entrantes desde fuera del flujo de trabajo de la aplicación lógica. Para agregar el desencadenador de solicitud, siga estos pasos:

   1. En el diseñador, seleccione **Elegir una operación**. En el panel **Elegir una operación** que se abre, debajo del cuadro de búsqueda, seleccione **Integrado**.

   1. En el cuadro de búsqueda, escriba `HTTP request`. En la lista de desencadenadores, seleccione el desencadenador de solicitud llamado **Cuando se recibe una solicitud HTTP**.

     > [!TIP]
     > Proporcionar un esquema JSON es opcional. Si tiene una carga de ejemplo de la solicitud entrante, seleccione **Usar una carga de ejemplo para generar el esquema**, ingrese la carga de ejemplo y seleccione **Listo**. El esquema aparece en el cuadro **Esquema JSON del cuerpo de la solicitud**.

1. En el paso del flujo de trabajo en el que quiere agregar la acción **Codificación de archivo plano**, elija una opción:

   * Para agregar la acción **Codificación de archivo plano** al final del flujo de trabajo, seleccione el signo más ( **+** ) y, a continuación, **Agregar una acción**.

   * Para agregar la acción **Codificación de archivo plano** entre pasos existentes, seleccione el signo más ( **+** ) que aparece entre los pasos y, a continuación, seleccione **Permite agregar un nuevo paso**.

1. En el panel **Elegir una operación** que se abre, debajo del cuadro de búsqueda, seleccione **Integrado**.

1. En el cuadro de búsqueda, escriba `flat file`. En la lista de acciones, seleccione la acción llamada **Codificación de archivo plano**.

   ![Captura de pantalla que muestra Azure Portal y el diseñador de flujos de trabajo Estándar con el texto "archivo plano" en el cuadro de búsqueda y la acción "Codificación de archivo plano" seleccionada.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-standard.png)

1. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico. En la lista, dentro de la sección **Cuando se recibe una solicitud HTTP**, seleccione la propiedad **Cuerpo**, que contiene la salida del cuerpo de la solicitud del desencadenador y el contenido que se va a codificar.

   ![Captura de pantalla que muestra el diseñador de flujos de trabajo Estándar y la propiedad "Contenido" con la lista de contenido dinámico y el contenido seleccionados para la codificación.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-standard.png)

   > [!TIP]
   > Si no aparece la propiedad **Cuerpo** en la lista de contenido dinámico, seleccione **Ver más** junto a la etiqueta de la sección **Cuando se recibe una solicitud HTTP**.
   > También puede especificar directamente el contenido que se va a codificar en el cuadro **Contenido**.

1. En la lista **Nombre**, seleccione el esquema que ha cargado previamente en el recurso de la aplicación lógica para la codificación, por ejemplo:

   ![Captura de pantalla que muestra el diseñador de flujos de trabajo Estándar y la lista "Nombre" abierta con el esquema seleccionado que se usará para la codificación.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-standard.png)

   > [!NOTE]
   > Si no aparece ningún esquema en la lista, el recurso de la aplicación lógica Estándar no contiene ningún archivo de esquema que se pueda usar para la codificación. Obtenga información sobre cómo [cargar el esquema que desea usar en el recurso de la aplicación lógica Estándar](logic-apps-enterprise-integration-schemas.md).

1. Guarde el flujo de trabajo. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Para probar el flujo de trabajo, envíe una solicitud al punto de conexión HTTPS que aparece en la propiedad **Dirección URL de HTTP POST** del desencadenador de solicitud e incluya el contenido XML que desea codificar en el cuerpo de la solicitud.

Ahora ya ha terminado de configurar la acción de codificación de archivos planos. En una aplicación real, podría almacenar los datos codificados en una aplicación de línea de negocio (LOB) como Salesforce. O bien, puede enviar los datos codificados a un socio comercial. Para enviar el resultado de la acción de codificación a Salesforce o a su socio comercial, use los otros [conectores disponibles en Azure Logic Apps](../connectors/apis-list.md).

---

## <a name="add-flat-file-decoding-action"></a>Adición de la acción Descodificación de archivo plano

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. Si tiene una aplicación lógica en blanco que no tiene un desencadenador, agregue el desencadenador que desee. De lo contrario, continúe con el paso siguiente.

   En este ejemplo, se usa el desencadenador de solicitud, llamado **Cuando se recibe una solicitud HTTP**, que controla las solicitudes entrantes desde fuera del flujo de trabajo de la aplicación lógica. Para agregar el desencadenador de solicitud, siga estos pasos:

   1. En el cuadro de búsqueda del diseñador, seleccione **Integrado**. En el cuadro de búsqueda del diseñador, escriba `HTTP request`.

   1. En la lista de desencadenadores, seleccione el desencadenador de solicitud llamado **Cuando se recibe una solicitud HTTP**.

   > [!TIP]
   > Proporcionar un esquema JSON es opcional. Si tiene una carga de ejemplo de la solicitud entrante, seleccione **Usar una carga de ejemplo para generar el esquema**, ingrese la carga de ejemplo y seleccione **Listo**. El esquema aparece en el cuadro **Esquema JSON del cuerpo de la solicitud**.

1. En el paso del flujo de trabajo en el que quiere agregar la acción **Descodificación de archivo plano**, elija una opción:

   * Para agregar la acción **Descodificación de archivo plano** al final del flujo de trabajo, seleccione **Nuevo paso**.

   * Para agregar la acción **Descodificación de archivo plano** entre pasos existentes, mueva el puntero del ratón sobre la flecha que conecta dichos pasos para que aparezca el signo más ( **+** ). Haga clic en el signo más y seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, escriba `flat file`. En la lista de acciones, seleccione la acción llamada **Descodificación de archivo plano**.

   ![Captura de pantalla que muestra Azure Portal y el diseñador del plan de consumo con el texto "archivo plano" en el cuadro de búsqueda y la acción "Descodificación de archivo plano" seleccionada.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-consumption.png)

1. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico. En la lista, dentro de la sección **Cuando se recibe una solicitud HTTP**, seleccione la propiedad **Cuerpo**, que contiene la salida del cuerpo de la solicitud del desencadenador y el contenido que se va a descodificar.

   ![Captura de pantalla que muestra el diseñador del plan de consumo y la propiedad "Contenido" con la lista de contenido dinámico y el contenido seleccionados para la descodificación.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-consumption.png)

   > [!TIP]
   > Si no aparece la propiedad **Cuerpo** en la lista de contenido dinámico, seleccione **Ver más** junto a la etiqueta de la sección **Cuando se recibe una solicitud HTTP**. También puede especificar directamente el contenido que desea descodificar en el cuadro **Contenido**.

1. En la lista **Nombre del esquema**, seleccione el esquema que se encuentra en la cuenta de integración vinculada para usarlo para la descodificación, por ejemplo:

   ![Captura de pantalla que muestra el diseñador del plan de consumo y la lista "Nombre de esquema" abierta con el esquema seleccionado que se usará para la descodificación.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-consumption.png)

   > [!NOTE]
   > Si no aparece ningún esquema en la lista, la cuenta de integración no contiene ningún archivo de esquema que se pueda usar para la descodificación. Cargue el esquema que desea usar a su cuenta de integración.

1. Guarde el flujo de trabajo. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Para probar el flujo de trabajo, envíe una solicitud al punto de conexión HTTPS que aparece en la propiedad **Dirección URL de HTTP POST** del desencadenador de solicitud e incluya el contenido XML que desea descodificar en el cuerpo de la solicitud.

Ahora ya ha terminado de configurar la acción de descodificación de archivos planos. En una aplicación real, podría almacenar los datos descodificados en una aplicación de línea de negocio (LOB) como Salesforce. O bien, puede enviar los datos descodificados a un socio comercial. Para enviar el resultado de la acción de descodificación a Salesforce o a su socio comercial, use los otros [conectores disponibles en Azure Logic Apps](../connectors/apis-list.md).

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. Si tiene una aplicación lógica en blanco que no tiene un desencadenador, agregue el desencadenador que desee. De lo contrario, continúe con el paso siguiente.

   En este ejemplo, se usa el desencadenador de solicitud, llamado **Cuando se recibe una solicitud HTTP**, que controla las solicitudes entrantes desde fuera del flujo de trabajo de la aplicación lógica. Para agregar el desencadenador de solicitud, siga estos pasos:

   1. En el diseñador, seleccione **Elegir una operación**. En el panel **Elegir una operación** que se abre, debajo del cuadro de búsqueda, seleccione **Integrado**.

   1. En el cuadro de búsqueda, escriba `HTTP request`. En la lista de desencadenadores, seleccione el desencadenador de solicitud llamado **Cuando se recibe una solicitud HTTP**.

     > [!TIP]
     > Proporcionar un esquema JSON es opcional. Si tiene una carga de ejemplo de la solicitud entrante, seleccione **Usar una carga de ejemplo para generar el esquema**, ingrese la carga de ejemplo y seleccione **Listo**. El esquema aparece en el cuadro **Esquema JSON del cuerpo de la solicitud**.

1. En el paso del flujo de trabajo en el que quiere agregar la acción **Descodificación de archivo plano**, elija una opción:

   * Para agregar la acción **Descodificación de archivo plano** al final del flujo de trabajo, seleccione el signo más ( **+** ) y, a continuación, **Agregar una acción**.

   * Para agregar la acción **Descodificación de archivo plano** entre pasos existentes, seleccione el signo más ( **+** ) que aparece entre los pasos y, a continuación, seleccione **Permite agregar un nuevo paso**.

1. En el panel **Elegir una operación** que se abre, debajo del cuadro de búsqueda, seleccione **Integrado**.

1. En el cuadro de búsqueda, escriba `flat file`. En la lista de acciones, seleccione la acción llamada **Descodificación de archivo plano**.

   ![Captura de pantalla que muestra Azure Portal y el diseñador de flujos de trabajo Estándar con el texto "archivo plano" en el cuadro de búsqueda y la acción "Descodificación de archivo plano" seleccionada.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-standard.png)

1. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico. En la lista, dentro de la sección **Cuando se recibe una solicitud HTTP**, seleccione la propiedad **Cuerpo**, que contiene la salida del cuerpo de la solicitud del desencadenador y el contenido que se va a descodificar.

   ![Captura de pantalla que muestra el diseñador de flujos de trabajo Estándar y la propiedad "Contenido" con la lista de contenido dinámico y el contenido seleccionados para la descodificación.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-standard.png)

   > [!TIP]
   > Si no aparece la propiedad **Cuerpo** en la lista de contenido dinámico, seleccione **Ver más** junto a la etiqueta de la sección **Cuando se recibe una solicitud HTTP**.
   > También puede especificar directamente el contenido que desea descodificar en el cuadro **Contenido**.

1. En la lista **Nombre**, seleccione el esquema que ha cargado previamente en el recurso de la aplicación lógica para la descodificación, por ejemplo:

   ![Captura de pantalla que muestra el diseñador de flujos de trabajo Estándar y la lista "Nombre" abierta con el esquema seleccionado que se usará para la descodificación.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-standard.png)

   > [!NOTE]
   > Si no aparece ningún esquema en la lista, el recurso de la aplicación lógica Estándar no contiene ningún archivo de esquema que se pueda usar para la descodificación. Aprenda a [cargar el esquema que quiere usar en el recurso de aplicación lógica estándar](logic-apps-enterprise-integration-schemas.md).

1. Guarde el flujo de trabajo. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Para probar el flujo de trabajo, envíe una solicitud al punto de conexión HTTPS que aparece en la propiedad **Dirección URL de HTTP POST** del desencadenador de solicitud e incluya el contenido XML que desea descodificar en el cuerpo de la solicitud.

Ahora ya ha terminado de configurar la acción de descodificación de archivos planos. En una aplicación real, podría almacenar los datos descodificados en una aplicación de línea de negocio (LOB) como Salesforce. O bien, puede enviar los datos descodificados a un socio comercial. Para enviar el resultado de la acción de descodificación a Salesforce o a su socio comercial, use los otros [conectores disponibles en Azure Logic Apps](../connectors/apis-list.md).

---

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información acerca de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
