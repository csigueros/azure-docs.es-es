---
title: Solución de problemas de suscripciones no aptas en Azure Portal
description: Este artículo le ayuda a solucionar los problemas del mensaje de error "No hay suscripciones aptas" de Azure Portal al intentar comprar una reserva.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 40002ab69c39481393654629a44a038dfc9d01af
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988938"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Solución de problemas de suscripciones no aptas

Este artículo le ayuda a solucionar los problemas del mensaje de error *No hay suscripciones aptas* de Azure Portal al intentar comprar una reserva.

## <a name="symptoms"></a>Síntomas

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Reservas**.
1. Seleccione **Agregar** y, luego, elija un servicio.
1. Se muestra el siguiente mensaje de error:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. En el área **Seleccione el producto que quiere comprar**, expanda la lista **Suscripción de facturación** para ver el motivo por el que una suscripción específica no es apta para comprar una instancia reservada. En la imagen siguiente se muestran ejemplos de por qué no se puede comprar una reserva.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Ejemplo que muestra por qué no se puede comprar una reserva" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Causa

Para comprar una instancia reservada de Azure, debe tener al menos una suscripción que cumpla los requisitos siguientes:

- La suscripción debe ser un tipo de oferta admitido. Los tipos de oferta admitidos son: Pago por uso, Proveedor de soluciones en la nube (CSP), Microsoft Azure Enterprise o Contrato de cliente de Microsoft.
- Debe ser propietario o comprador de una reserva de la suscripción.

Si no tiene una suscripción que cumpla los requisitos, recibirá el error `No eligible subscriptions`.

### <a name="cause-1"></a>Causa 1

La suscripción debe ser un tipo de oferta admitido. Los tipos de oferta admitidos son: Pago por uso, CSP, Microsoft Azure Enterprise o Contrato de cliente de Microsoft. El tipo de suscripción no pertenece a uno de los admitidos. Al seleccionar una suscripción que tiene un tipo de oferta que no admite reservas, verá el siguiente error.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Ejemplo que muestra el mensaje de error La suscripción no es apta para la compra" :::

### <a name="cause-2"></a>Causa 2

Debe ser propietario o comprador de una reserva de la suscripción. Si no tiene permisos suficientes, verá el siguiente error.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Solución

- Si la oferta actual no admite reservas, debe crear otra suscripción de Azure.
- Si no tiene acceso a una reserva existente, puede obtener acceso a ella a través del propietario actual.

### <a name="solution-1"></a>Solución 1

Para comprar una reserva, debe crear una suscripción de Azure que admita reservas.

- Si tiene una evaluación gratuita de Azure, puede [actualizar la suscripción](../manage/upgrade-azure-subscription.md).
- Puede crear una suscripción de Azure con [tarifas de pago por uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Regístrese con un [contrato de cliente de Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) y cree una suscripción.
- Compre una nueva suscripción con un [CSP](https://www.microsoft.com/solution-providers/home) y cree una suscripción.

### <a name="solution-2"></a>Solución 2

Para obtener acceso de propietario a una reserva, debe obtener acceso a:

- La orden de reserva con la que se compró la reserva
- La propia reserva

El propietario actual de la orden de reserva o de la reserva puede delegarle el acceso mediante estos pasos.

Para que otras personas puedan administrar las reservas, tiene dos opciones:

- Delegue la administración del acceso para un pedido de reserva individual mediante la asignación del rol Propietario a un usuario en el ámbito de recursos del pedido de reserva. Si desea otorgar un acceso limitado, seleccione otro rol.  
     Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- Agregue un usuario como administrador de facturación a un Contrato Enterprise o un Contrato de cliente de Microsoft:
    - En el caso de un Contrato Enterprise, agregue usuarios con el rol de _Administrador de empresa_ para ver y administrar todos los pedidos de reserva que se aplican al Contrato Enterprise. Los usuarios con el rol de _Administrador de empresa (solo lectura)_ solo pueden ver la reserva. Ni los administradores de departamento ni los propietarios de cuentas no pueden ver reservas, _salvo que_ se les agreguen mediante el Control de acceso (IAM). Para más información, consulte [Administración de roles de Azure Enterprise](../manage/understand-ea-roles.md).

        _Los administradores de empresa pueden tomar posesión de un pedido de reserva y pueden agregar otros usuarios a una reserva mediante el Control de acceso (IAM)._
    - En el caso de un Contrato de cliente de Microsoft, los usuarios con el rol Propietario de perfil de facturación o el rol Colaborador de perfil de facturación pueden administrar todas las compras de reserva realizadas mediante el perfil de facturación. Los lectores del perfil de facturación y los administradores de facturas pueden ver todas las reservas que se pagan con el perfil de facturación. Sin embargo, no pueden realizar cambios en las reservas.
    Para más información, consulte [Tareas y roles del perfil de facturación](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Para más información, consulte [Agregar o cambiar los usuarios que pueden administrar una reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="next-steps"></a>Pasos siguientes

- Revise [Agregar o cambiar los usuarios que pueden administrar una reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default) si necesita tener un propietario de reserva que le conceda acceso.
