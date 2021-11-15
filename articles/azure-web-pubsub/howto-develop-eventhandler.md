---
title: Configuración del controlador de eventos
description: Guía sobre los conceptos del controlador de eventos y la introducción a la integración al realizar el desarrollo con el servicio Azure Web PubSub.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/31/2021
ms.openlocfilehash: 45ad4d9d6d441584a512f5666245aed4020778cb
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576403"
---
# <a name="event-handler-in-azure-web-pubsub-service"></a>Controlador de eventos en el servicio Azure Web PubSub

El controlador de eventos controla los eventos de cliente entrantes. Los controladores de eventos se registran y configuran en el servicio a través del portal o la CLI de Azure de antemano para que, cuando se desencadene un evento de cliente, el servicio pueda identificar si se espera que el evento se controle o no. Ahora se admite el controlador de eventos como el lado servidor, que expone el punto de conexión accesible público para que el servicio invoque cuando se desencadene el evento. En otras palabras, actúa como un **webhook**. 

El servicio entrega los eventos de cliente al webhook ascendente mediante el [protocolo HTTP CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md).

Para cada evento, formula una solicitud HTTP POST al servidor ascendente registrado y espera una respuesta HTTP. 

Los datos que se envían del servicio al servidor siempre están en formato `binary` de CloudEvents.

:::image type="content" source="media/howto-develop-eventhandler/event-trigger.png" alt-text="Captura de pantalla del desencadenador de eventos del servicio Web PubSub.":::

## <a name="upstream-and-validation"></a>Servidor ascendente y validación

Al configurar el punto de conexión de webhook, la dirección URL puede usar el parámetro `{event}` para definir una plantilla de dirección URL. El servicio calcula el valor de la dirección URL del webhook dinámicamente cuando entra la solicitud del cliente. Por ejemplo, cuando llega una solicitud `/client/hubs/chat`, con un patrón `http://host.com/api/{event}` de dirección URL de controlador de eventos configurado para el centro `chat`, cuando el cliente se conecta, primero se aplica POST a esta dirección URL: `http://host.com/api/connect`. Esto puede ser útil cuando un cliente de WebSocket de PubSub envía eventos personalizados, que el controlador de eventos ayuda a enviar eventos diferentes a diferentes niveles ascendentes. Tenga en cuenta que el parámetro `{event}` no está permitido en el nombre de dominio de la dirección URL.

Al configurar el controlador de eventos ascendente a través de Azure Portal o la CLI, el servicio sigue la [protección contra abusos de CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) para validar el webhook ascendente. Este mecanismo validará todas las direcciones URL de webhook ascendentes registradas. El encabezado de la solicitud `WebHook-Request-Origin` se establece en el nombre de dominio del servicio `xxx.webpubsub.azure.com`, y espera que la respuesta que tiene el encabezado `WebHook-Allowed-Origin` contenga este nombre de dominio o `*`.

Al realizar la validación, el parámetro `{event}` se resuelve en `validate`. Por ejemplo, al intentar establecer la dirección URL en `http://host.com/api/{event}`, el servicio intenta aplicar **OPTIONS** a una solicitud a `http://host.com/api/validate`, y la configuración se puede establecer correctamente solo cuando la respuesta es válida.

Por ahora, no se admiten [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) ni [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback).

## <a name="authentication-between-service-and-webhook"></a>Autenticación entre el servicio y el webhook

- Modo anónimo
- La autenticación simple con `?code=<code>` se proporciona a través de la dirección URL del webhook configurada como un parámetro de consulta.
- Autenticación de AAD. 
   - Agregue un secreto de cliente en [Registros de aplicaciones] de AAD y proporcione el [secreto de cliente] a Azure Web PubSub a través del portal o la CLI.
   - Proporcione la [Identidad](../app-service/overview-managed-identity.md?tabs=dotnet) a Azure Web PubSub mediante el portal o la CLI.

## <a name="configure-event-handler"></a>Configuración del controlador de eventos

### <a name="configure-through-azure-portal"></a>Configuración mediante Azure Portal

Busque el servicio Azure Web PubSub en **Azure Portal**. Vaya a **Configuración** y escriba el nombre del centro de conectividad. A continuación, haga clic en **Agregar** para configurar la dirección URL del webhook del lado servidor. Recuerde hacer clic en **Guardar** al finalizar.

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="Captura de pantalla de la configuración del controlador de eventos.":::

### <a name="configure-through-azure-cli"></a>Configuración mediante la CLI de Azure

Use los comandos del grupo de centro de conectividad [**az webpubsub**](/cli/azure/webpubsub/hub) de la CLI de Azure para configurar los ajustes del controlador de eventos.

Comandos: | Descripción
--|--
create | Cree la configuración del centro de conectividad para el servicio WebPubSub.
delete | Permite eliminar la configuración del centro de conectividad para el servicio WebPubSub.
list   | Permite enumerar la configuración del centro de conectividad para el servicio WebPubSub.
mostrar   | Permite mostrar la configuración del centro de conectividad para el servicio WebPubSub.
update | Permite actualizar la configuración del centro de conectividad para el servicio WebPubSub.

A continuación, se muestra un ejemplo de creación de dos direcciones URL de webhook para el centro de conectividad de `MyHub` para `MyWebPubSub` recursos.

```azurecli-interactive
az webpubsub hub create -n "MyWebPubSub" -g "MyResourceGroup" --hub-name "MyHub" --event-handler url-template="http://host.com" user-event-pattern="*" --event-handler url-template="http://host2.com" system-event="connected" system-event="disconnected" auth-type="ManagedIdentity" auth-resource="uri://myUri"
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]