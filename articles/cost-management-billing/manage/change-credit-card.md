---
title: Cambio de la tarjeta de crédito para Azure
description: Describe cómo cambiar la tarjeta de crédito que se usa para pagar una suscripción de Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/27/2021
ms.author: banders
ms.openlocfilehash: fb37278a30e935d2bb65a7b132177a1bd588117d
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731221"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Adición o actualización de una tarjeta de crédito para Azure

Este documento se aplica a los clientes que se han suscrito a Azure online con una tarjeta de crédito.

En Azure Portal, no solo puede cambiar el método de pago predeterminado a una nueva tarjeta de crédito, sino también actualizar los detalles de la tarjeta de crédito. Debe ser [administrador de cuenta](../understand/subscription-transfer.md#whoisaa) o tener los [permisos del Contrato de cliente de Microsoft](understand-mca-roles.md) correctos para realizar estos cambios. También puede reemplazar la tarjeta de crédito actual para todas las suscripciones.

Si desea eliminar una tarjeta de crédito, consulte [Eliminación de un método de pago de la facturación de Azure (versión preliminar)](delete-azure-payment-method.md).

Los métodos de pago admitidos para Microsoft Azure son tarjetas de crédito y cheques o transferencias bancarias. Para obtener la aprobación para pagar por cheque o transferencia bancaria, consulte [Pago de las suscripciones de Azure con factura](pay-by-invoice.md).

Con un Contrato de cliente de Microsoft, los métodos de pago se asocian a perfiles de facturación. Aprenda a [comprobar el acceso a un Contrato de cliente de Microsoft](#check-the-type-of-your-account). Si tiene un Contrato de cliente de Microsoft, consulte el tema sobre cómo [administrar tarjetas de crédito para un Contrato de cliente de Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

>[!NOTE]
> Al crear una suscripción, puede especificar una tarjeta de crédito nueva. Al hacerlo, ninguna otra suscripción se asocia a la tarjeta de crédito nueva. Sin embargo, si más adelante hace los cambios que se indican a continuación, *todas las suscripciones*  utilizarán el método de pago que seleccione.
  >- Si desea activar un método de pago, use la opción **Establecer como activo**.
  >- Use la opción de pago **Reemplazar** para cualquier suscripción.
  >- Cambie el método de pago predeterminado.

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Administración de tarjetas de crédito para una suscripción de Azure

Las secciones siguientes se aplican a los clientes que tienen una cuenta de facturación del programa Microsoft Online Services. Aprenda a [comprobar el tipo de cuenta de facturación](#check-the-type-of-your-account). Si el tipo de cuenta de facturación es el programa de Microsoft Online Services, los métodos de pago se asocian a las suscripciones individuales de Azure. Si se produce un error después de agregar la tarjeta de crédito, consulte [Tarjeta de crédito rechazada al suscribirse a Azure](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-all-subscriptions-by-adding-a-new-credit-card"></a>Cambio de tarjeta de crédito para todas las suscripciones al agregar una tarjeta de crédito nueva

Puede cambiar el crédito predeterminado de la suscripción de Azure a una nueva tarjeta de crédito o a una tarjeta de crédito guardada previamente en Azure Portal. Para cambiar la tarjeta de crédito, debe ser el administrador de cuenta. 

Si se tiene el mismo método de pago activo en varias suscripciones, el cambio del método de pago predeterminado en cualquiera de las suscripciones también actualizará el método de pago activo de las demás.

Puede cambiar la tarjeta de crédito predeterminada de la suscripción a una nueva siguiendo estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de la cuenta.
1. Busque **Administración de costos + facturación**.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Captura de pantalla que muestra Buscar." lightbox="./media/change-credit-card/search.png" :::
1. Seleccione la suscripción a la que quiere agregar la tarjeta de crédito.
1. Seleccione **Métodos de pago**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Captura de pantalla que muestra la opción Administrar métodos de pago seleccionada." lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. En la esquina superior izquierda, seleccione **+ Agregar** para agregar una tarjeta. Aparece un formulario de tarjeta de crédito a la derecha.
1. Escriba los detalles de la tarjeta de crédito.  
    :::image type="content" source="./media/change-credit-card/sub-add-new-default.png" alt-text="Captura de pantalla que muestra la incorporación de una tarjeta nueva." lightbox="./media/change-credit-card/sub-add-new-default.png" :::
1. Para convertir esta tarjeta en el método de pago predeterminado, seleccione **Convertir en mi método de pago predeterminado** arriba del formulario. Esta tarjeta se convertirá en el instrumento de pago activo para todas las suscripciones que utilicen la misma tarjeta que la suscripción seleccionada.
1. Seleccione **Siguiente**.

### <a name="replace-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Reemplazo de la tarjeta de crédito de una suscripción por una tarjeta de crédito que se ha guardado previamente

También puede reemplazar la tarjeta de crédito predeterminada de una suscripción por una que ya esté guardada en la cuenta. Para ello, siga estos pasos. Este procedimiento cambia la tarjeta de crédito para todas las demás suscripciones.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de la cuenta.
1. Busque **Administración de costos + facturación**.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Captura de pantalla que muestra la búsqueda de Cost Management + Billing." lightbox="./media/change-credit-card/search.png" :::
1. Seleccione la suscripción a la que quiere agregar la tarjeta de crédito.
1. Seleccione **Métodos de pago**.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Captura de pantalla que muestra la opción Administrar métodos de pago." lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Seleccione **Reemplazar** para cambiar la tarjeta de crédito actual por una que seleccione.
    :::image type="content" source="./media/change-credit-card/replace-credit-card.png" alt-text="Captura de pantalla que muestra la opción Reemplazar." lightbox="./media/change-credit-card/replace-credit-card.png" :::
1. En **Reemplazar método de pago predeterminado**, seleccione otra tarjeta de crédito por la cual reemplazar la tarjeta de crédito predeterminada y, luego, seleccione **Siguiente**.
    :::image type="content" source="./media/change-credit-card/replace-default-payment-method.png" alt-text="Captura de pantalla que muestra el cuadro Reemplazar método de pago predeterminado." lightbox="./media/change-credit-card/replace-default-payment-method.png" :::
1. Transcurridos unos instantes, verá la confirmación de que se cambió el método de pago.

### <a name="edit-credit-card-details"></a>Edición de los detalles de la tarjeta de crédito

Si se renueva la tarjeta de crédito y el número sigue siendo el mismo, actualice la información de la tarjeta de crédito existente, como la fecha de expiración. Si su número de tarjeta de crédito cambia porque la tarjeta se pierda, se la roben o caduque, siga los pasos de la sección [Adición de una tarjeta de crédito como método de pago](#addcard). No es necesario actualizar el CVV.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de la cuenta.
1. Busque **Administración de costos + facturación**.
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Captura de pantalla de la búsqueda." lightbox="./media/change-credit-card/search.png" :::
1. Seleccione **Métodos de pago**.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Captura de pantalla que muestra Administrar métodos de pago" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Seleccione la tarjeta de crédito que desea editar. Aparecerá un formulario de tarjeta de crédito a la derecha.
    :::image type="content" source="./media/change-credit-card/edit-card-x.png" alt-text="Captura de pantalla que muestra Editar método de pago." lightbox="./media/change-credit-card/edit-card-x.png" :::
1. Actualice los detalles de la tarjeta de crédito.
1. Seleccione **Siguiente**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Administración de tarjetas de crédito para un Contrato de cliente de Microsoft

Las secciones siguientes se aplican a los clientes que tienen un Contrato de cliente de Microsoft y se han suscrito a Azure Online con una tarjeta de crédito y a los que tienen los [permisos del Contrato de cliente de Microsoft](understand-mca-roles.md) correctos. [Aprenda a comprobar si tiene un Contrato de cliente de Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Cambio de la tarjeta de crédito predeterminada

Si tiene un Contrato de cliente de Microsoft, la tarjeta de crédito están asociada a un perfil de facturación. Para cambiar el método de pago de un perfil de facturación, debe ser la persona que se suscribió a Azure y que creó la cuenta de facturación o tener los [permisos del Contrato de cliente de Microsoft](understand-mca-roles.md) correctos.

Si quiere cambiar el método de pago predeterminado del perfil de facturación al pago mediante cheque o transferencia bancaria, consulte [Pago de las suscripciones de Azure con factura](pay-by-invoice.md).

Para cambiar la tarjeta de crédito, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque en **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Perfiles de facturación**.
1. Seleccione un perfil de facturación.
1. En el menú de la izquierda, seleccione **Métodos de pago**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="Captura de pantalla que muestra los métodos de pago en el menú." lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. En la sección **Método de pago predeterminado**, seleccione **Reemplazar**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Captura de pantalla en la que se muestra Reemplazar." lightbox="./media/change-credit-card/change-payment-method-mca.png" :::
1. En la nueva área de la derecha, seleccione una tarjeta existente en la lista desplegable o agregue una nueva seleccionando el vínculo azul **Add new payment method** (Agregar nuevo método de pago).

### <a name="edit-a-credit-card"></a>Edición de una tarjeta de crédito

Puede editar los detalles de la tarjeta de crédito (como actualizar la fecha de expiración) en Azure Portal. 

Para editar una tarjeta de crédito, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque en **Administración de costos + facturación**.
1. En el menú de la izquierda, seleccione **Perfiles de facturación**.
1. Seleccione un perfil de facturación.
1. En el menú de la izquierda, seleccione **Métodos de pago**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="Captura de pantalla que muestra los métodos de pago en el menú." lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. En la sección **Tarjetas de crédito del usuario**, busque la tarjeta que desea editar.
1. Seleccione los puntos suspensivos (`...`) al final de la fila.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Captura de pantalla que muestra los puntos suspensivos." lightbox="./media/change-credit-card/edit-delete-credit-card-mca.png" :::
1. Para editar los detalles de la tarjeta de crédito, seleccione **Editar** en el menú contextual.

## <a name="troubleshooting"></a>Solución de problemas

Azure no admite tarjetas prepago ni virtuales. Si aparecen errores al agregar o actualizar una tarjeta de crédito válida, pruebe a abrir el explorador en modo privado.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

Las siguientes secciones responden a las preguntas más frecuentes sobre cómo cambiar la información de la tarjeta de crédito.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>¿Por qué sigo recibiendo un mensaje similar a "Su sesión de inicio de sesión ha expirado. Haga clic aquí para volver a iniciar sesión"?

Si sigue apareciendo este mensaje de error incluso si ya cerró la sesión y volvió a iniciarla, inténtelo de nuevo con una sesión de exploración privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription"></a>¿Cómo se usa una tarjeta diferente para cada suscripción?

Como ya se mencionó, al crear una suscripción puede especificar una tarjeta de crédito nueva. Al hacerlo, ninguna otra suscripción se asocia a la tarjeta de crédito nueva. Puede agregar varias suscripciones nuevas, cada una con una tarjeta de crédito única. Sin embargo, si más adelante hace los cambios que se indican a continuación, *todas las suscripciones*  utilizarán el método de pago que seleccione.

- Si desea activar un método de pago, use la opción **Establecer como activo**.
- Use la opción de pago **Reemplazar** para cualquier suscripción.
- Cambie el método de pago predeterminado.

### <a name="how-do-i-make-payments"></a>¿Cómo puedo realizar los pagos?

Si ha configurado una tarjeta de crédito como método de pago, se le cobra automáticamente en su tarjeta después de finalizar cada período de facturación. No es necesario hacer nada más.

Si desea [pagar con factura](pay-by-invoice.md), envíe el pago a la ubicación indicada en la parte inferior de la factura.

### <a name="how-do-i-change-the-tax-id"></a>¿Cómo se cambia el número de identificación tributaria?

Para agregar o actualizar el número de identificación fiscal, actualice el perfil en [Azure Portal](https://portal.azure.com) y seleccione **Registro fiscal**. Este número de identificación tributaria se utiliza para los cálculos de exención fiscal y aparece en la factura.

## <a name="check-the-type-of-your-account"></a>Comprobación del tipo de la cuenta

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [reservas de Azure](../reservations/save-compute-costs-reservations.md) para ver si pueden hacerle ahorrar dinero.
- Si desea eliminar una tarjeta de crédito, consulte [Eliminación de un método de pago de la facturación de Azure (versión preliminar)](delete-azure-payment-method.md).