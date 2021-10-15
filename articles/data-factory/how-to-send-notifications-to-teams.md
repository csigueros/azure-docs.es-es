---
title: Envío de notificaciones a un canal de Microsoft Teams
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo enviar notificaciones a un canal de Microsoft Teams desde una canalización de Azure Data Factory o Synapse Analytics.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.custom: synapse
ms.topic: how-to
ms.subservice: tutorials
ms.date: 09/29/2021
ms.openlocfilehash: d1a23e166c322a4a74c3ec175ea672d78ed23de9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367648"
---
# <a name="send-notifications-to-a-microsoft-teams-channel-from-an-azure-data-factory-or-synapse-analytics-pipeline"></a>Envío de notificaciones a un canal de Microsoft Teams desde una canalización de Azure Data Factory o Synapse Analytics

A menudo es necesario enviar notificaciones durante la ejecución de una canalización o después de esta. La notificación proporciona un envío proactivo de alertas y reduce la necesidad de supervisión reactiva para detectar problemas.  Puede obtener información sobre [cómo enviar notificaciones por correo electrónico mediante aplicaciones lógicas](tutorial-control-flow-portal.md#create-email-workflow-endpoints) que una factoría de datos o una canalización de Synapse pueden invocar.  Muchas empresas también usan cada vez más Microsoft Teams para la colaboración.  En este artículo se muestra cómo configurar notificaciones de alertas de canalización en Microsoft Teams. 

## <a name="prerequisites"></a>Requisitos previos

Para poder enviar notificaciones a Teams desde las canalizaciones, debe crear un [webhook](/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using) de entrada para el canal de Teams. Si necesita crear un nuevo canal de Teams para este fin, consulte la [documentación de Teams](https://support.microsoft.com/office/create-a-channel-in-teams-fda0b75e-5b90-4fb8-8857-7e102b014525).  

1.  Abra Microsoft Teams y vaya a la pestaña Aplicaciones. Busque "Webhook entrante" y seleccione el conector de webhook entrante.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-incoming-webhook-connector.png" alt-text="Muestra la aplicación Webhook entrante en la pestaña Aplicaciones de Teams.":::

1.  Agregue el conector al sitio de Teams al que desee enviar las notificaciones.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-connector-to-site.png" alt-text="Resalta el botón &quot;Agregar a un equipo&quot; de la aplicación Webhook entrante.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-prod-notifications.png" alt-text="Muestra la solicitud de selección de equipo en el cuadro de diálogo de configuración de la aplicación Webhook entrante en Teams.":::

1.  Asigne al webhook el nombre adecuado y, opcionalmente, cargue un icono para identificar los mensajes.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-icon.png" alt-text="Resalta la propiedad de nombre, la carga de imágenes opcional y el botón &quot;Crear&quot; en la página de opciones de Webhook entrante.":::  

1.  Copie el almacén de la dirección URL del webhook que se genera durante la creación para su uso posterior en ADF.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-copy-webhook-url.png" alt-text="Muestra la nueva dirección URL del webhook en la página de opciones Webhook entrante después de la creación.":::

1.  Puede ver la notificación en el canal donde agrega el conector de webhook.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-channel-notification.png" alt-text="Muestra la notificación en el canal de Teams en el que agregó el conector del webhook.":::
        
## <a name="steps-to-send-notifications-on-teams-channel-from-a-pipeline"></a>Pasos para enviar notificaciones en el canal de Teams desde una canalización:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1.  Cree una nueva **canalización a partir de una plantilla**. La galería de plantillas proporciona una plantilla de canalización que facilita la introducción a las notificaciones de Teams.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template.png" alt-text="Muestra el menú &quot;Canalización a partir de plantilla &quot; en Azure Data Factory Studio.":::

1.  Busque "teams" y, a continuación, seleccione y use la plantilla **Enviar una notificación a un canal de Microsoft Teams**.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog.png" alt-text="Muestra la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot; en la galería de plantillas.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template.png" alt-text="Muestra los detalles de la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot; después de seleccionarla en la galería de plantillas.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Muestra las propiedades de la canalización creada por la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot;.":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

1.  Cree una nueva **canalización a partir de una plantilla**. La galería de plantillas proporciona una plantilla de canalización que facilita la introducción a las notificaciones de Teams.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template-synapse.png" alt-text="Muestra el menú &quot;Canalización a partir de plantilla &quot; en Azure Data Factory Studio.":::

1.  Busque "teams" y, a continuación, seleccione y use la plantilla **Enviar una notificación a un canal de Microsoft Teams**.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog-synapse.png" alt-text="Muestra la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot; en la galería de plantillas.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template-synapse.png" alt-text="Muestra los detalles de la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot; después de seleccionarla en la galería de plantillas.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Muestra las propiedades de la canalización creada por la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot;.":::

---

3.  Se recomienda agregar el **identificador de suscripción** de Data Factory, el **grupo de recursos** y la **dirección URL del webhook de Teams** (consulte los [requisitos previos](#prerequisites)) para el valor predeterminado de los parámetros correspondientes.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/webhook-recommended-properties.png" alt-text="Muestra las propiedades recomendadas de la canalización creada por la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot;.":::

    Estos parámetros se usan para construir la dirección URL de supervisión. Supongamos que no proporciona una suscripción y un grupo de recursos válidos (de la misma factoría de datos a la que pertenecen las canalizaciones). En ese caso, la notificación no contendrá una dirección URL de supervisión de canalización válida, pero los mensajes seguirán funcionando.  Además, agregar estos parámetros ayuda a evitar la necesidad de pasar siempre esos valores desde otra canalización. Si piensa controlar esos valores a través de un enfoque basado en metadatos, debe modificarlos en consecuencia.
    
1.  Agregue una actividad de **ejecución de canalización** en la canalización desde la que desea enviar notificaciones en el canal de Teams. Seleccione la canalización generada a partir de la plantilla **Enviar una notificación a un canal de Microsoft Teams** como la **canalización invocada** en la actividad de **ejecución de canalización**.

     :::image type="content" source="media/how-to-send-notifications-to-teams/execute-pipeline-activity.png" alt-text="Muestra la actividad de &quot;ejecución de canalización&quot; en la canalización creada por la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot;.":::

1.  Personalice los parámetros según sea necesario en función del tipo de actividad.

    :::image type="content" source="media/how-to-send-notifications-to-teams/customize-parameters-by-activity-type.png" alt-text="Muestra la personalización de los parámetros en la canalización creada por la plantilla &quot;Enviar una notificación a un canal de Microsoft Teams&quot;.":::   
  
1.  Reciba notificaciones en Teams.

    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-notifications-view-pipeline-run.png" alt-text="Muestra las notificaciones de canalización en un canal de Teams":::.
## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>Adición de mensajes dinámicos con variables del sistema y expresiones

Puede usar [variables del sistema](control-flow-system-variables.md) y [expresiones](control-flow-expression-language-functions.md) para que los mensajes sean dinámicos. Por ejemplo:  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

Las expresiones anteriores devolverán los mensajes de error pertinentes a partir de una operación incorrecta, que se pueden enviar como notificación en un canal de Teams. Consulte el artículo [Propiedades de salida de la actividad de copia](copy-activity-monitoring.md) para obtener más detalles.

También le animamos a que revise el [ esquema de carga útil de notificaciones](https://adaptivecards.io/explorer/AdaptiveCard.html) de Microsoft Teams y personalice aún más la plantilla anterior según sus necesidades.
