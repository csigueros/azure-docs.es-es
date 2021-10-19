---
title: Envío de correo electrónico
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a enviar un correo electrónico con una canalización Azure Data Factory o Azure Synapse.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: 64f7fcf5cfd5c24cb8d34e29572f7fbb1314414a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668910"
---
# <a name="send-an-email-with-an-azure-data-factory-or-azure-synapse-pipeline"></a>Envío de un correo electrónico con una canalización de Azure Data Factory o Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A menudo es necesario enviar notificaciones durante la ejecución de una canalización o después de esta. La notificación proporciona un envío proactivo de alertas y reduce la necesidad de supervisión reactiva para detectar problemas.  En este artículo se muestra cómo configurar notificaciones por correo electrónico desde una canalización de Azure Data Factory o Azure Synapse. 

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
- **Aplicación lógica**. Para desencadenar el envío de un correo electrónico de la canalización, defina el flujo de trabajo con [Logic Apps](../logic-apps/logic-apps-overview.md). Para obtener más información acerca de cómo crear un flujo de trabajo de una aplicación lógica, consulte [Cómo crear una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-email-workflow-in-your-logic-app"></a>Creación del flujo de trabajo de correo electrónico en la aplicación lógica

Cree un flujo de trabajo de aplicación lógica denominado `SendEmailFromPipeline`. Defina el desencadenador del flujo de trabajo como `When an HTTP request is received` y agregue una acción de `Office 365 Outlook – Send an email (V2)`.

:::image type="content" source="media/how-to-send-email/logic-app-workflow-designer.png" alt-text="Muestra el diseñador de flujo de trabajo de aplicación lógica con una acción Enviar correo electrónico (V2) desde un desencadenador de solicitud HTTP.":::

Para el desencadenador de solicitud HTTP, proporcione este JSON para `Request Body JSON Schema`:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

La solicitud HTTP en el Diseñador de aplicación lógica debería tener este aspecto:

:::image type="content" source="media/how-to-send-email/logic-app-http-request-trigger.png" alt-text="Muestra el diseñador de flujo de trabajo de aplicación lógica para el desencadenador de solicitud HTTP con el campo Esquema JSON del cuerpo de la solicitud rellenado.":::

Para la acción **Enviar un correo electrónico (V2)** , personalice el formato del correo electrónico con las propiedades del esquema JSON del cuerpo de la solicitud.

:::image type="content" source="media/how-to-send-email/logic-app-email-action.png" alt-text="Muestra el diseñador de flujo de trabajo de aplicación lógica para la acción Enviar correo electrónico (V2).":::

Guarde el flujo de trabajo. Tome nota de la dirección URL del nuevo flujo de trabajo:

:::image type="content" source="media/how-to-send-email/logic-app-workflow-url.png" alt-text="Muestra la pestaña Información general del flujo de trabajo de la aplicación lógica con la dirección URL del flujo de trabajo resaltada.":::

## <a name="create-a-pipeline-to-trigger-your-logic-app-email-workflow"></a>Creación de una canalización para desencadenar el flujo de trabajo de correo electrónico de la aplicación lógica

Una vez creado el flujo de trabajo de la aplicación lógica para enviar correo electrónico, puede desencadenarlo desde una canalización mediante una actividad **web**.  

1. Cree una canalización y busque la actividad **Web** en la categoría **General** y arrástrela al lienzo de edición.

1. Seleccione la nueva actividad **Web1** y, luego, elija la pestaña **Configuración**.

   Proporcione la dirección URL del flujo de trabajo de aplicación lógica que creó anteriormente en el campo **Dirección URL**.

   Especifique el siguiente JSON en **Cuerpo**.
    ```json
       {
        "message" : "This is a custom dynamic message from your pipeline with run ID @{pipeline().RunId}.",
        "dataFactoryName" : "@{pipeline().DataFactory}", 
        "pipelineName" : "@{pipeline().Pipeline}", 
        "receiver" : "@{pipeline().parameters.receiver}"
       }
    ```
    
    Use expresiones dinámicas para generar mensajes útiles para eventos en las canalizaciones.  Observe que el formato JSON aquí coincide con el formato JSON que definió en la aplicación lógica y también puede personalizarlos según sea necesario.
    
    :::image type="content" source="media/how-to-send-email/pipeline-with-web-activity-calling-logic-app.png" alt-text="Muestra una canalización con una actividad web configurada con la dirección URL del flujo de trabajo de la aplicación lógica y el cuerpo del mensaje JSON.":::

1. Elija el área en segundo plano del diseñador de canalizaciones para seleccionar la página de propiedades de la canalización, agregue un nuevo parámetro denominado receptor y proporcione una dirección de correo electrónico como su valor predeterminado.
   
   En este ejemplo, se proporciona el correo electrónico del receptor de un parámetro de canalización que se define arbitrariamente.  El valor del receptor se puede tomar de cualquier expresión o incluso de orígenes de datos vinculados.

   :::image type="content" source="media/how-to-send-email/pipeline-receiver-email-parameter.png" alt-text="Muestra la configuración del parámetro &quot;receiver&quot; en el diseñador de canalizaciones.":::

1. Publique la canalización y desencadénela manualmente para confirmar que el correo electrónico se envía según lo previsto.

   :::image type="content" source="media/how-to-send-email/pipeline-manually-trigger.png" alt-text="Muestra cómo desencadenar manualmente la canalización."::: 

## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>Adición de mensajes dinámicos con variables del sistema y expresiones

Puede usar [variables del sistema](control-flow-system-variables.md) y [expresiones](control-flow-expression-language-functions.md) para que los mensajes sean dinámicos. Por ejemplo:  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

Las expresiones anteriores devolverán los mensajes de error pertinentes de una actividad de copia no realizada, que se pueden redirigir a la actividad web que envía el correo electrónico. Consulte el artículo [Propiedades de salida de la actividad de copia](copy-activity-monitoring.md) para obtener más detalles.

## <a name="next-steps"></a>Pasos siguientes

[Envío de notificaciones de Microsoft Teams desde una canalización](how-to-send-notifications-to-teams.md)
