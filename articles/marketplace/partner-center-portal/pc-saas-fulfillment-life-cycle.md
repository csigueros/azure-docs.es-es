---
title: Administración del ciclo de vida de la suscripción de SaaS | Marketplace comercial de Microsoft
description: Aprenda a administrar el ciclo de vida de la suscripción de SaaS mediante la versión 2 de las API de suministro.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: dfe47c220cb0dc427d9ff00bdfbaa85da949f520
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063910"
---
# <a name="managing-the-saas-subscription-life-cycle"></a>Administrar el ciclo de vida de la suscripción a SaaS

El marketplace comercial administra todo el ciclo de vida de una suscripción de SaaS después de que el cliente final la compra. Usa la página de aterrizaje, las API de suministro, las API de operaciones y el webhook como mecanismo para impulsar la activación, el uso, las actualizaciones y la cancelación de la suscripción de SaaS real. La factura que recibe el usuario final se basa en el estado de la suscripción de SaaS que mantiene Microsoft.

## <a name="states-of-a-saas-subscription"></a>Estados de una suscripción a SaaS

En el diagrama siguiente se muestran los estados de una suscripción de SaaS y las acciones aplicables.

[ ![Diagrama que muestra el ciclo de vida de una suscripción de software como servicio en Marketplace.](./media/saas-subscription-lifecycle-api-v2.png) ](./media/saas-subscription-lifecycle-api-v2.png#lightbox)

### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Comprada pero todavía no activada (*PendingFulfillmentStart*)

Después de que un usuario final o un proveedor de soluciones en la nube (CSP) compre una oferta de SaaS en el Marketplace comercial, el editor recibe una notificación de la compra. Después, el editor puede crear y configurar una nueva cuenta de SaaS en el lado del editor para el usuario final.

Para que tenga lugar la creación de una cuenta:

1. El cliente selecciona el botón **Configure account now** (Configurar cuenta ahora), que está disponible para una oferta de SaaS después de realizar la compra correctamente en Microsoft AppSource o Azure Portal. Como alternativa, el cliente puede usar el botón **Configurar ahora** del correo electrónico que recibirá poco después de la compra.
2. Después, Microsoft notifica al asociado la compra, para lo que abre la dirección URL de la página de aterrizaje con el parámetro token (el token de identificación de la compra del Marketplace comercial) en la nueva pestaña del explorador.

Un ejemplo de este tipo de llamada es `https://contoso.com/signup?token=<blob>`, pero la dirección URL de la página de aterrizaje de esta oferta de SaaS en el Centro de partners está configurada como `https://contoso.com/signup`. Este token proporciona al editor un identificador que distingue de forma única la compra de SaaS y el cliente.

[!INCLUDE [pound-sign-note](../includes/pound-sign-note.md)]

> [!IMPORTANT]
> La dirección URL de la página de aterrizaje debe estar en funcionamiento de forma ininterrumpida y poder recibir nuevas llamadas de Microsoft en todo momento. Si la página de aterrizaje deja de estar disponible, los clientes no podrán registrarse en el servicio de SaaS y empezar a usarlo.

A continuación, el editor debe devolver el *token* a Microsoft mediante una llamada a la [API Resolve de SaaS](pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription) y la introducción del token como valor del parámetro de encabezado `x-ms-marketplace-token header`. Como resultado de la llamada API Resolve, se intercambia el token para ver los detalles de la compra de SaaS, como el identificador único de la compra y el identificador de la oferta y del plan adquiridos.

En la página de aterrizaje, el cliente debe haber iniciado sesión en la cuenta de SaaS nueva o existente mediante el inicio de sesión único (SSO) de Azure Active Directory (Azure AD).

>[!NOTE]
>El editor no recibirá ninguna notificación de la compra de SaaS hasta que el cliente inicie el proceso de configuración desde el lado de Microsoft.

El editor debe implementar el inicio de sesión único para proporcionar la experiencia de usuario que Microsoft necesita para este flujo. Asegúrese de usar la aplicación de Azure AD multiinquilino y de permitir tanto cuentas profesionales y educativas como cuentas personales de Microsoft al configurar el SSO. Este requisito solo se aplica a la página de aterrizaje y a los usuarios a los que se redirige al servicio de SaaS cuando ya han iniciado sesión con las credenciales de Microsoft. El SSO no es necesario para todos los inicios de sesión en el servicio de SaaS.

> [!NOTE]
>Si el inicio de sesión único requiere que un administrador conceda permiso a una aplicación, la descripción de la oferta en el Centro de partners debe indicar que se requiere acceso de nivel de administrador. Esta información está pensada para cumplir con las [directivas de certificación del marketplace comercial](/legal/marketplace/certification-policies#10003-authentication-options).

Después de iniciar sesión, el cliente debe completar la configuración de SaaS en el lado del editor. Luego, el editor debe llamar a la [API Activate Subscription](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) para enviar una señal a Azure Marketplace que indique que el aprovisionamiento de la cuenta de SaaS se ha completado.
Esta acción dará comienzo al ciclo de facturación del cliente. Si la llamada a la API Activate Subscription no se realiza correctamente, no se facturará la compra al cliente.

![Diagrama que muestra las llamadas API para un escenario de aprovisionamiento.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

### <a name="active-subscribed"></a>Activo (*Suscrito*)

Activo (*Suscrito*) es el estado estable de una suscripción de SaaS aprovisionada. Una vez que se ha procesado la llamada a la [API Activate Subscription](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) en el lado de Microsoft, la suscripción de SaaS se marca como *Suscrita*. El cliente ya puede usar el servicio de SaaS en el lado del editor y se facturará.

Cuando una suscripción de SaaS ya está activa, el cliente puede seleccionar **Administrar experiencia de SaaS** en Azure Portal o el Centro de administración de Microsoft 365. Esta acción también provoca que Microsoft llame a la **dirección URL de la página de aterrizaje** con el parámetro *token*, como sucede en el flujo de Activate. El editor debe distinguir entre las nuevas compras y la administración de las cuentas de SaaS existentes y controlar en consecuencia la llamada a la dirección URL de la página de aterrizaje.

### <a name="being-updated-subscribed"></a>Actualizando (*Suscrito*)

Esta acción significa que tanto Microsoft como el editor están procesando una actualización de una suscripción de SaaS activa existente. Pueden iniciar esta actualización:

- El cliente desde el marketplace comercial.
- El CSP desde el marketplace comercial.
- El cliente desde el sitio de SaaS del editor (no se aplica a las compras realizadas por el CSP).

Hay disponibles dos tipos de actualizaciones para una suscripción de SaaS:

- Actualización del plan cuando el cliente elige otro plan para la suscripción.
- Actualización de la cantidad cuando el cliente cambia el número de puestos comprados para la suscripción.

Solo se puede actualizar una suscripción activa. Mientras se actualiza la suscripción, su estado permanece activo en el lado de Microsoft.

#### <a name="update-initiated-from-the-commercial-marketplace"></a>Actualización iniciada desde el marketplace comercial

En este flujo, el cliente cambia el plan de suscripción o la cantidad de puestos desde Azure Portal o el Centro de administración de Microsoft 365.

1. Tras introducir una actualización, Microsoft llama a la dirección URL del webhook del editor, configurada en el campo **Webhook de conexión** de la página _Technical configuration_ (Configuración técnica) del Centro de partners, con un valor adecuado para el parámetro *action* y otros pertinentes.
1. El lado del editor debe realizar los cambios necesarios en el servicio de SaaS y notificar a Microsoft cuando finalice mediante una llamada a la [API Update Status of Operation](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation).
1. Si se envía la revisión con un estado de *error*, el proceso de actualización no se completará en el lado de Microsoft. La suscripción de SaaS se quedará con la cantidad de puestos y el plan existentes.

> [!NOTE]
> El editor debe invocar PATCH para [actualizar la API Status of Operation](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) con una respuesta de error o de éxito *en un período de 10 segundos* después de recibir la notificación de webhook. Si no se recibe una respuesta PATCH del estado de la operación en el plazo de 10 segundos, el plan de cambios se *revisa automáticamente como correcto*.

En el diagrama siguiente se muestra la secuencia de llamadas API para un escenario de actualización que se inicia desde el marketplace comercial.

![Diagrama que muestra las llamadas API para una actualización iniciada desde el marketplace.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

#### <a name="update-initiated-from-the-publisher"></a>Actualización iniciada desde el editor

En este flujo, el cliente cambia el plan de suscripción o la cantidad de puestos comprados desde el propio servicio de SaaS. 

1. Antes de realizar el cambio solicitado en el lado del editor el código de este debe llamar a la [API Change Plan](pc-saas-fulfillment-subscription-api.md#change-the-plan-on-the-subscription), a la [API Change Quantity](pc-saas-fulfillment-subscription-api.md#change-the-quantity-of-seats-on-the-saas-subscription) o a ambas.

1. Microsoft aplicará el cambio a la suscripción y, luego, enviará una notificación al editor a través del campo **Webhook de conexión** para aplicar el mismo cambio.

1. Solo entonces el editor debe realizar el cambio necesario en la suscripción de SaaS y notificar a Microsoft cuando se efectúe el cambio mediante una llamada a la [API Update Status of Operation](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation).

En el diagrama siguiente se muestra la secuencia de llamadas API para un escenario de actualización que se inicia desde el lado del editor.

![Diagrama que muestra las llamadas API para una actualización iniciada desde el lado del editor.](./media/saas-update-status-api-v2-calls-publisher-side.png)

### <a name="suspended-suspended"></a>Suspendido (*Suspendido*)

Este estado indica que no se ha recibido el pago de un cliente porque el servicio de SaaS no se ha recibido. Microsoft notificará al editor este cambio en el estado de la suscripción de SaaS. La notificación se realiza a través de una llamada al webhook con el parámetro *action* establecido en *Suspendido*.

El editor podría realizar cambios en el servicio de SaaS en el lado del editor. Se recomienda que el editor ponga esta información a disposición del cliente suspendido y que limite o bloquee el acceso del cliente al servicio de SaaS. Existe la posibilidad de que el pago nunca se reciba.

> [!NOTE]
> Microsoft ofrece al cliente un período de gracia de 30 días antes de cancelar automáticamente la suscripción. Una vez transcurrido el período de gracia de 30 días, el webhook recibirá una acción `Unsubscribe`.

Cuando una suscripción se encuentra en el estado *Suspendido*:

* El partner o el ISV deben mantener la cuenta de SaaS en un estado recuperable, de modo que se pueda restaurar toda la funcionalidad sin que se pierdan los datos o la configuración.
* El partner o el ISV debería recibir una solicitud para restablecer la suscripción si se recibe el pago durante el período de gracia, o bien una solicitud de desaprovisionamiento de la suscripción al final del período de gracia. Ambas solicitudes se enviarán a través del mecanismo de webhook.

El estado de la suscripción se cambia a Suspendido en el lado de Microsoft antes de que el editor realice alguna acción. Solo se pueden suspender las suscripciones activas.

### <a name="reinstated-suspended"></a>Restablecido (*Suspendido*)

Esta acción indica que el instrumento de pago del cliente ha vuelto a ser válido, que se ha realizado un pago para la suscripción de SaaS y que se ha restablecido la suscripción. En este caso: 

1. Microsoft llama al webhook con un parámetro *action* establecido en el valor *Reinstate*.
1. El editor se asegura de que la suscripción esté totalmente operativa de nuevo en el lado del editor.
1. El editor llama a la [API Patch Operation](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) con el estado correcto.
1. El proceso de restablecimiento se realiza correctamente y se vuelve a facturar al cliente para la suscripción de SaaS. 

Si se envía la revisión con un estado de *error*, el proceso de restablecimiento no finalizará en el lado de Microsoft y la suscripción permanecerá *Suspendida*.

Solo se puede restablecer una suscripción suspendida. La suscripción de SaaS suspendida permanece en un estado *Suspendido* mientras se restablece. Una vez finalizada esta operación, el estado de la suscripción pasará a ser *Activo*.

### <a name="renewed-subscribed"></a>Renovado (*Suscrito*)

Al final del período de suscripción (después de un mes o de un año), Microsoft renueva automáticamente la suscripción de SaaS. El valor predeterminado para la opción de renovación automática es *true* para todas las suscripciones de SaaS. Las suscripciones de SaaS activas seguirán renovándose periódicamente. Microsoft proporciona notificaciones de webhook solo de informe para eventos de renovación. Un cliente puede desactivar la renovación automática de una suscripción de SaaS a través del portal de administración de Microsoft 365. En este caso, la suscripción de SaaS se cancelará automáticamente al final del período de facturación actual. Los clientes también pueden cancelar la suscripción de SaaS en cualquier momento.

Solo se renuevan automáticamente las suscripciones activas. Las suscripciones permanecen activas durante el proceso de renovación si la renovación automática se realiza correctamente. Después de la renovación, las fechas de inicio y finalización del período de suscripción se actualizan con las fechas del nuevo período.

Si se produce un error en una renovación automática debido a un problema con el pago, la suscripción pasará a estar *Suspendida* y se le notificará al editor.

### <a name="canceled-unsubscribed"></a>Cancelado (*Suscripción cancelada*)

Las suscripciones alcanzan este estado como respuesta a una acción explícita del cliente o del CSP para la cancelación de una suscripción desde el sitio del editor, Azure Portal o el Centro de administración de Microsoft 365. Una suscripción también puede cancelarse implícitamente, como resultado del impago de importes pendientes, después de mantenerse en el estado *Suspendido* durante 30 días.

Después de que el editor reciba una llamada del webhook de cancelación, debe conservar los datos del cliente para llevar a cabo la recuperación si se solicita durante al menos siete días. Solo entonces se pueden eliminar los datos del cliente.

Una suscripción de SaaS se puede cancelar en cualquier momento de su ciclo de vida. Una vez cancelada una suscripción, no se puede reactivar.

## <a name="next-steps"></a>Pasos siguientes

- [API de suscripción de suministro de SaaS v2](pc-saas-fulfillment-subscription-api.md)
- [API de operaciones de suministro de SaaS v2](pc-saas-fulfillment-operations-api.md)
