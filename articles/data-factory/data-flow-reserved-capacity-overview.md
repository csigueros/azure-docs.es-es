---
title: Ahorro de costos de proceso con capacidad reservada
description: Obtenga información sobre cómo comprar capacidad reservada de flujo de datos de Azure Data Factory para ahorrar en los costos de proceso.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.date: 02/05/2021
ms.openlocfilehash: a46a84b9dfc1b167f41d15b4e9e5fa0e91fd1b69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740986"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Ahorro de costos para los recursos con capacidad reservada: flujos de datos de Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ahorre dinero en los costos de flujo de datos de Azure Data Factory al comprometerse con una reserva de recursos de proceso en lugar de elegir el precio de pago por uso. Con la capacidad reservada, se compromete a usar flujos de datos de ADF durante un período de uno a tres años para obtener un descuento considerable en los costos de proceso. Para comprar capacidad reservada, debe especificar la región de Azure, el tipo de proceso, la cantidad del recuento de núcleos y el período.

No es necesario asignar la reserva a una fábrica o entorno de ejecución de integración específicos. Las fábricas existentes o recién implementadas obtienen la ventaja automáticamente. Al comprar una reserva, se compromete a usar el servicio por los costos de proceso de flujo de datos durante un período de uno a tres años. Tan pronto como se compra una reserva, los costos de proceso que coinciden con los atributos de la reserva dejan de pagarse según las tarifas de pago por uso. 

Puede comprar [capacidad reservada](https://portal.azure.com) si elige reservas [por adelantado o con pagos mensuales](../cost-management-billing/reservations/prepare-buy-reservation.md). Para adquirir capacidad reservada:

- Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con tarifas de pago por uso.
- En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción. Capacidad reservada.

Para obtener más información sobre cómo se les cobra a los clientes de empresa y a los de Pago por uso las compras de reservas, consulte [Información sobre el uso de reservas de Azure para la inscripción Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [Información sobre el uso de reservas de Azure para suscripciones de pago por uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> La compra de capacidad reservada no asigna previamente ni reserva recursos de infraestructura específicos (máquinas virtuales o clústeres) para su uso.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Determinación de los tamaños correctos de Azure IR necesarios antes de la compra

El tamaño de la reserva se debe basar en la cantidad total de proceso que usan los flujos de datos existentes o que se van a implementar pronto con el mismo nivel de proceso.

Por ejemplo, supongamos que ejecuta una canalización cada hora usando con 32 núcleos optimizados para memoria. Además, supongamos que planea implementar en el siguiente mes una canalización adicional que utiliza 64 núcleos de uso general. Y también supongamos que sabe que necesitará estos recursos durante al menos 1 año. En este caso, escriba el número de núcleos necesarios para cada tipo de proceso durante 1 hora. En Azure Portal, busque Reservas. Elija Data Factory > Flujos de datos > y, después, escriba 32 para los núcleos optimizados para memoria y 64 para los de uso general.

## <a name="buy-reserved-capacity"></a>Compra de capacidad reservada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Reservations**.
3. Seleccione **Agregar** y, en el panel **Comprar reservas**, seleccione **ADF Data Flows** (Flujos de datos de ADF) para comprar una nueva reserva de flujos de datos de ADF.
4. Rellene los campos obligatorios y los atributos seleccionados serán aptos para obtener el descuento en la capacidad reservada. El número real de flujos de datos que obtienen el descuento depende del ámbito y la cantidad seleccionada.
5. Revise el costo de la reserva de capacidad reservada en la sección **Costos**.
6. Seleccione **Comprar**.
7. Haga clic en **Ver esta reserva** para conocer el estado de la compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelación, intercambio o reembolso de reservas

Puede cancelar, intercambiar o reembolsar reservas con ciertas limitaciones. Para más información, consulte [Autoservicio de intercambios y reembolsos de reservas de Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

- [Información sobre el descuento de Azure Reservations](data-flow-understand-reservation-charges.md)
