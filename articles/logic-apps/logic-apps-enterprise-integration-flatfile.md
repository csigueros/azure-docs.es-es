---
title: Codificación o descodificación de archivos planos
description: Codificación o descodificación de archivos planos para integración empresarial en Azure Logic Apps mediante Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 05/01/2021
ms.openlocfilehash: 73b7538c41f1a560d07a702660a28f41b2c0e1de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072453"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Codificación y descodificación de archivos planos en Azure Logic Apps

Antes de enviar contenido XML a un socio comercial en un escenario de negocio a negocio (B2B), le recomendamos que primero codifique el contenido. Al compilar el flujo de trabajo de una aplicación lógica, puede codificar y descodificar archivos planos mediante el conector de **archivos planos**. El flujo de trabajo de la aplicación lógica puede obtener este contenido XML de diversos orígenes, como el desencadenador de solicitud, otra aplicación u otros [conectores compatibles con Azure Logic Apps](../connectors/apis-list.md).

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta y una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) asociada con la suscripción a Azure y [vinculada a las aplicaciones lógicas](./logic-apps-enterprise-integration-create-integration-account.md#link-account) en las que planea usar el conector de **archivos planos**. Tanto la cuenta de integración como la aplicación de lógica deben existir en la misma ubicación o región de Azure.

* Al menos dos [socios comerciales](logic-apps-enterprise-integration-partners.md) que ya haya definido en su cuenta de integración

* El [esquema](logic-apps-enterprise-integration-schemas.md) del archivo plano que ha cargado a la cuenta de integración para codificar o descodificar el contenido XML

* La aplicación lógica en la que quiere usar el conector de **archivos planos** y un desencadenador que inicie el flujo de trabajo de la aplicación lógica. El conector de **archivos planos** proporciona únicamente las acciones, no los desencadenadores. Puede usar el desencadenador u otra acción para insertar el contenido XML en el flujo de trabajo de la aplicación lógica para la codificación o descodificación. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) e [Inicio rápido: Creación de la primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Límites

Asegúrese de que los grupos XML contenidos en el esquema de archivo plano que genere no tengan números excesivos de la propiedad `max count` establecida en un valor *mayor que 1*. Evite anidar un grupo XML con un valor de propiedad `max count` mayor que 1 dentro de otro grupo  XML con una propiedad `max count` mayor que 1.

Cada vez que el esquema de archivo plano permite elegir el fragmento siguiente, el motor de Azure Logic Apps que analiza el esquema genera un *símbolo* y una *predicción* para ese fragmento. Si el esquema permite demasiadas construcciones de este tipo, por ejemplo, más de 100 000, la expansión del esquema se vuelve excesivamente grande, lo que consume demasiados recursos y tiempo.

## <a name="add-flat-file-encode-action"></a>Agregar una acción de codificación de archivo plano

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el desencadenador o la acción de su aplicación lógica, seleccione **Nuevo paso** > **Agregar una acción**. En este ejemplo se usa el desencadenador de solicitud, denominado **Cuando se recibe una solicitud HTTP** y controla las solicitudes entrantes desde fuera de la aplicación lógica.

   > [!TIP]
   > Proporcionar un esquema JSON es opcional. Si tiene una carga de ejemplo de la solicitud entrante, seleccione **Usar una carga de ejemplo para generar el esquema**, ingrese la carga de ejemplo y seleccione **Listo**. El esquema aparece en el cuadro **Esquema JSON del cuerpo de la solicitud**.

1. En **Elegir una acción**, escriba `flat file`. En la lista de acciones, seleccione esta acción: **Codificación de archivo plano**

   ![Seleccionar la acción "Codificación de archivos planos"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico. En la lista, dentro de la sección **Cuando se recibe una solicitud HTTP**, seleccione la propiedad **Cuerpo**, que contiene la salida del cuerpo de la solicitud del desencadenador y el contenido que se va a codificar.

   ![Seleccionar contenido de la lista de contenido dinámico para codificarlo](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Si no ve la propiedad **Cuerpo** en la lista de contenido dinámico, seleccione **Ver más** junto a la etiqueta de la sección **Cuando se recibe una solicitud HTTP**.
   > También puede especificar directamente el contenido que desea descodificar en el cuadro **Contenido**.

1. En la lista **Nombre del esquema**, seleccione el esquema que se encuentra en la cuenta de integración vinculada para usarlo para la codificación, por ejemplo:

   ![Selección del esquema que se va a usar para la codificación](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Si no aparece ningún esquema en la lista, la cuenta de integración no contiene ningún archivo de esquema que se pueda usar para la codificación. Cargue el esquema que desea usar a su cuenta de integración.

1. Guarde la aplicación lógica. Para probar el conector, realice una solicitud al punto de conexión HTTPS, que aparece en la propiedad **Dirección URL de HTTP POST** del desencadenador de solicitud e incluya el contenido XML que desea codificar en el cuerpo de la solicitud.

Ahora ya ha terminado de configurar la acción de codificación de archivos planos. En una aplicación real, podría almacenar los datos codificados en una aplicación de línea de negocio (LOB) como Salesforce. O bien, puede enviar los datos codificados a un socio comercial. Para enviar el resultado de la acción de codificación a Salesforce o a su socio comercial, use los otros [conectores disponibles en Azure Logic Apps](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Agregar una acción de descodificación de archivo plano

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el desencadenador o la acción de su aplicación lógica, seleccione **Nuevo paso** > **Agregar una acción**. En este ejemplo se usa el desencadenador de solicitud, denominado **Cuando se recibe una solicitud HTTP** y controla las solicitudes entrantes desde fuera de la aplicación lógica.

   > [!TIP]
   > Proporcionar un esquema JSON es opcional. Si tiene una carga de ejemplo de la solicitud entrante, seleccione **Usar una carga de ejemplo para generar el esquema**, ingrese la carga de ejemplo y seleccione **Listo**. El esquema aparece en el cuadro **Esquema JSON del cuerpo de la solicitud**.

1. En **Elegir una acción**, escriba `flat file`. En la lista de acciones, seleccione esta acción: **Descodificación de archivo plano**

   ![Seleccionar la acción "Descodificación de archivo plano"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Haga clic en el cuadro **Contenido** para que aparezca la lista de contenido dinámico. En la lista, dentro de la sección **Cuando se recibe una solicitud HTTP**, seleccione la propiedad **Cuerpo**, que contiene la salida del cuerpo de la solicitud del desencadenador y el contenido que se va a descodificar.

   ![Seleccionar contenido de la lista de contenido dinámico para descodificarlo](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Si no ve la propiedad **Cuerpo** en la lista de contenido dinámico, seleccione **Ver más** junto a la etiqueta de la sección **Cuando se recibe una solicitud HTTP**. También puede especificar directamente el contenido que desea descodificar en el cuadro **Contenido**.

1. En la lista **Nombre del esquema**, seleccione el esquema que se encuentra en la cuenta de integración vinculada para usarlo para la descodificación, por ejemplo:

   ![Selección del esquema que se va a usar para la descodificación](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Si no aparece ningún esquema en la lista, la cuenta de integración no contiene ningún archivo de esquema que se pueda usar para la descodificación. Cargue el esquema que desea usar a su cuenta de integración.

1. Guarde la aplicación lógica. Para probar el conector, realice una solicitud al punto de conexión HTTPS, que aparece en la propiedad **Dirección URL de HTTP POST** del desencadenador de solicitud e incluya el contenido XML que desea descodificar en el cuerpo de la solicitud.

Ahora ya ha terminado de configurar la acción de descodificación de archivos planos. En una aplicación real, podría almacenar los datos descodificados en una aplicación de línea de negocio (LOB) como Salesforce. O bien, puede enviar los datos descodificados a un socio comercial. Para enviar el resultado de la acción de descodificación a Salesforce o a su socio comercial, use los otros [conectores disponibles en Azure Logic Apps](../connectors/apis-list.md).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información acerca de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
