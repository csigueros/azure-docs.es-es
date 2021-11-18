---
title: Configuración de la directiva de apagado automático para laboratorios y máquinas virtuales
description: Descubra cómo establecer programaciones y directivas de apagado automático para Azure DevTest Labs o para máquinas virtuales individuales (VM) con el fin de apagar las máquinas virtuales a una hora concreta del día.
ms.topic: how-to
ms.date: 11/01/2021
ms.openlocfilehash: 028e52ccd4068524bcd9b4c8c5adb707d4400d70
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722285"
---
# <a name="configure-auto-shutdown-for-labs-and-vms-in-devtest-labs"></a>Configuración del apagado automático de laboratorios y máquinas virtuales en DevTest Labs

Como propietario de un laboratorio de Azure DevTest Labs, puede configurar una programación para que se apaguen todas las máquinas virtuales (VM) del laboratorio a una hora específica del día o de la noche. De esta forma, ahorra en los costos de ejecutar máquinas que no se usan.

También puede establecer una directiva central de apagado automático para controlar si los usuarios del laboratorio pueden programar el apagado automático de sus propias máquinas virtuales individuales. Las directivas de apagado automático van desde permitir a los propietarios de máquinas virtuales controlar completamente las programaciones de apagado de la máquina virtual hasta no permitirles ningún control sobre las programaciones.

En este artículo se explica cómo establecer programaciones de apagado automático para laboratorios de DevTest Labs y para máquinas virtuales de laboratorio individuales. También se describe cómo establecer la directiva de apagado automático del laboratorio y cómo configurar las notificaciones de apagado automático.

## <a name="configure-lab-auto-shutdown-schedule"></a>Configuración de la programación de apagado automático del laboratorio

El apagado automático ayuda a minimizar el desperdicio del laboratorio, ya que todas las máquinas virtuales de un laboratorio se apagan a una hora específica del día o de la noche. Para ver o cambiar la programación de apagado automático de un laboratorio, siga estos pasos:

1. En la página principal del laboratorio, seleccione **Configuration and policies** (Configuración y directivas).
1. En la sección **Programaciones** del menú izquierdo, seleccione **Apagado automático**.
1. En la pantalla **Apagado automático**, en **Habilitado**, seleccione **Activado** para habilitar esta directiva o **Desactivado** para deshabilitarla.
1. En **Apagado programado** y **Zona horaria**, si ha activado el apagado automático, especifique la hora y la zona horaria para apagar todas las máquinas virtuales del laboratorio.
1. En la opción **¿Enviar notificación antes del apagado automático?** , seleccione **Sí** o **No** para enviar una notificación 30 minutos antes de la hora de apagado automático especificada. Si elige **Sí**, escriba un punto de conexión de URL de webhook en **Dirección URL de webhook**, o escriba direcciones de correo electrónico separadas por coma en **Dirección de correo electrónico** donde quiera que se publique o envíe la notificación. Para más información, consulte la sección [Notificaciones de apagado automático](#auto-shutdown-notifications).
1. Seleccione **Guardar**.

   ![Captura de pantalla que muestra la configuración de los detalles de apagado automático de un laboratorio.](media/devtest-lab-auto-shutdown/auto-shutdown.png)

De forma predeterminada, esta programación se aplica a todas las máquinas virtuales del laboratorio. Para quitar esta configuración de una máquina virtual específica, si lo permite la directiva, abra el panel de administración de la máquina virtual y cambie la configuración de **Apagado automático**.

> [!NOTE]
> Si actualiza la programación de apagado automático de su laboratorio o de una máquina virtual antes que transcurran 30 minutos desde la hora de apagado programada anteriormente, la nueva hora de apagado entra en vigor al día siguiente.

## <a name="configure-lab-auto-shutdown-policy"></a>Configuración de la directiva de apagado automático de un laboratorio

Como propietario de un laboratorio, puede controlar su costo y reducir sus residuos mediante la administración de la configuración de la directiva de apagado automático del laboratorio. Para saber cómo configurar todas las directivas de laboratorio, consulte [Definición de las directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md).

> [!IMPORTANT]
> Los cambios en la directiva de apagado automático se aplican solo a las nuevas máquinas virtuales creadas en el laboratorio y no a las máquinas virtuales ya existentes.

1. En la página principal del laboratorio, seleccione **Configuration and policies** (Configuración y directivas).

1. En la sección **Programaciones** del menú izquierdo, seleccione **Directivas de apagado automático**.

1. Seleccione una de las opciones.

   ![Captura de pantalla que muestra las opciones de directiva de apagado automático.](./media/devtest-lab-auto-shutdown/policy-options.png)

   - **El usuario establece una programación y puede desactivarla**: los usuarios del laboratorio pueden invalidar o rechazar la programación del laboratorio. Esta opción concede a los propietarios de máquinas virtuales control total para establecer las programaciones de apagado automático de sus máquinas virtuales.

   - **El usuario establece una programación y no puede desactivarla**: los usuarios del laboratorio pueden invalidar la programación del laboratorio, pero no pueden rechazar la directiva de apagado automático. Esta opción garantiza que todas las máquinas virtuales del laboratorio están bajo una programación de apagado automático. Los propietarios de máquinas virtuales pueden actualizar la hora de programación y configurar las notificaciones de apagado.

   - **El usuario no tiene control sobre la programación establecida por el administrador del laboratorio**: los usuarios del laboratorio no pueden modificar ni rechazar la programación de apagado automático del laboratorio. Esta opción proporciona al administrador del laboratorio el control completo de la programación de todas las máquinas virtuales del laboratorio. Los propietarios del laboratorio solo pueden configurar notificaciones de apagado automático para sus máquinas virtuales.

1. Seleccione **Guardar**.

## <a name="configure-vm-auto-shutdown-settings"></a>Configuración de las opciones de apagado automático

En función de la directiva de apagado automático, también puede establecer una programación de apagado automático para cada máquina virtual del laboratorio.

1. En la página principal de la máquina virtual, en la sección **Operaciones** del menú izquierdo, seleccione **Apagado automático**.
1. En la pantalla **Apagado automático**, en **Habilitado**, seleccione **Activado** para habilitar esta directiva o **Desactivado** para deshabilitarla.
1. En **Apagado programado** y **Zona horaria**, si ha activado el apagado automático, especifique la hora y la zona horaria para apagar todas las máquinas virtuales del laboratorio.
1. En la opción **¿Enviar notificación antes del apagado automático?** , seleccione **Sí** o **No** para enviar una notificación 30 minutos antes de la hora de apagado automático especificada. Si elige **Sí**, escriba un punto de conexión de URL de webhook en **Dirección URL de webhook**, o escriba una dirección de correo electrónico en **Dirección de correo electrónico** donde quiera que se publique o envíe la notificación. Para más información, consulte la sección [Notificaciones de apagado automático](#auto-shutdown-notifications).
1. Seleccione **Guardar**.

   ![Captura de pantalla que muestra la configuración de los detalles de apagado automático de una máquina virtual.](media/devtest-lab-auto-shutdown/compute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Visualización de registros de actividad para las actualizaciones del apagado automático

Al actualizar la configuración del apagado automático, verá la actividad anotada en el registro de actividad de la máquina virtual.

1. En la página principal de la máquina virtual, seleccione **Registro de actividad** en el menú izquierdo.
1. Quite el filtro **Recurso**, aplique el filtro **Grupo de recursos** adecuado y vea las entradas de **Add or modify schedules** (Agregar o modificar programaciones).

   ![Captura de pantalla que muestra Add or modify schedules (Agregar o modificar programaciones) en el registro de actividad.](media/devtest-lab-auto-shutdown/activity-log-entry.png)

1. Seleccione la operación **Add or modify schedules** (Agregar o modificar programaciones) para abrir una página de resumen que muestre más detalles sobre la operación.

## <a name="auto-shutdown-notifications"></a>Notificaciones de apagado automático

Al habilitar las notificaciones en la configuración de apagado automático, los usuarios del laboratorio reciben una notificación 30 minutos antes del apagado automático por si alguna de sus máquinas virtuales va a resultar afectada. La notificación proporciona a los usuarios la oportunidad de guardar su trabajo antes del apagado. Si la configuración de apagado automático especifica una dirección de correo electrónico, la notificación se envía a esa dirección de correo electrónico. Si la configuración especifica un webhook, la notificación se envía a la dirección URL del webhook.

La notificación también puede proporcionar vínculos que permiten las siguientes acciones en cada máquina virtual si alguien necesita seguir trabajando:

- Omitir el apagado automático esta vez.
- Posponer el apagado durante una hora.
- Posponer el apagado durante dos horas.

Puede usar webhooks para implementar sus propias notificaciones. Se establecen integraciones que se suscriben a determinados eventos. Cuando se produce uno de esos eventos, se envía una carga HTTP POST a la dirección URL del webhook.

Aplicaciones como [Azure Logic Apps](../logic-apps/logic-apps-overview.md) y Slack tienen una amplia compatibilidad con webhooks. Para más información sobre cómo responder a webhooks, consulte [Introducción a los enlaces y desencadenadores HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook.md) y [Agregar un desencadenador HTTP para Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

En el ejemplo siguiente se muestra cómo usar Logic Apps para configurar una notificación de apagado automático que envía un correo electrónico a los propietarios de máquinas virtuales.

### <a name="create-a-logic-app-that-sends-email-notifications"></a>Creación de una aplicación lógica que envía notificaciones por correo electrónico

Logic Apps ofrece muchos conectores que facilitan la integración de un servicio con otros clientes, como Office 365 y Twitter. A nivel general, los pasos para configurar una aplicación lógica para la notificación por correo electrónico son los siguientes:

1. Cree una aplicación lógica.
1. Configure la plantilla integrada.
1. Realice la integración con su cliente de correo electrónico.
1. Obtenga la dirección URL del webhook que se usará en la configuración de la notificación de apagado automático.

Para empezar, cree una aplicación lógica en Azure con los pasos siguientes:

1. En Azure Portal, escriba *logic apps* en el campo de búsqueda superior y, luego, seleccione **Logic Apps**.

1. En la página **Logic Apps**, seleccione en **Agregar**.

1. En la página **Crear aplicación lógica**:

   - Seleccione su **suscripción** de Azure.
   - Seleccione un **Grupo de recursos** o cree uno nuevo.
   - Escriba un valor en **Nombre de la aplicación lógica**.
   - Seleccione una opción de **Región** para la aplicación lógica.

   ![Captura de pantalla que muestra la página Crear aplicación lógica.](media/devtest-lab-auto-shutdown/new-logic-app-page.png)

1. Seleccione **Revisar y crear** y, una vez superada la validación, seleccione **Crear**.

1. Cuando finalice la implementación, seleccione **Go to resource** (Ir al recurso).

A continuación, configure la plantilla integrada.

1. En la página Aplicación lógica, seleccione **Diseñador de aplicación lógica** en **Herramientas de desarrollo** del panel de navegación izquierdo.

1. En el menú superior, seleccione **Plantillas**.

1. En **Plantillas**, seleccione **HTTP Request/Response** (Solicitud o respuesta HTTP).

   ![Captura de pantalla que muestra la plantilla HTTP Request Response (Solicitud o respuesta HTTP).](media/devtest-lab-auto-shutdown/select-http-request-response-option.png)

1. En la página **HTTP Request-Response** (Solicitud-respuesta HTTP), seleccione **Use this template** (Usar esta plantilla).

   ![Captura de pantalla que muestra la selección de Usar esta plantilla.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)

1. Pegue el siguiente código JSON en la sección **Esquema JSON de cuerpo de solicitud**.

   ![Captura de pantalla que muestra Esquema JSON de cuerpo de solicitud.](media/devtest-lab-auto-shutdown/request-json.png)

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

Ahora, realice la integración con el cliente de correo electrónico.

1. En el diseñador, seleccione **Nuevo paso**.

   ![Captura de pantalla que muestra Nuevo paso en el diseñador.](media/devtest-lab-auto-shutdown/new-step.png)

1. En la página **Elegir una operación**, escriba *Office 365 Outlook: enviar un correo electrónico* en el campo de búsqueda y, luego, en **Acciones**, seleccione **Enviar correo electrónico (V2)** .

   ![Captura de pantalla que muestra la opción Enviar correo electrónico (V2).](media/devtest-lab-auto-shutdown/select-send-email.png)

1. En el formulario **Enviar correo electrónico (V2)** , rellene los campos **Para**, **Asunto** y **Cuerpo**.

   Seleccione **Agregar contenido dinámico** para rellenar automáticamente la notificación con los valores que usan la aplicación y los conectores. Por ejemplo, en **Para**, seleccione **propietario**. Rellene el campo **Asunto** con **vmName** y **labName**. Agregue contenido como los valores **skipUrl** y **delayUrl** al cuerpo del mensaje.

   ![Captura de pantalla que muestra un correo electrónico de notificación de ejemplo.](media/devtest-lab-auto-shutdown/email-options.png)

1. Seleccione **Guardar** en la barra de herramientas.

Ahora puede copiar la dirección URL del webhook. Seleccione el paso **Cuando se recibe una solicitud HTTP** y, luego, elija el botón de copia para copiar la dirección URL de HTTP POST en el portapapeles. Pegue esta dirección URL del webhook en la configuración de notificación de apagado automático.

![Captura de pantalla que muestra la copia de la dirección URL del webhook.](media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Pasos siguientes

- [Inicio automático de máquinas virtuales del laboratorio](devtest-lab-auto-startup-vm.md)
- [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md)
- [Recepción y respuesta de solicitudes HTTPS entrantes en Azure Logic Apps](../connectors/connectors-native-reqres.md)