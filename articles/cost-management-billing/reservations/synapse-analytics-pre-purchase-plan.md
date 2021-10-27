---
title: Optimización de los costos de Azure Synapse Analytics con un plan de compra anticipada
description: Descubra cómo ahorrar en los costos de Azure Synapse Analytics cuando realiza una compra anticipada de unidades de confirmación de Azure Synapse (SCU) por un año.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/08/2021
ms.author: banders
ms.openlocfilehash: 031dc79bfabf03bfadf0c7e01171cbb72a15008e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007095"
---
# <a name="optimize-azure-synapse-analytics-costs-with-a-pre-purchase-plan"></a>Optimización de los costos de Azure Synapse Analytics con un plan de compra anticipada

Puede ahorrar en los costos de Azure Synapse Analytics cuando realiza una compra anticipada de unidades de confirmación de Azure Synapse (SCU) por un año. Puede usar las SCU compradas de manera anticipada en cualquier momento durante el período de compra. A diferencia de las máquinas virtuales, las unidades compradas de manera anticipada no expiran al cabo una hora y se pueden usar en cualquier momento durante el período de compra.

Cualquier uso de Azure Synapse Analytics se deduce automáticamente de las SUC compradas de forma anticipada. Para recibir los descuentos de compra anticipada, no es necesario volver a implementar o asignar un plan adquirido de forma anticipada a las áreas de trabajo de Azure Synapse Analytics.

## <a name="determine-the-right-size-to-buy"></a>Determinación del tamaño que se debe comprar

Una compra anticipada de Synapse se aplica a todas las cargas de trabajo y niveles de Synapse. El plan de compra anticipada se puede considerar como un grupo de unidades de confirmación de Synapse de prepago. El uso se deduce del grupo, independientemente de la carga de trabajo o el nivel. Otros gastos tales como proceso, almacenamiento y redes se cobran por separado.

El descuento por compra anticipada de Synapse se aplica al uso de los siguientes productos:

- Grupo de SQL dedicado de Azure Synapse Analytics
- Red virtual administrada de Azure Synapse Analytics
- Canalizaciones de Azure Synapse Analytics
- Grupo de SQL de Azure Synapse Analytics
- Grupo de Apache Spark sin servidor de Azure Synapse Analytics: optimizado para memoria
- Flujo de datos de Azure Synapse Analytics: básico
- Flujo de datos de Azure Synapse Analytics: estándar

Para más información sobre los niveles de SCU disponibles y los descuentos de precios, usará la experiencia de compra de reserva en la sección siguiente.

## <a name="purchase-synapse-commit-units"></a>Compra de unidades de confirmación de Synapse

Los planes de Synapse se compran en [Azure Portal](https://portal.azure.com). Para comprar un plan de compra anticipada, debe tener el rol de propietario al menos en una suscripción Enterprise.

- Debe tener un rol de propietario al menos en una suscripción Enterprise (números de oferta: MS-AZR-0017P o MS-AZR-0148P) o Contrato de cliente de Microsoft o una suscripción individual con tarifas de pago por uso (números de oferta: MS-AZR-0003P o MS-AZR-0023P).
- En el caso de las suscripciones de Contrato Enterprise (EA), la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com/). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción.

### <a name="to-purchase"></a>Para comprar:

1. Vaya a [Azure Portal](https://portal.azure.com/?synapse=true#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand/autoOpenSpecPicker//productType/Reservation).
1. Si es necesario, vaya a **Reservas** y, luego, en la parte superior de la página, seleccione **+ Agregar**.
1. En la página Comprar reservas, seleccione **Azure Synapse Analytics Pre-Purchase Plan** (Plan de compra anticipada de Azure Synapse Analytics).
1. En la página Seleccione el producto que quiere comprar, elija una suscripción. Use la lista **Suscripción** para seleccionar la que se va a usar para pagar la capacidad reservada. Los costos anticipados de la capacidad reservada se cobran en el método de pago de la suscripción. Los cargos se deducirán del saldo de prepago de Azure (antes llamado compromiso monetario) de la inscripción o se cobrarán como parte del uso por encima del límite.
1. Seleccione un ámbito. Use la lista **Ámbito** para seleccionar el ámbito de la suscripción:
    - El ámbito **Grupo de recursos único**: el descuento por reserva se aplica a los recursos coincidentes solo del grupo de recursos seleccionado.
    - El ámbito **Suscripción única**: el descuento por reserva se aplica a los recursos coincidentes de la suscripción seleccionada.
    - El ámbito **Compartido**: el descuento por reserva se aplica a los recursos coincidentes en suscripciones válidas que están en el contexto de facturación. Para los clientes con Contrato Enterprise, el contexto de facturación es la inscripción.
    - **Grupo de administración**: aplica el descuento de reserva al recurso correspondiente en la lista de suscripciones que forman parte del grupo de administración y del ámbito de facturación.
1. Seleccione el número de unidades de confirmación de Azure Synapse que quiere comprar y complete la compra.  
    :::image type="content" source="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" alt-text="Captura de pantalla que muestra la opción para seleccionar la experiencia del producto del plan de compra anticipada de Azure Synapse Analytics." lightbox="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" :::

## <a name="change-scope-and-ownership"></a>Cambio del ámbito y de la propiedad

Puede realizar los siguientes tipos de cambios en una reserva después de haberla comprado:

- Actualización del ámbito de la reserva
- Control de acceso basado en roles de Azure (RBAC de Azure)

No se puede dividir ni combinar un plan de compra anticipada de unidades de confirmación de Synapse. Para obtener más información sobre cómo administrar las reservas, consulte [Administración de reservas](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Cancelaciones e intercambios

No se admite la cancelación ni el intercambio en los planes de compra anticipada de Synapse. Todas las compras son definitivas.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

- [¿Qué es Azure Reservations?](save-compute-costs-reservations.md)
- [Administración de Azure Reservations](manage-reserved-vm-instance.md)
- [Información sobre el descuento de Azure Reservations](understand-reservation-charges.md)
- [Información sobre el uso de reservas para suscripciones de pago por uso](understand-reserved-instance-usage.md)
- [Información sobre el uso de reservas para la inscripción Enterprise](understand-reserved-instance-usage-ea.md)