---
title: Envío de eventos de Auth0 a Azure mediante Azure Event Grid
description: Envíe eventos de Auth0 a los servicios de Azure con Azure Event Grid.
ms.topic: conceptual
ms.date: 07/22/2021
ms.openlocfilehash: 3fe7c69822f82ffbf8e76c0e3c4baaea236738e5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466788"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integración de Azure Event Grid con Auth0

En este artículo se describe cómo conectar sus cuentas de Auth0 y Azure al crear un tema de asociado de Event Grid.

Consulte los [códigos de tipo de evento de Auth0](https://auth0.com/docs/logs/references/log-event-type-codes) para obtener una lista completa de los eventos compatibles con Auth0.

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Envío de eventos de Auth0 a Azure Event Grid
Para enviar eventos de Auth0 a Azure, haga lo siguiente:

1. Habilitación del proveedor de recursos de Event Grid
1. Configuración de un tema de asociado de Auth0 en el panel de Auth0
1. Activación del tema de asociado en Azure
1. Suscripción a eventos de Auth0

Para más información acerca de estos conceptos, consulte los [conceptos](concepts.md) de Event Grid.

### <a name="enable-event-grid-resource-provider"></a>Habilitación del proveedor de recursos de Event Grid
A menos que haya usado Event Grid antes, deberá registrar el proveedor de recursos de Event Grid. Si ha usado Event Grid anteriormente, vaya a la siguiente sección.

En Azure Portal:
1. Seleccione Suscripciones en el menú de la izquierda.
1. Seleccione la suscripción que está usando para Event Grid.
1. En el menú de la izquierda, en Configuración, seleccione Proveedores de recursos.
1. Busque Microsoft.EventGrid.
1. Seleccione Registrar.
1. Actualice la vista para asegurarse de que el estado se cambie a Registrado.

### <a name="set-up-an-auth0-partner-topic"></a>Configuración de un tema de asociado de Auth0
Una parte del proceso de integración consiste en configurar Auth0 para usarse como un origen de eventos (este paso se realiza en el [panel de Auth0](https://manage.auth0.com/)).

1. Inicie sesión en el [panel de Auth0](https://manage.auth0.com/).
1. Vaya a Logs > Streams (Registros > Flujos).
1. Haga clic en + Create Stream (Crear flujo).
1. Seleccione Azure Event Grid y escriba un nombre único para el nuevo flujo.
1. Proporcione el id. de suscripción a Azure, la región de Azure y un nombre de grupo de recursos para crear el origen del evento. 
1. Haga clic en Guardar.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Activación del tema de asociado de Auth0 en Azure
La activación del tema de Auth0 en Azure permite que los eventos fluyan de Auth0 a Azure.

1. Inicie sesión en el Portal de Azure.
1. Busque `Partner Topics` en la parte superior y haga clic en `Event Grid Partner Topics` debajo de Servicios.
1. Haga clic en el tema que coincida con el flujo que creó en el panel de Auth0.
1. Confirme que el campo `Source` coincide con su cuenta de Auth0.
1. Haga clic en Activate (Activar).

### <a name="subscribe-to-auth0-events"></a>Suscripción a eventos de Auth0

#### <a name="create-an-event-handler"></a>Creación de un controlador de eventos
Necesitará un controlador de eventos para probar un tema de asociado. Diríjase a su suscripción a Azure y arranque un servicio que se admita como [controlador de eventos](event-handlers.md), como una instancia de [Azure Functions](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Suscripción al tema de asociado de Auth0
Suscribirse al tema de asociado de Auth0 le permite informarle a Event Grid sobre la ubicación a la que quiere enviar los eventos de Auth0.

1. En la hoja del tema del asociado de la integración de Auth0, seleccione + Suscripción de eventos en la parte superior.
1. En la página Crear suscripción de eventos, haga lo siguiente:
    1. Escriba un nombre para la suscripción a eventos.
    1. Seleccione el servicio o webhook de Azure que creó para el tipo de punto de conexión.
    1. Siga las instrucciones del servicio en cuestión.
    1. Haga clic en Crear.

## <a name="testing"></a>Prueba
La integración del tema de asociado de Auth0 con Azure debería estar lista para usarse.

### <a name="verify-the-integration"></a>Comprobación de la integración
Para comprobar que la integración funciona según lo esperado:

1. Inicie sesión en el panel de Auth0.
1. Vaya a Logs > Streams (Registros > Flujos).
1. Haga clic en el flujo de Event Grid.
1. Una vez dentro del flujo, haga clic en la pestaña de estado. El flujo debe estar activo y, siempre y cuando no vea ningún error, funciona.

Intente [invocar cualquiera de las acciones de Auth0 que desencadenan un evento que se publicará](https://auth0.com/docs/logs/references/log-event-type-codes) para ver el flujo de eventos.

## <a name="delivery-attempts-and-retries"></a>Intentos de entrega y reintentos
Los eventos de Auth0 se entregan a Azure a través de un mecanismo de transmisión. Cada evento se envía a medida que se desencadena en Auth0. Si Event Grid no puede recibir el evento, Auth0 intentará realizar el envío del evento hasta tres veces. De lo contrario, Auth0 registrará el error de entrega en su sistema.

## <a name="next-steps"></a>Pasos siguientes

- [Tema de asociado de Auth0](auth0-overview.md)
- [Introducción a los temas de asociados](partner-events-overview.md)
- [Convertirse en asociado de Event Grid](partner-onboarding-overview.md)