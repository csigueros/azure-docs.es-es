---
title: Solicitud de aumentos de cuota al servicio de asistencia
description: Cómo crear una solicitud de soporte técnico en Azure Portal para Azure Data Factory solicitar aumentos de cuota u obtener soporte técnico para la resolución de problemas.
ms.service: data-factory
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 03/10/2020
author: jonburchel
ms.author: jburchel
ms.openlocfilehash: a6cffaf7b1f49e747b2d160318673dd1a07b9885
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350088"
---
# <a name="request-quota-increases-and-get-support-for-azure-data-factory"></a>Solicitud de aumentos de cuota y obtención de soporte técnico para Azure Data Factory

En este artículo se describe cómo enviar una incidencia de soporte técnico en Azure Portal para Azure Data Factory. Este proceso le permite solicitar un aumento de la cuota o enviar una solicitud de soporte técnico al equipo de soporte técnico de ingeniería.

## <a name="create-a-support-ticket"></a>Creación de una incidencia de soporte técnico

Siga estos pasos para crear una nueva solicitud de soporte técnico desde Azure Portal para Azure Data Factory.

1. En el menú de [Azure Portal](https://portal.azure.com), seleccione **Ayuda y soporte técnico**.

   ![El vínculo de Ayuda y soporte técnico](./media/quota-increase/help-plus-support.png)


1. Seleccione **Ayuda y soporte técnico** y **Nueva solicitud de soporte técnico**.

    :::image type="content" source="./media/quota-increase/new-support-request.png" alt-text="Creación de una solicitud de soporte técnico":::

1. Revise el [Plan de soporte técnico de Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * El soporte técnico para **facturación, cuota y administración de suscripciones** está disponible en todos los niveles.
   * El soporte técnico para problemas del tipo **break-fix** se proporciona en los niveles [Desarrollador](https://azure.microsoft.com/support/plans/developer/), [Estándar](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) o [Premier](https://azure.microsoft.com/support/plans/premier/). Los problemas "break-fix" son aquellos que experimentan los clientes mientras usan Azure en los que hay una posibilidad razonable de que hayan sido causados por Microsoft.
   * El **aprendizaje para desarrolladores** y los **servicios de asesoramiento** están disponibles en los niveles de soporte técnico [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) y [Premier](https://azure.microsoft.com/support/plans/premier/).

   Si tiene un plan de soporte técnico Premier, también puede informar sobre problemas relacionados con Azure Data Factory en el [portal Microsoft Premier Online](https://premier.microsoft.com/). Consulte [Planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para obtener más información sobre los diversos planes de soporte técnico, incluido su ámbito, los tiempos de respuesta, el precio, etc.  Si quiere ver las preguntas más frecuentes sobre el soporte técnico de Azure, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

1. En **Tipo de problema**, seleccione el tipo de problema apropiado. En caso de problemas de break-fix, seleccione **Técnico**. Para las solicitudes de aumento de cuota, seleccione **Límites de servicio y suscripción (cuotas)** .

   :::image type="content" source="./media/quota-increase/select-quota-issue-type.png" alt-text="Selección de un tipo de problema":::  

   > [!NOTE]
   > El resto de este artículo se centra en las solicitudes de aumento de cuota. No obstante, también puede seleccionar **Técnico** aquí para solicitudes de soporte técnico para la resolución de problemas. Si selecciona **Técnico**, se le pedirá que proporcione un resumen y, a continuación, identifique un tipo de problema en **Seleccionar el tipo de problema**. Es posible que vea soluciones que le ayuden a resolver el problema. Si las soluciones presentadas no resuelven el problema, seleccione **Siguiente: Detalles** y rellene el formulario para enviar la incidencia de soporte técnico.

1. Para las solicitudes de aumento de cuota, seleccione **Data Factory** para el **Tipo de cuota**. Luego, seleccione **Siguiente**.

   :::image type="content" source="./media/quota-increase/select-quota-type.png" alt-text="Selección de un tipo de cuota":::

1. En la ventana **Detalles adicionales**, escriba información adicional sobre los límites de cuota específicos que necesita aumentar.  Consulte el artículo [Límites de Data Factory ](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) para obtener información sobre límites específicos.  Seleccione el método de contacto que prefiera y haga clic en **Siguiente**.

   :::image type="content" source="./media/quota-increase/provide-details.png" alt-text="La sección &quot;Proporcionar detalles&quot;":::

## <a name="submit-your-request"></a>Enviar la solicitud

Después de revisar los detalles de la solicitud en la página final, seleccione **Crear** para enviar la solicitud.

## <a name="monitor-a-support-ticket"></a>Supervisión de una incidencia de soporte técnico

Una vez que ha enviado la solicitud de asistencia, el equipo de soporte técnico de Azure se pondrá en contacto con usted. Para comprobar tanto el estado de la solicitud como los detalles de la misma, seleccione **Todas las solicitudes de soporte técnico** en el panel.

:::image type="content" source="./media/quota-increase/monitor-ticket.png" alt-text="Comprobar estado":::

## <a name="other-resources"></a>Otros recursos

También puede conectar con la comunidad de Azure Data Factory en [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory) o mediante la [página de preguntas y respuestas de Microsoft para Azure Data Factory](/answers/topics/azure-data-factory.html).