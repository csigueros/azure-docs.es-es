---
title: Eliminación de un método de pago de la facturación de Azure
description: Describe cómo eliminar un método de pago usado por una suscripción de Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 194c37d38242e3376516b660dd37071d128b77fa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650066"
---
# <a name="delete-an-azure-billing-payment-method"></a>Eliminación de un método de pago de la facturación de Azure

En este documento se proporcionan instrucciones para ayudarle a eliminar un método de pago, como una tarjeta de crédito, desde distintos tipos de suscripciones de Azure. Se pueden eliminar métodos de pago de:

- Contrato de cliente de Microsoft (MCA).
- Microsoft Online Services Program (MOSP), conocido también como pago por uso.

Sea cual sea su tipo de suscripción de Azure, debe cancelarla para poder eliminar su método de pago asociado.

No se admite la eliminación de un método de pago de otros tipos de suscripción de Azure, como Microsoft Partner Agreement y Contrato Enterprise.

## <a name="delete-an-mca-payment-method"></a>Eliminación de un método de pago de MCA

El usuario que creó la cuenta de Contrato de cliente de Microsoft es el único que puede eliminar un método de pago.

Para eliminar un método de pago de Contrato de cliente de Microsoft, siga estos pasos.

1. Inicie sesión en Azure Portal en https://portal.azure.com/.
1. Vaya a **Administración de costos + facturación**.
1. Si fuera necesario, seleccione un ámbito de facturación.
1. En la lista de menús de la izquierda, en **Facturación**, seleccione **Perfiles de facturación**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Captura de pantalla de ejemplo que muestra los perfiles de facturación en Azure Portal" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. En la lista de perfiles de facturación, seleccione en el que se use el método de pago.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Imagen de ejemplo que muestra la lista de perfiles de facturación" :::
1. En la lista de menús de la izquierda, en **Configuración**, seleccione **Métodos de pago**.
1. En la página de métodos de pago del perfil de facturación, se muestra una tabla de métodos de pago en la sección **Tarjetas de crédito del usuario**. Busque la tarjeta de crédito que desea eliminar, seleccione los puntos suspensivos ( **...** ) y, después, seleccione **Eliminar**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Ejemplo que muestra dónde se elimina una tarjeta de crédito" :::
1. Aparece la página Delete a payment method (Eliminar un método de pago). Azure comprueba si el método de pago está en uso.
    - Cuando el método de pago no se usa, la opción **Eliminar** está habilitada. Selecciónela para eliminar la información de la tarjeta de crédito.
    - Si el método de pago se usa, es preciso reemplazarlo o desasociarlo. Siga leyendo las secciones posteriores, ya que en ellas se explica cómo **desasociar** un método de pago que una suscripción no utilice.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Desasociación de un método de pago usado por un perfil de facturación de MCA

Si un perfil de facturación de MCA utiliza su método de pago, verá un mensaje similar al del ejemplo siguiente.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Imagen de ejemplo que muestra que en Contrato de cliente de Microsoft se usa un método de pago" :::

Para desasociar un método de pago es preciso cumplir una lista de condiciones. Si alguna no se cumple, aparecen instrucciones que explican cómo cumplirla. También aparece un vínculo que le lleva a la ubicación en la que puede resolver la condición.

Cuando se cumplan todas las condiciones, podrá desasociar el método de pago del perfil de facturación.

> [!NOTE]
> Cuando se desasocia el método de pago predeterminado, el perfil de facturación pasa a un estado _inactivo_. Nada de lo que se elimine en este proceso se podrá recuperar. Una vez que un perfil de facturación se establece como inactivo, para crear recursos debe registrarse en una nueva suscripción de Azure.

#### <a name="to-detach-a-payment-method"></a>Para desasociar un método de pago

1. En el área Delete a payment method (Eliminar un método de pago), seleccione el vínculo **Detach the current payment method** (Desasociar el método de pago actual).
1. Si se cumplen todas las condiciones, seleccione **Detach** (Desasociar). De lo contrario, continúe con el paso siguiente.
1. Si Detach (Desasociar) no está disponible, se muestra una lista de condiciones. Realice las acciones de la lista. Seleccione el vínculo que se muestra en el área Detach the default payment method (Desasociar el método de pago predeterminado). Este es un ejemplo de una acción correctiva que explica las acciones que necesita realizar.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Ejemplo que muestra una acción correctiva necesaria para desasociar un método de pago de MCA" :::
1. Al seleccionar el vínculo de la acción correctiva, se le redirige a la página de Azure en que se realiza la acción. Realice la acción de corrección que sea necesaria.
1. Si fuera necesario, complete las restantes acciones correctivas.
1. Vuelva a **Administración de costos + facturación** > **Perfiles de facturación** > **Métodos de pago**. Seleccione **Desasociar**. En la parte inferior de la página Detach the default payment method (Desasociar el método de pago predeterminado), seleccione **Detach** (Desasociar).

> [!NOTE]
> - Después de cancelar una suscripción, esta puede tardar hasta 90 días en eliminarse. Si desea reducir la espera, abra una solicitud de soporte técnico de Azure para pedir la eliminación inmediata de la suscripción.
> - No se puede eliminar un método de pago hasta que se hayan satisfecho todos los cargos anteriores de un perfil de facturación. Si está en un período de facturación activo, debe esperar hasta que finalice dicho período para eliminar el método de pago. **Mientras espera a que finalice el período de facturación, asegúrese de que se cumplen las restantes condiciones para la desasociación**.

## <a name="delete-a-mosp-payment-method"></a>Eliminación de un método de pago de MOSP

Los métodos de pago deben eliminarlos los administradores de cuenta.

Si la suscripción a MOSP está utilizando el método de pago, siga estos pasos.

1. Inicie sesión en Azure Portal en https://portal.azure.com/.
1. Vaya a **Administración de costos + facturación**.
1. Si fuera necesario, seleccione un ámbito de facturación.
1. En la lista de menús de la izquierda, en **Facturación**, seleccione **Métodos de pago**.
1. En el área Métodos de pago, seleccione la _línea_ en la que está su método de pago. No seleccione el vínculo del método de pago. Es posible que se indique visualmente que ha seleccionado el método de pago.
1. Seleccione **Eliminar**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Ejemplo que muestra una acción correctiva necesaria para desasociar un método de pago de MOSP" :::
1. Si se cumplen todas las condiciones, en el área Delete a payment method (Eliminar un método de pago), seleccione **Delete** (Eliminar). Si la opción Delete (Eliminar) no está disponible, vaya al paso siguiente.
1. Se muestra una lista de condiciones. Realice las acciones de la lista. Seleccione el vínculo que se muestra en el área Delete a payment method (Eliminar un método de pago).  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Imagen de ejemplo que muestra que en la suscripción MOSP se usa un método de pago" :::
1. Al seleccionar el vínculo de la acción correctiva, se le redirige a la página de Azure en que se realiza la acción. Realice la acción de corrección que sea necesaria.
1. Si fuera necesario, complete las restantes acciones correctivas.
1. Vuelva a **Administración de costos + facturación** > **Perfiles de facturación** > **Métodos de pago** y elimine el método de pago.

> [!NOTE]
> Después de cancelar una suscripción, esta puede tardar hasta 90 días en eliminarse. Si desea reducir la espera, abra una solicitud de soporte técnico de Azure para pedir la eliminación inmediata de la suscripción.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la cancelación de una suscripción de Azure, consulte el artículo sobre la [cancelación de una suscripción de Azure](cancel-azure-subscription.md).
- Para más información sobre la adición o actualización de tarjetas de crédito, consulte [Agregar, actualizar o quitar una tarjeta de crédito para Azure](change-credit-card.md).