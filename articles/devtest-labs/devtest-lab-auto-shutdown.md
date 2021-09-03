---
title: Administración de directivas de apagado automático en máquinas virtuales de laboratorio de Azure DevTest Labs y de proceso de Azure Compute | Microsoft Docs
description: Aprenda a establecer una directiva de apagado automático para un laboratorio, de modo que las máquinas virtuales se apaguen automáticamente cuando no se estén usando.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 946ee2a4d6f3503671664b0e46806fef7f71a459
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113302003"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configuración del apagado automático de máquinas virtuales de laboratorio y proceso en Azure DevTest Labs

En este artículo se explica cómo configurar las opciones de apagado automático de las máquinas virtuales de laboratorio de DevTest Labs y de las máquinas virtuales de proceso de Compute.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configuración del apagado automático de las máquinas virtuales de laboratorio (DevTest Labs)

Azure DevTest Labs permite controlar los costos y desperdiciar lo mínimo posible en sus laboratorios gracias a la posibilidad de administrar políticas (configuración) en cada uno de ellos. En este artículo se muestra cómo configurar la directiva de apagado automático para un laboratorio.  También se muestra cómo configurar las opciones de apagado automático de una máquina virtual de laboratorio. Para ver cómo establecer cada una de las directivas de laboratorio, vea [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Establecimiento de la directiva de apagado automático para un laboratorio

Como propietario del laboratorio, puede configurar una programación de apagado para todas las máquinas virtuales de su laboratorio. Al hacerlo, puede ahorrar costos derivados de la ejecución de máquinas que no se usan (inactivas). Puede aplicar una directiva de apagado en todas las máquinas del laboratorio a nivel central y también ahorrarles a los usuarios del laboratorio el trabajo de configurar una programación para sus máquinas individuales. Esta característica permite establecer la directiva en la programación del laboratorio, con una configuración que cubre desde permitir que los usuarios del laboratorio tengan control total sobre la programación de apagado de su máquina virtual hasta no tener ningún control sobre el apagado. Como propietario del laboratorio, puede configurar esta directiva mediante los pasos siguientes:

1. En la página principal del laboratorio, seleccione **Configuration and policies** (Configuración y directivas).
2. Seleccione **Auto shutdown policy** (Directiva de apagado automático) en la sección **Schedules** (Programaciones) del menú izquierdo.
3. Seleccione una de las opciones. En las siguientes secciones, se proporcionan más detalles de estas opciones:

    ![Opciones de la directiva de apagado automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> Los cambios en la directiva de apagado automático se aplican solo a las nuevas máquinas virtuales creadas en el laboratorio y no a las máquinas virtuales ya existentes.

### <a name="configure-autoshutdown-settings"></a>Configuración del apagado automático

La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de sus máquinas virtuales.

Para ver o cambiar las directivas de un laboratorio, siga estos pasos:

1. En la página principal del laboratorio, seleccione **Configuration and policies** (Configuración y directivas).
2. Seleccione **Auto shutdown** (Apagado automático) en la sección **Schedules** (Programaciones) del menú izquierdo.
3. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.
     ![Detalles de apagado automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Si habilita esta directiva, especifique la hora local (y la zona horaria) para apagar todas las máquinas virtuales del laboratorio actual.
5. Especifique **Yes** (Sí) o **No** en la opción para enviar una notificación 30 minutos antes de la hora especificada para el apagado automático. Si elige **Sí**, escriba un punto de conexión de URL del webhook o una dirección de correo electrónico para especificar dónde quiere que se publique o se envíe la notificación. El usuario recibe la notificación y se le ofrece la opción de retrasar el apagado. Para obtener más información, consulte la sección [Notificaciones](#notifications).
6. Seleccione **Guardar**.

    De manera predeterminada, una vez que se habilite, esta directiva se aplica a todas las máquinas virtuales del laboratorio actual. Para quitar esta configuración de una máquina virtual específica, abra el panel de administración de la máquina virtual y cambie la configuración de **Apagado automático**.

> [!NOTE]
> Si actualiza la programación de apagado automático del laboratorio o de una máquina virtual de laboratorio específica en un plazo de 30 minutos a partir de la hora programada, se aplicará la hora de apagado actualizada en la programación del día siguiente.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>El usuario establece una programación y puede excluirse de ella

Si establece esta directiva para el laboratorio, los usuarios del laboratorio pueden excluirse de la programación del laboratorio o invalidarla. Esta opción concede a los usuarios del laboratorio control completo sobre la programación del apagado automático de sus máquinas virtuales. Los usuarios del laboratorio no verán ningún cambio en su página de programación de apagado de la máquina virtual.

![Opciones de la directiva de apagado automático: 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>El usuario establece una programación y no puede excluirse de ella

Si establece esta directiva para el laboratorio, los usuarios del laboratorio pueden invalidar la programación del laboratorio. Sin embargo, no se pueden excluir de la directiva de apagado automático. Esta opción garantiza que todas las máquinas del laboratorio están bajo una programación de apagado automático. Los usuarios del laboratorio pueden actualizar la programación de apagado automático de sus máquinas virtuales y configurar notificaciones de apagado.

![Opciones de la directiva de apagado automático: 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>El usuario no tiene control sobre la programación establecida por el administrador del laboratorio

Si establece esta directiva para el laboratorio, los usuarios del laboratorio no pueden excluirse de la programación del laboratorio ni invalidarla. Esta opción ofrece al administrador del laboratorio control total sobre la programación de cada máquina del laboratorio. Los usuarios del laboratorio solo pueden configurar notificaciones de apagado automático para sus máquinas virtuales.

![Opciones de la directiva de apagado automático: 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configuración del apagado automático de máquinas virtuales de proceso

1. En la página **Máquina virtual**, seleccione **Apagado automático** en el menú de la izquierda de la sección **Operaciones**.
2. En la página **Apagado automático**, seleccione **Activado** para habilitar esta directiva y **Desactivado** para deshabilitarla.
3. Si habilita esta directiva, especifique la **hora** (y la **zona horaria**) a la que se deben apagar las máquinas virtuales.
4. Elija **Yes** (Sí) o **No** en la opción para enviar una notificación 30 minutos antes de la hora especificada para el apagado automático. Si elige **Sí**, escriba un punto de conexión de URL del webhook o una dirección de correo electrónico para especificar dónde quiere que se publique o se envíe la notificación. El usuario recibe la notificación y se le ofrece la opción de retrasar el apagado. Para obtener más información, consulte la sección [Notificaciones](#notifications).
5. Seleccione **Guardar**.

    ![Configuración del apagado automático de una máquina virtual de proceso](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Visualización de registros de actividad para las actualizaciones del apagado automático

Al actualizar la configuración del apagado automático, verá la actividad que figura en el registro de actividad de la máquina virtual.

1. En [Azure Portal](https://portal.azure.com), vaya a la página principal de la máquina virtual.
2. Seleccione **Registro de actividad** en el menú de la izquierda.
3. Quite **Recurso: mycomputevm** de los filtros.
4. Confirme que ve la operación **Agregar o modificar programaciones** en el registro de actividad. Si no lo ve, espere unos minutos y actualice el registro de actividad.

    ![Entrada de registro de actividad](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Seleccione la operación **Agregar o modificar programaciones** para ver la información siguiente en la página **Resumen**:

    - Nombre de la operación (Agregar o modificar programaciones)
    - Fecha y hora en que se actualizó la configuración del apagado automático.
    - La dirección de correo electrónico del usuario que actualizó la configuración.

        ![Resumen de entradas del registro de actividad](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. Cambie a la pestaña **Historial de cambios** de la página **Agregar o modificar programaciones** y verá el historial de cambios de la configuración. En el ejemplo siguiente, la hora de apagado se cambió de 7 p.m. a 6 p.m. el 10 de abril de 2020 a las 15:18:47 EST. Y la configuración se deshabilitó a las 15:25:09 EST.

    ![Registro de actividad - historial de cambios](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. Para ver más detalles sobre la operación, cambie a la pestaña **JSON** de la página **Agregar o modificar programaciones**.

## <a name="notifications"></a>Notificaciones

Una vez que se haya configurado el apagado automático, se enviarán notificaciones a los usuarios del laboratorio treinta minutos antes de que se active el apagado automático si alguna de sus máquinas virtuales se ve afectada. Esta opción proporciona a los usuarios del laboratorio la oportunidad de guardar su trabajo antes del apagado. La notificación también proporciona vínculos para cada máquina virtual con las siguientes acciones, en caso de que alguien deba seguir trabajando en su máquina virtual.

- Omitir el apagado automático de ese momento
- Posponer el apagado durante una hora
- Posponer el apagado durante dos horas

Si se especificó un webhook, la notificación se envía a su dirección URL.  Si se especificó una dirección de correo electrónico en la configuración de apagado, se envía un mensaje a esa dirección. Los webhooks le permiten crear o configurar integraciones que se suscriben a ciertos eventos. Cuando se activa uno de esos eventos, DevTest Labs enviará una carga HTTP POST a la dirección URL configurada del webhook. Para obtener más información sobre cómo responder a webhooks, consulte [Introducción a los enlaces y desencadenadores HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook.md) o cómo [agregar un desencadenador HTTP para Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

Le recomendamos que use webhooks porque son ampliamente compatibles con diversas aplicaciones, como Azure Logic Apps y Slack.  Además, le permiten implementar su propio método para enviar notificaciones. A modo de ejemplo, este artículo le indicará cómo configurar la notificación de apagado automáticamente para enviar un correo electrónico al propietario de la máquina virtual mediante Azure Logic Apps. Primero, veamos rápidamente los pasos básicos para habilitar la notificación de apagado automático en su laboratorio.

### <a name="create-a-logic-app-that-sends-email-notifications"></a>Creación de una aplicación lógica que envía notificaciones por correo electrónico

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ofrece muchos conectores que facilitan la integración de un servicio con otros clientes, como Office 365 y Twitter. En el nivel alto, los pasos para configurar una aplicación lógica para las notificaciones por correo electrónico se pueden dividir en cuatro fases:

- Cree una aplicación lógica.
- Configure la plantilla integrada.
- Intégrela con su cliente de correo electrónico.
- Copie la dirección URL del webhook.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica

Para comenzar, cree una aplicación lógica en su suscripción de Azure siguiendo estos pasos:

1. Seleccione **+ Create a resource** (+ Crear un recurso) en el menú de la izquierda, seleccione **Integration** (Integración) y seleccione **Logic App** (Aplicación lógica).

    ![Nuevo menú de aplicaciones lógicas](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. En la página **Aplicación lógica: crear**, siga estos pasos:
    1. Escriba un **nombre** para la aplicación lógica.
    2. Selección la **suscripción** de Azure.
    3. Cree un **grupo de recursos** nuevo o seleccione uno existente.
    4. Seleccione una **ubicación** para la aplicación lógica.

        ![Aplicación lógica nueva: configuración](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. En las **notificaciones**, seleccione **Go to resource** (Ir al recurso) en la notificación.

    ![Ir al recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Seleccione **Logic app designer** (Diseñador de aplicaciones lógicas) en la categoría **Deployment Tools** (Herramientas de implementación).

    ![Seleccionar solicitud o respuesta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. En la página **HTTP Request-Response** (Solicitud-respuesta HTTP), seleccione **Use this template** (Usar esta plantilla).

    ![Seleccione Usar esta opción de plantilla](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie el siguiente JSON en la sección **Request Body JSON Schema** (Esquema de JSON del cuerpo de la solicitud):

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![Captura de pantalla que muestra "Esquema JSON de cuerpo de solicitud".](./media/devtest-lab-auto-shutdown/request-json.png)
7. Seleccione **+ New step** (+ Nuevo paso) en el diseñador y siga estos pasos:
    1. Busque **Office 365 Outlook - Send an email** (Office 365 Outlook: enviar un correo electrónico).
    2. Seleccione **Send an email** (Enviar un correo electrónico) desde **Actions** (Acciones).

        ![Opción de enviar un correo electrónico](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Seleccione **Sign in** (Iniciar sesión) para iniciar sesión en su cuenta de correo electrónico.
    4. Seleccione el campo **TO** (PARA) y elija el propietario.
    5. Seleccione **SUBJECT** (ASUNTO) y escriba el asunto de la notificación por correo electrónico. Por ejemplo: "Apagado de la máquina vmName para el laboratorio: labName".
    6. Seleccione **BODY** (CUERPO) y defina el contenido del cuerpo para la notificación por correo electrónico. Por ejemplo: "vmName está programado para apagarse en 15 minutos. Para omitir este apagado, haga clic en: dirección URL. Retrasar el apagado durante una hora: delayUrl60. Retrasar el apagado durante dos hora: delayUrl120".

        ![Esquema JSON del cuerpo de la solicitud](./media/devtest-lab-auto-shutdown/email-options.png)
8. Seleccione **Guardar** en la barra de herramientas. Ya puede copiar la **dirección URL de HTTP POST**. Seleccione el botón para copiar la dirección URL en el Portapapeles.

    ![Dirección URL de Webhook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo configurar todas las directivas, consulte [Administración de las directivas de un laboratorio de Azure DevTest Labs](devtest-lab-set-lab-policy.md).
