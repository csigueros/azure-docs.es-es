---
title: 'Pago por adelantado de planes de software: Azure Reservations'
description: Descubra cómo puede pagar por adelantado planes de software y ahorrar dinero en los costos de pago por uso.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: c46215bde33f726aba61ad42bf27a14ac2edb4dc
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216502"
---
# <a name="prepay-for-azure-software-plans"></a>Pago por adelantado de planes de software de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

Cuando paga por adelantado por el uso de software SUSE y RedHat en Azure, puede ahorrar dinero con respecto a los costos de pago por uso. Los descuentos solo se aplican a los medidores de SUSE y de RedHat, y no al uso de la máquina virtual. Puede comprar reservas para máquinas virtuales por separado para obtener un ahorro adicional.

Puede comprar planes de software SUSE y RedHat en Azure Portal. Para comprar un plan:

- Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con precios de pago por uso.
- En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com/). Si ese valor está deshabilitado, debe ser un administrador de EA para la suscripción.
- En el caso del programa del Proveedor de soluciones en la nube (CSP), solo los agentes de administración o de ventas pueden comprar planes de software.

## <a name="buy-a-software-plan"></a>Compra de un plan de software

1. Inicie sesión en Azure Portal y vaya a [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Haga clic en **agregar** y, después, seleccione el plan de software que desee comprar.
Rellene todos los campos obligatorios. Cualquier máquina virtual de SUSE Linux o de RedHat que coincida con los atributos de lo que compra obtiene el descuento. El número real de implementaciones que obtienen el descuento depende del ámbito y la cantidad seleccionada.
3. Seleccione una suscripción. Se utiliza para pagar el plan.
Los costos anticipados de la reserva se cargan al método de pago de la suscripción. El tipo de suscripción debe ser Contrato Enterprise (números de oferta: MS-AZR-0017P o MS-AZR-0148P) o un contrato individual con precios de pago por uso (números de oferta: MS-AZR-0003P o MS-AZR-0023P).
    - Para una suscripción Enterprise, los cargos se deducen del pago por adelantado de Azure (antes conocido como saldo de compromiso monetario) de la inscripción, o se cobran como uso por encima del límite.
    - Para una suscripción individual con precios de pago por uso, los cargos se cobran en el método de pago de la factura o la tarjeta de crédito de la suscripción.
4. Seleccione un ámbito. El ámbito puede cubrir una o varias suscripciones (ámbito compartido).
    - Suscripción única: el descuento del plan se aplica al uso correspondiente en la suscripción.
    - Compartido: el descuento del plan se aplica a las instancias correspondientes en cualquier suscripción del contexto de facturación. Para los clientes de Enterprise, el contexto de facturación es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de planes individuales con precios de pago por uso, el contexto de facturación son todas las suscripciones de planes individuales con precios de pago por uso creadas por el administrador de cuenta.
    - Grupo de administración: aplica el descuento de reserva al recurso correspondiente en la lista de suscripciones que forman parte del grupo de administración y del ámbito de facturación.
    - Grupo de recursos único: el descuento por reserva se aplica a los recursos coincidentes solo del grupo de recursos seleccionado.
5. Seleccione un producto para elegir el tamaño de máquina virtual y el tipo de imagen. El descuento se aplica solo al tamaño de máquina virtual seleccionado.
6. Seleccione un plazo de un año o tres años.
7. Elija una cantidad, que es el número de instancias de máquina virtual de prepago a las que se aplica el descuento de facturación.
8. Agregue el producto al carro de compra, revíselo y cómprelo.

El descuento de reserva se aplica automáticamente al medidor de software que paga por adelantado. El plan no cubre los cargos de proceso de máquina virtual. Puede adquirir por separado las reservas de VM.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Descuento aplicado a diferentes tamaños de máquina virtual de SUSE

Del mismo modo que con las instancias reservadas de VM, los planes SUSE Linux ofrecen flexibilidad de tamaño de instancia. El descuento se aplica incluso cuando se implementa una VM con un tamaño distinto al del plan SUSE que compró. Para más información, consulte [Descubra cómo se aplica el descuento del plan de software](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Descuento del plan RedHat

Los planes están disponibles solo para máquinas virtuales de Red Hat Enterprise Linux. El descuento no se aplica a las máquinas virtuales de RedHat Enterprise Linux SAP HANA ni a las de RedHat Enterprise Linux SAP Business Apps.

Se aplican descuentos de plan RedHat solo para el tamaño de máquina virtual seleccionado en el momento de la compra. Los planes RHEL no se reembolsarán ni intercambiarán después de la compra.


## <a name="cancellation-and-exchanges-not-allowed"></a>No se admiten cambios ni cancelaciones

Una vez comprado, un plan SUSE o RedHat no se puede cancelar ni cambiar. Compruebe el uso para asegurarse de comprar el plan adecuado. Para saber identificar lo que se tiene que comprar, consulte [Descubra cómo se aplica el descuento del plan de software](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo administrar una reserva, consulte [Administración de reservas de Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para más información, vea los siguientes artículos:

- [¿Qué es Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Administración de reservas en Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Información sobre cómo se aplica el descuento por la reserva de SUSE](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Información sobre el uso de reservas para suscripciones de pago por uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Información sobre el uso de reservas para la inscripción Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
