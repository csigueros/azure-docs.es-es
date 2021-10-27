---
title: Centro de transferencia de suscripciones y reservas de Azure
description: Este artículo le ayuda a comprender qué se necesita para transferir suscripciones de Azure y proporciona vínculos a otros artículos para obtener información más detallada.
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: dbe893dc471f055dd5b062e7ce5dcf492dd0f421
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007074"
---
# <a name="azure-subscription-and-reservation-transfer-hub"></a>Centro de transferencia de suscripciones y reservas de Azure

En este artículo se describen los tipos de transferencias admitidas para las suscripciones y las reservas de Azure, lo que se conoce como _productos_. También le ayuda a comprender qué se necesita para transferir productos de Azure entre distintos contratos de facturación y proporciona vínculos a otros artículos para obtener información más detallada sobre transferencias específicas. Los productos de Azure se crean a partir de diferentes tipos de contrato de Azure y una transferencia de un tipo de contrato de origen a otro varía en función de los tipos de contrato de origen y destino. Las transferencias de productos de Azure pueden ser un proceso automático o manual, según el tipo de contrato de origen y de destino. Si se trata de un proceso manual, los tipos de contrato determinan cuánto esfuerzo se necesita.

> [!NOTE]
> Hay muchos tipos de productos de Azure, pero no todos los productos se pueden transferir de un tipo a otro. En este artículo solo se documentan las transferencias de productos admitidas. Si necesita ayuda con una situación que no se aborda en este artículo, puede crear una [solicitud de soporte técnico de Azure](https://go.microsoft.com/fwlink/?linkid=2083458).

Este artículo también le ayudará a comprender qué debe saber _antes_ de transferir la propiedad de facturación de un producto de Azure a otra cuenta. Es posible que quiera transferir la propiedad de facturación del producto de Azure si deja la organización o si quiere que el producto se facture en otra cuenta. La transferencia de la propiedad de facturación a otra cuenta proporciona a los administradores el permiso de nueva cuenta para las tareas de facturación. Pueden cambiar el método de pago, ver los cargos y cancelar el producto.

Si quiere mantener la propiedad de facturación, pero cambiar el tipo de producto, consulte [Cambio de la suscripción de Azure a una oferta distinta](switch-azure-offer.md). Para controlar quién puede acceder a los recursos del producto, consulte [Roles integrados de Azure](../../role-based-access-control/built-in-roles.md).

Si es un cliente de Contrato Enterprise (EA), los administradores de la empresa pueden transferir la propiedad de facturación de los productos entre cuentas en EA Portal. Para más información, consulte [Cambio de la propiedad de la cuenta o de la suscripción de Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Este artículo se centra en las transferencias de productos. Sin embargo, también se analiza la transferencia de recursos porque es necesaria en algunos escenarios de transferencia de productos.

Para más información sobre las transferencias de productos entre diferentes inquilinos de Azure AD, consulte [Transferencia de una suscripción de Azure a otro directorio de Azure AD](../../role-based-access-control/transfer-subscription.md).

> [!NOTE]
> La mayoría de las transferencias de propiedad de facturación no cambian el inquilino del servicio de los productos subyacentes. No provocan ningún tiempo de inactividad. Sin embargo, incluso si cambia un inquilino de facturación, este cambio no afecta a los servicios o recursos en ejecución.

## <a name="product-transfer-planning"></a>Planeamiento de transferencia de productos

A medida que empiece a planear la transferencia de productos, tenga en cuenta la información necesaria para responder a las siguientes preguntas:

- ¿Por qué se requiere la transferencia de productos?
- ¿Cuál es la escala de tiempo que se quiere para la transferencia de productos?
- ¿Cuál es el tipo de oferta actual del producto y a qué desea transferirlo?
  - Programa de suscripción en línea de Microsoft (MOSP), también conocido como Pago por uso
  - Oferta de Azure anterior en CSP
  - Nueva oferta de Azure en CSP, también denominada Plan de Azure con Microsoft Partner Agreement (MPA)
  - Contrato Enterprise (EA)
  - Contrato de cliente de Microsoft (MCA) en la trayectoria Enterprise cuando se compran servicios de Azure por medio de un representante de Microsoft y un MCA individual de Microsoft cuando se compran servicios de Azure a través de Azure.com
  - Otros, como MSDN, BizSpark, EOPEN, Pase para Azure y evaluación gratuita
- ¿Tiene los permisos necesarios en el producto para realizar una transferencia? En la siguiente tabla de soporte técnico de transferencia de productos se muestra el permiso específico necesario para cada tipo de transferencia.
  - Solo el administrador de facturación de una cuenta puede transferir la propiedad de una suscripción.
  - Solo un propietario o administrador de facturación puede transferir la propiedad de la reserva.
- ¿Hay suscripciones existentes que se beneficien de las reservas y tendrán que transferirse las reservas con la suscripción?

Debe tener una respuesta para cada pregunta antes de continuar con cualquier transferencia.

Las respuestas a las preguntas anteriores pueden ayudarle a comunicarse anticipadamente con otros involucrados en el proceso para establecer las expectativas y escalas de tiempo. El esfuerzo de transferencia de productos varía considerablemente, pero es probable que tarde más de lo esperado.

Las respuestas a las preguntas sobre el tipo de oferta de origen y destino ayudan a definir las rutas técnicas que deberá seguir y a identificar las limitaciones que podría tener una determinada combinación de transferencia. En la siguiente sección se tratan las limitaciones con más detalle.

## <a name="product-transfer-support"></a>Compatibilidad de la transferencia de productos

En la tabla siguiente se describe la compatibilidad de la transferencia de productos entre los distintos tipos de contrato. Se proporcionan vínculos para obtener más información sobre cada tipo de transferencia.

Actualmente no se admite la transferencia de productos de [Evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) o [Azure bajo licencia Open](https://azure.microsoft.com/offers/ms-azr-0111p/). Para ver una solución alternativa, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Los planes de soporte técnico no se pueden transferir. Si tiene un plan de soporte técnico, debe cancelarlo en el contrato antiguo y comprar uno nuevo para el nuevo contrato.

Los productos de desarrollo/pruebas no se muestran en la tabla siguiente. Las transferencias de productos de desarrollo/pruebas se llevan a cabo de la misma manera que otros tipos de productos. Por ejemplo, una transferencia de productos de desarrollo/pruebas de EA se lleva a cabo de la misma manera en que se transfiere un producto de EA.

> [!NOTE]
> Las reservas se incluyen en la mayoría de las transferencias de productos admitidas. Sin embargo, hay algunas transferencias en las que las reservas no se incluirán, como se indica en la tabla siguiente.

| Tipo de contrato de producto de origen (actual) | Tipo de contrato de producto de destino (futuro) | Notas |
| --- | --- | --- |
| EA | MOSP (Pago por uso) | <ul><li>La transferencia de una inscripción de EA a una suscripción de MOSP requiere una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/). <li> Las reservas no se transfieren automáticamente y no se admite su transferencia. |
| EA | MCA - Individual | <ul><li>Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). <li> Las reservas no se transfieren automáticamente, por lo que debe abrir una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/) para hacerlo. |
| EA | EA | <ul><li>La transferencia entre inscripciones de EA requiere una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/). <li>Las reservas no se transfieren automáticamente entre inscripciones, por lo que debe abrir una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/) para transferirlas. <li> La transferencia dentro de la misma inscripción es la misma acción que cambiar el propietario de la cuenta. Para más información, consulte [Cambio de la propiedad de la cuenta o de la suscripción de Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership). |
| EA | MCA - Enterprise | <ul><li> La transferencia de todos los productos de inscripción se completa como parte del proceso de transición de MCA desde un EA. Para más información, consulte [Finalización de tareas del Contrato Enterprise en la cuenta de facturación para un contrato de cliente de Microsoft](mca-enterprise-operations.md). <li> Si desea transferir productos específicos (es decir, no todos los productos de una inscripción), consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). |
| EA | MPA | <ul><li> Solo los partners de facturación directa de CSP certificados como [Proveedor de servicios administrados (MSP) experto de Azure](https://partner.microsoft.com/membership/azure-expert-msp) pueden solicitar la transferencia de productos de Azure para sus clientes que tengan un Contrato Enterprise (EA) directo. Para información, consulte [Obtención de la propiedad de la facturación de las suscripciones a Azure para la cuenta de MPA](mpa-request-ownership.md). Solo se permiten las transferencias de productos de los clientes que han aceptado un Contrato de cliente de Microsoft (MCA) y han adquirido un plan de Azure con el programa CSP. <li> Existen limitaciones y restricciones. Para más información, consulte [Transferencia de suscripciones EA a un asociado de CSP](transfer-subscriptions-subscribers-csp.md#transfer-ea-subscriptions-to-a-csp-partner).  |
| MCA - Individual | MOSP (Pago por uso) | <ul><li> Para más información, consulte [Transferencia de la propiedad de facturación de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md). <li> Las reservas no se transfieren automáticamente y no se admite su transferencia. |
| MCA - Individual | MCA - Individual | <ul><li>Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). <li> Se admiten transferencias de reserva de autoservicio. |
| MCA - Individual | EA | <ul><li> Para más información, consulte [Transferencia de la suscripción al EA](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea). <li> Se admiten transferencias de reserva de autoservicio. |
| MCA - Individual | MCA - Enterprise | <ul><li> Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md).<li> Se admiten transferencias de reserva de autoservicio. |
| MCA - Enterprise | MOSP | <ul><li> Requiere una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/). <li> Las reservas no se transfieren automáticamente y no se admite su transferencia. |
| MCA - Enterprise | MCA - Individual | <ul><li> Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). <li> Las reservas no se transfieren automáticamente, por lo que debe abrir una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/) para hacerlo. |
| MCA - Enterprise | MCA - Enterprise | <ul><li> Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). <li> Las reservas no se transfieren automáticamente, por lo que debe abrir una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/) para hacerlo.  |
| Oferta de Azure anterior en CSP | Oferta de Azure anterior en CSP | <ul><li> Requiere una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/). <li> Las reservas no se transfieren automáticamente y no se admite su transferencia. |
| Oferta de Azure anterior en CSP | MPA | Para más información, consulte [Transferencia de las suscripciones de Azure de un cliente a un CSP diferente (en un plan de Azure)](/partner-center/transfer-azure-subscriptions-under-azure-plan). |
| MPA | EA | <ul><li> No se admite la transferencia automática. Cualquier transferencia requiere que los recursos se muevan manualmente del producto MPA existente a un producto de EA recién creado o existente. <li> Use la información de la sección [Realización de transferencias de recursos](#perform-resource-transfers). <li> Las reservas no se transfieren automáticamente y no se admite su transferencia. |
| MPA | MPA | <ul><li> Para más información, consulte [Transferencia de las suscripciones de Azure de un cliente a un CSP diferente (en un plan de Azure)](/partner-center/transfer-azure-subscriptions-under-azure-plan). <li> Las reservas no se transfieren automáticamente y no se admite su transferencia. |
| MOSP (Pago por uso) | MOSP (Pago por uso) | <ul><li> Si va a cambiar el propietario de facturación de la suscripción, consulte [Transferencia de la propiedad de facturación de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md). <li> Las reservas no se transfieren automáticamente, por lo que debe abrir una [incidencia de soporte técnico de facturación](https://azure.microsoft.com/support/create-ticket/) para hacerlo.  |
| MOSP (Pago por uso) | MCA - Individual | <ul><li> Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). <li> Se admiten transferencias de reserva de autoservicio. |
| MOSP (Pago por uso) | EA | Para más información, consulte [Transferencia de la suscripción al EA](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea). |
| MOSP (Pago por uso) | MCA - Enterprise | <ul><li> Para más información, consulte [Transferencia de la propiedad de facturación de la suscripción de Azure en un Contrato de cliente de Microsoft](mca-request-billing-ownership.md). <li> Se admiten transferencias de reserva de autoservicio. |

## <a name="perform-resource-transfers"></a>Realización de transferencias de recursos

Algunas transferencias de productos requieren que mueva manualmente los recursos de Azure entre la suscripción. El traslado de recursos puede provocar tiempo de inactividad; además, hay varias limitaciones a la hora de mover tipos de recursos de Azure como máquinas virtuales, grupos de seguridad de red, instancias de App Services y otros.

Microsoft no proporciona una herramienta para mover automáticamente recursos entre suscripciones. Cuando sea necesario, debe mover manualmente los recursos de Azure entre suscripciones. Para más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Se necesitan tiempo y planeamiento adicionales cuando hay un gran número de recursos para mover.

## <a name="other-planning-considerations"></a>Otras consideraciones de planeación

Lea las secciones siguientes para obtener más información sobre otras consideraciones antes de iniciar una transferencia de productos.

### <a name="resources-transfer-with-subscriptions"></a>Transferencia de recursos con suscripciones

Cuando solo cambia la propiedad de facturación, los recursos no se ven afectados. Sin embargo, al mover un recurso o cambiar el inquilino del servicio, los recursos podrían verse afectados.

### <a name="transfer-a-product-from-one-account-to-another"></a>Transferencia de un producto de una cuenta a otra

Si es administrador de dos cuentas, puede transferir un producto entre ellas. En teoría, estas cuentas se consideran cuentas de dos usuarios distintos, por lo que puede transferir los productos entre ellas.

Para ver los pasos necesarios para transferir el producto, consulte [Transferencia de la propiedad de facturación de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md).

### <a name="transferring-a-product-shouldnt-create-downtime"></a>La transferencia de un producto no debe suponer tiempo de inactividad

Si transfiere un producto a una cuenta en el mismo inquilino de Azure AD, los recursos que se ejecutan en la suscripción no se ven afectados. Sin embargo, la información de contexto guardada en PowerShell no se actualiza, por lo que es posible que tenga que borrarla o cambiar la configuración. Al mover un recurso o cambiar el inquilino del servicio, los recursos podrían verse afectados.

### <a name="new-account-usage-and-billing-history"></a>Historial de uso y facturación de cuenta nueva

La única información disponible para los usuarios de la nueva cuenta es el historial de uso y facturación a partir del momento de la transferencia. El historial de uso y facturación no se transfiere con el producto.

### <a name="remaining-product-credits"></a>Créditos de producto restantes

Si tiene un producto de Visual Studio o Microsoft Partner Network, obtiene créditos mensuales. Los créditos no se transfieren con el producto a la nueva cuenta. El usuario que acepta la solicitud de transferencia debe tener una licencia de Visual Studio para aceptar la solicitud de transferencia. El producto usa el crédito de Visual Studio que está disponible en la cuenta del usuario. Para más información, consulte [Transferencia de la propiedad de facturación de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

### <a name="users-keep-access-to-transferred-resources"></a>Los usuarios mantienen el acceso a los recursos transferidos

Tenga en cuenta que los usuarios con acceso a los recursos de un producto mantienen su acceso cuando se transfiere la propiedad de facturación. Sin embargo, es posible que se eliminen tanto los [roles de administrador](add-change-subscription-administrator.md) como las [asignaciones de roles de Azure](../../role-based-access-control/role-assignments-portal.md). La pérdida de acceso se produce cuando su cuenta se encuentra en un inquilino de Azure AD que no sea el inquilino del producto y el usuario que envió la solicitud de transferencia mueve el producto al inquilino de su cuenta.

Puede ver los usuarios que tienen asignaciones de roles de Azure para acceder a los recursos del producto en Azure Portal. Visite la [página Suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). A continuación, elija el producto que quiere comprobar y seleccione **Control de acceso (IAM)** en el panel izquierdo. Luego, en la parte superior de la página, seleccione **Asignaciones de roles**. La página de asignaciones de roles enumera todos los usuarios que tienen acceso en el producto.

Aunque las [asignaciones de roles de Azure](../../role-based-access-control/role-assignments-portal.md) se eliminen durante la transferencia, es posible que los usuarios de la cuenta del propietario original sigan teniendo acceso al producto mediante otros mecanismos de seguridad, como:

- Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure](../../cloud-services/cloud-services-certs-create.md).
- Claves de acceso para servicios como Almacenamiento. Para más información, consulte [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-account-create.md).
- Credenciales de acceso remoto para servicios como Azure Virtual Machines.

Cuando el destinatario debe restringir el acceso a sus recursos, debe considerar la actualización de todos los secretos asociados al servicio. La mayoría de los recursos se pueden actualizar. Inicie sesión en [Azure Portal](https://portal.azure.com/) y seleccione **Todos los recursos** en el menú Concentrador. Después, seleccione el tamaño del recurso. A continuación, en la página de recursos, seleccione **Configuración**. Aquí puede ver y actualizar los secretos existentes.

### <a name="you-pay-for-usage-when-you-receive-ownership"></a>Se paga por el uso cuando se recibe la propiedad

La cuenta es responsable del pago de cualquier uso que se notifica desde el momento de la transferencia en adelante. Puede haber una parte del uso que se realizara antes de la transferencia, pero que se notificara con posterioridad. El uso se incluye en la factura de la cuenta.

### <a name="transfer-enterprise-agreement-product-ownership"></a>Transferencia de la propiedad de producto con un contrato Enterprise

El administrador de Enterprise puede actualizar la propiedad de cualquier cuenta, incluso después de que el propietario de la cuenta original ya no forme parte de la organización. Para obtener más información acerca de la transferencia de cuentas de Contrato Enterprise de Azure, consulte [Transferencias de Azure Enterprise](ea-transfers.md).

## <a name="next-steps"></a>Pasos siguientes

- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
