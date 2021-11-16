---
title: Pago de suscripciones de Azure mediante cheque o transferencia bancaria
description: Aprenda a pagar las suscripciones de Azure mediante cheque o transferencia bancaria.
author: bandersmsft
ms.reviewer: lishepar
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/08/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8f501056145a2ff4d9589ebcba68219fa89af4d0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058935"
---
# <a name="pay-for-your-azure-subscription-by-check-or-wire-transfer"></a>Pago de su suscripción de Azure mediante cheque o transferencia bancaria

Este artículo se aplica a los clientes con un Contrato de cliente de Microsoft (MCA) y a los clientes que se han registrado en Azure a través del sitio web de Azure (para una cuenta del Programa de Microsoft Online Services también denominada cuenta de pago por uso). Si se ha suscrito a Azure a través de un representante de Microsoft, el método de pago predeterminado se habrá establecido como *cheque o transferencia bancaria*.

Si cambia al pago con cheque o transferencia bancaria, significa que pagará el importe en un plazo de 30 días después de la fecha de factura mediante cheque o transferencia bancaria. 

Cuando solicita cambiar el método de pago a cheque o transferencia bancaria, hay dos resultados posibles:

- Se le aprueba automáticamente y se le pide información sobre su empresa.
- No se le aprueba automáticamente, pero puede enviar una solicitud al soporte técnico de Azure.

> [!IMPORTANT]
> * El pago mediante cheque o transferencia bancaria solo está disponible para los clientes que usan Azure en nombre de una empresa.
> * Pague todos los cargos pendientes antes de cambiar al pago con cheque o transferencia bancaria.
> * El método de pago predeterminado pagará las facturas pendientes. Para que se pague mediante cheque o transferencia bancaria, debe cambiar el método de pago predeterminado a cheque o transferencia bancaria después de que se le haya aprobado.
> * Actualmente, el pago mediante cheque o transferencia bancaria no se admite con Azure global en China.
> * Una vez que se cambie al pago mediante cheque o transferencia bancaria, no podrá volver al pago con tarjeta de crédito o débito.

## <a name="request-to-pay-by-check-or-wire-transfer"></a>Solicitud para el pago mediante cheque o transferencia bancaria

1. Inicie sesión en Azure Portal.
1. Vaya a **Suscripciones** y, a continuación, seleccione aquella para la que desea configurar el pago con cheque o transferencia bancaria.
1. En el menú de la izquierda, seleccione **Métodos de pago**.
1. En la página Métodos de pago, seleccione **Pago por cheque o transferencia bancaria**.  
    :::image type="content" source="./media/pay-by-invoice/payment-methods.png" alt-text="Captura de pantalla que muestra la página Métodos de pago." lightbox="./media/pay-by-invoice/payment-methods.png" :::
1.  En la página **Pago por cheque o transferencia bancaria**, verá un mensaje que indica que puede solicitar el uso del cheque o transferencia bancaria en lugar del pago automático mediante una tarjeta de crédito o débito. Seleccione **Continuar** para iniciar la comprobación.  
    
    :::image type="content" source="./media/pay-by-invoice/pay-check-wire-transfer-continue.png" alt-text="Captura de pantalla que muestra la opción Continuar." :::
1. En función del estado de aprobación:  

    - Si se le aprueba automáticamente, la página muestra un mensaje que indica que se le ha aprobado para pagar mediante cheque o transferencia bancaria. Escriba el **nombre de la empresa** y seleccione **Guardar**.  
        :::image type="content" source="./media/pay-by-invoice/pay-check-wire-transfer.png" alt-text="Captura de pantalla que muestra la aprobación del pago mediante cheque o transferencia bancaria." lightbox="./media/pay-by-invoice/pay-check-wire-transfer.png" :::

    - Si no se pudo procesar la solicitud o esta no se aprueba, debe seguir los pasos descritos en la siguiente sección [Envío de una solicitud para configurar el pago mediante cheque o transferencia bancaria](#submit-a-request-to-set-up-pay-by-check-or-wire-transfer).
1. Si se le ha aprobado, en la página Métodos de pago en **Otros métodos de pago**, a la derecha de **Cheque o transferencia bancaria**, seleccione el símbolo de puntos suspensivos ( **...** ) y, a continuación, seleccione **Establecer como valor predeterminado**.  
    Está todo listo para pagar mediante cheque o transferencia bancaria.

## <a name="submit-a-request-to-set-up-pay-by-check-or-wire-transfer"></a>Envío de una solicitud para configurar el pago mediante cheque o transferencia bancaria

Si no se le aprueba automáticamente, puede enviar una solicitud al soporte técnico de Azure para aprobar el pago mediante cheque o transferencia bancaria. Si se aprueba su solicitud, puede cambiar al pago con cheque o transferencia bancaria en Azure Portal.

1. Inicie sesión en Azure Portal para enviar una solicitud de soporte técnico. Busque y seleccione **Ayuda y soporte técnico**.  
    :::image type="content" source="./media/pay-by-invoice/search-for-help-and-support.png" alt-text="Captura de pantalla de la búsqueda de ayuda y soporte técnico." lightbox="./media/pay-by-invoice/search-for-help-and-support.png" :::
1. Seleccione **Nueva solicitud de soporte técnico**.  
    :::image type="content" source="./media/pay-by-invoice/help-and-support.png" alt-text="Captura de pantalla del vínculo de nueva solicitud de soporte técnico." lightbox="./media/pay-by-invoice/help-and-support.png" :::
1. Seleccione **Facturación** como **tipo de incidencia**. El *tipo de incidencia* es la categoría de la solicitud de soporte técnico. Seleccione la suscripción que desea pagar mediante cheque o transferencia bancaria, seleccione un plan de soporte técnico y, después, seleccione **Siguiente**.
1. Seleccione **Pago** como el **tipo de problema**. El *tipo de problema* es la subcategoría de la solicitud de soporte técnico.
1. Seleccione **Switch to Pay by Invoice** (Cambiar a pago por factura) como el **subtipo de problema**.
1. Escriba la siguiente información en el cuadro **Detalles** y después haga clic en **Siguiente**.
    - El cliente es nuevo o ya existía:
    - Si existe, el método de pago actual:
    - Id. de pedido (solicitar para la opción de factura):
    - Live ID (o Id. org) de administradores de cuentas (debería ser el dominio de la empresa):
    - Id. de cuenta de comercio:
    - Nombre de la empresa (como figura en el registro):
    - Dirección de la empresa (como figura en el registro):
    - Sitio web de la empresa:
    - País:
    - Código o número de identificación fiscal:
    - Empresa establecida el (año):
    - Transacciones comerciales realizadas con Microsoft:
    - Nombre de contacto:
    - Teléfono de contacto:
    - Dirección de correo electrónico de contacto:
    - Justificación sobre por qué desea la opción de pago mediante cheque o transferencia bancaria en lugar de una tarjeta de crédito:
    - Si desea aumentar el número de núcleos, especifique la siguiente información adicional:
        - (Cuota anterior) Núcleos existentes:
        - (Cuota nueva) Núcleos solicitados:
        - Serie y región específica de la suscripción:
    - El **nombre de la empresa** y la **dirección de la empresa** deben coincidir con la información que haya proporcionado para la cuenta de Azure. Para ver o actualizar la información, consulte [Cambio de la información de perfil de la cuenta de Azure](change-azure-account-profile.md).
    - Agregue la información de contacto de facturación en Azure Portal antes de que se pueda aprobar el límite de crédito. La información de contacto debe ser la del departamento de Contabilidad o Finanzas.
1. Compruebe la información de contacto y el método de contacto preferido, y seleccione **Crear**.

Si es necesario realizar una comprobación de crédito debido a la cantidad de crédito que necesita, le enviaremos una solicitud a tal fin. Es posible que le pidamos que especifique los extractos financieros auditados de su empresa. Si no se facilita ninguna información financiera o si la información no es suficiente para aceptar el límite de crédito solicitado, es posible que le pidamos un depósito de seguridad o una carta de crédito standby para aprobar la solicitud de comprobación de crédito.

## <a name="switch-to-pay-by-check-or-wire-transfer-after-approval"></a>Cambio para pagar mediante cheque o transferencia bancaria después de la aprobación

Si tiene una cuenta del Programa Microsoft Online Services (pago por uso) y se le ha aprobado el pago mediante cheque o transferencia bancaria, puede cambiar el método de pago en Azure Portal.

Con un contrato de cliente de Microsoft, puede cambiar el perfil de facturación para pagar mediante cheque o transferencia bancaria.

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Cambio de la suscripción de Azure para pagar mediante cheque o transferencia bancaria

Siga estos pasos para cambiar su suscripción de Azure al pago mediante cheque o transferencia bancaria. *Una vez que se cambie al pago mediante cheque o transferencia bancaria, no podrá volver a la modalidad de tarjeta de crédito*.

1. Vaya a Azure Portal para iniciar sesión como administrador de cuenta. Busque y seleccione **Administración de costos + facturación**.  
    :::image type="content" source="./media/pay-by-invoice/search.png" alt-text="Captura de pantalla en la que se muestra la búsqueda de Administración de costos + facturación en Azure Portal." lightbox="./media/pay-by-invoice/search.png" :::
1. Seleccione la suscripción que desea cambiar al pago mediante cheque o transferencia bancaria.
1. Seleccione **Métodos de pago**.
En la página Métodos de pago, seleccione **Pago por cheque o transferencia bancaria**.  
    :::image type="content" source="./media/pay-by-invoice/payment-methods.png" alt-text="Captura de pantalla que muestra la opción de pago mediante cheque o transferencia bancaria." lightbox="./media/pay-by-invoice/payment-methods.png" :::

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Cambio del perfil de facturación para pagar mediante cheque o transferencia bancaria

Siga los pasos siguientes para cambiar un perfil de facturación para pagar mediante cheque o transferencia bancaria. Solo la persona que se registró en Azure puede cambiar el método de pago predeterminado de un perfil de facturación.

1. Vaya a Azure Portal para ver la información de facturación. Busque y seleccione **Administración de costos + facturación**.
1. En el menú, elija **Perfiles de facturación**.  
    :::image type="content" source="./media/pay-by-invoice/billing-profile.png" alt-text="Captura de pantalla en la que se muestra el elemento de menú Perfiles de facturación." lightbox="./media/pay-by-invoice/billing-profile.png" :::
1. Seleccione un perfil de facturación.
1. En el menú **Perfil de facturación**, seleccione **Métodos de pago**.  
   :::image type="content" source="./media/pay-by-invoice/billing-profile-payment-methods.png" alt-text="Captura de pantalla en la que se muestra el elemento de menú Métodos de pago." lightbox="./media/pay-by-invoice/billing-profile-payment-methods.png" :::
1. En el título *Otros métodos de pago*, seleccione el símbolo de puntos suspensivos (...) después **Establecer como valor predeterminado**.  
    :::image type="content" source="./media/pay-by-invoice/customer-led-switch-to-invoice.png" alt-text="Captura de pantalla en la que se muestran los puntos suspensivos de Cheque o transferencia bancaria y la opción Establecer como valor predeterminado." lightbox="./media/pay-by-invoice/customer-led-switch-to-invoice.png" :::

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Comprobación del acceso a un contrato de cliente de Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

*¿Por qué he recibido una solicitud de un documento legal?*

En ocasiones, Microsoft necesita documentación legal si la información proporcionada está incompleta o no se puede comprobar. Algunos ejemplos pueden incluir:

* Diferencia entre el nombre de cuenta y el nombre de la compañía
* Cambio del nombre

## <a name="next-steps"></a>Pasos siguientes

* Si es necesario, actualice la información de contacto de facturación en [Azure Portal](https://portal.azure.com).