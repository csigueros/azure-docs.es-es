---
title: Ahorro de costos con capacidad reservada
description: Obtenga información sobre cómo comprar capacidad reservada de SQL Managed Instance habilitado para Azure Arc para ahorrar costos.
services: sql-database
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 10/27/2021
ms.openlocfilehash: 70a6f67fb5a7443e84c937c9a5bf56db5980022e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091085"
---
# <a name="reserved-capacity---azure-arc-enabled-sql-managed-instance"></a>Capacidad reservada: SQL Managed Instance habilitado para Azure Arc

Ahorre dinero con SQL Managed Instance habilitado para Azure Arc al comprometerse a una reserva de servicios de Azure Arc en lugar de elegir el pago por uso. Con la capacidad reservada, se compromete a usar SQL Managed Instance habilitado para Azure Arc durante un período de uno a tres años para obtener un descuento considerable en el precio del servicio. Para comprar capacidad reservada, debe especificar la región de Azure, el tipo de implementación, el nivel de servicio y el período.

No es necesario asignar la reserva a una base de datos específica o instancia administrada. Al igualarse las implementaciones existentes en ejecución o las recién implementadas se obtendrá la ventaja automáticamente. Al comprar una reserva, se compromete a usar los servicios de Azure Arc durante uno o tres años. Tan pronto como se compra una reserva, los costos del servicio que coinciden con los atributos de la reserva dejan de pagarse según las tarifas de pago por uso. 

Una reserva solo se aplica al costo de los servicios de Azure Arc y no cubre los costos de IP de SQL ni ningún otro cargo. Al final del plazo de reserva, la ventaja en la facturación expira y la instancia administrada se factura según los precios de pago por uso. Las reservas no se renuevan de manera automática. Para información sobre precios, consulte el artículo sobre la [oferta de capacidad reservada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Puede comprar la capacidad reservada de timbres en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/reservationsBrowse). Pague la reserva [por adelantado o mensualmente](../../cost-management-billing/reservations/prepare-buy-reservation.md). Para adquirir capacidad reservada:

- Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con tarifas de pago por uso.
- En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción. Capacidad reservada.

Para obtener más información sobre cómo se les cobra a los clientes empresariales y a los de pago por uso las compras de reservas, consulte [Información sobre el uso de reservas de Azure para la inscripción Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [Información sobre el uso de reservas de Azure para suscripciones de pago por uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-correct-size-before-purchase"></a>Determinación del tamaño correcto antes de la compra

El tamaño de la reserva debe basarse en la cantidad total de recursos de proceso medidos en núcleos virtuales usados por las instancias administradas existentes o que pronto se implementarán dentro de un ámbito de reserva de una región específica.

En la lista siguiente se muestra un escenario para proyectar cómo reservar recursos: 

* **Actual**: 
  - Una instancia administrada de 16 núcleos virtuales de nivel de uso general
  - Dos instancias administradas de 8 núcleos virtuales de nivel crítico para la empresa

* **En el próximo año agregará**: 
  - Una instancia administrada adicional de 16 núcleos virtuales de nivel de uso general
  - Una instancia administrada adicional de 32 núcleos virtuales de nivel crítico para la empresa

* **Compre una reserva para**:
  - Reserva de un año de 32 (2×16) núcleos virtuales para la instancia administrada de nivel de uso general
  - Reserva de un año de 48 (2×8+32) núcleos virtuales para la instancia administrada de nivel crítico para la empresa 

## <a name="buy-reserved-capacity"></a>Compra de capacidad reservada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Reservations**.
3. Seleccione **Agregar** y, en el panel **Comprar reservas**, seleccione **SQL Managed Instance** para comprar una nueva reserva para SQL Managed Instance habilitado para Azure Arc.
4. Rellene todos los campos obligatorios. Los recursos existentes de SQL Managed Instance que coincidan con los atributos seleccionados serán aptos para el descuento en la capacidad reservada. El número real de bases de datos o instancias administradas que obtienen el descuento depende del ámbito y la cantidad seleccionados.

    En la siguiente tabla se describen los campos obligatorios.
    
    | Campo      | Descripción|
    |------------|--------------|
    |Suscripción|Suscripción que se usa para pagar la reserva de capacidad. Los costos anticipados de la reserva se cobran en el método de pago de la suscripción. El tipo de suscripción debe ser un contrato Enterprise (número de la oferta: MS-AZR-0017P o MS-AZR-0148P) o un contrato individual con precio de pago por uso (número de la oferta: MS-AZR-0003P o MS-AZR-0023P). Para una suscripción Enterprise, los cargos se deducen del pago por adelantado de Azure (antes conocido como saldo de compromiso monetario) de la inscripción, o se cobran como uso por encima del límite. Para una suscripción individual con precios de pago por uso, los cargos se cobran en el método de pago de la factura o la tarjeta de crédito de la suscripción.|
    |Ámbito       |El ámbito de la reserva de núcleos virtuales puede cubrir una suscripción o varias (ámbito compartido). Si selecciona: <br/><br/>**Compartido**: el descuento por la reserva de núcleos virtuales se aplica a la base de datos o la instancia administrada en ejecución en cualquiera de las suscripciones en el contexto de facturación. Para los clientes Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.<br/><br/>**Suscripción única**: el descuento por la reserva de núcleos virtuales se aplica a las bases de datos o las instancias administradas de esta suscripción. <br/><br/>**Grupo de recursos único**: el descuento de reserva se aplica a las instancias administradas de la suscripción seleccionada y al grupo de recursos seleccionado de esa suscripción.</br></br>**Grupo de administración**: el descuento de reserva se aplica a las instancias administradas de la lista de suscripciones que forman parte del grupo de administración y de ámbito de facturación.|
    |Region      |Región de Azure que está cubierta por la reserva de capacidad.|
    |Tipo de implementación|El tipo de recurso de SQL para el que desea adquirir la reserva.|
    |Nivel de rendimiento|Nivel de servicio para las bases de datos o las instancias administradas. |
    |Término        |Un año o tres años.|
    |Cantidad    |Número de recursos de proceso que se compran dentro de la reserva de capacidad reservada. La cantidad es el número de núcleos virtuales de la región de Azure y el nivel de rendimiento seleccionados que se están reservando y obtendrán el descuento de facturación. Por ejemplo, si ejecuta o planea ejecutar varias instancias administradas con la capacidad de proceso total de 16 núcleos virtuales Gen5 en la región Este de EE. UU., especifique la cantidad como 16 para maximizar las ventajas para todas las bases de datos. |

1. Revise el costo de la reserva de capacidad reservada en la sección **Costos**.
1. Seleccione **Comprar**.
1. Haga clic en **Ver esta reserva** para conocer el estado de la compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelación, intercambio o reembolso de reservas

Puede cancelar, intercambiar o reembolsar reservas con ciertas limitaciones. Para más información, consulte [Autoservicio de intercambios y reembolsos de reservas de Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidad de tamaño del núcleo virtual

La flexibilidad de tamaño del núcleo virtual le ayuda a escalar o reducir verticalmente dentro de un nivel de rendimiento y una región, sin perder los beneficios de la capacidad reservada. Si se mantiene un búfer no aplicado en la reserva, puede administrar de manera eficaz los picos de rendimiento sin sobrepasar el presupuesto.

## <a name="limitation"></a>Limitación

Los precios de capacidad reservada solo se admiten para características y productos que se encuentran en estado de disponibilidad general. 

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

El descuento por la reserva de núcleos virtuales se aplica automáticamente al número de instancias administradas que coincidan con el ámbito y los atributos de la reserva de capacidad. Puede actualizar el ámbito de la reserva de capacidad mediante [Azure Portal](https://portal.azure.com), PowerShell, la CLI de Azure o la API.

Para obtener información sobre los niveles de servicio para SQL Managed Instance habilitado para Azure Arc, consulte [Niveles de servicio de SQL Managed Instance habilitado para Azure Arc](service-tiers.md).

- Para obtener información sobre los niveles de servicio de Azure SQL Managed Instance para el modelo de núcleos virtuales, consulte [Azure SQL Managed Instance: hardware de proceso en el nivel de servicio de núcleo virtual](../../azure-sql/managed-instance/service-tiers-managed-instance-vcore.md).

Para saber cómo administrar la reserva de capacidad, consulte el artículo sobre la [administración de capacidad reservada](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

- [¿Qué es Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Administración de Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Información sobre el descuento de Azure Reservations](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Información sobre el uso de reservas para suscripciones de pago por uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Información sobre el uso de reservas para la inscripción Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations en el programa del Proveedor de soluciones en la nube (CSP) del Centro de partners](/partner-center/azure-reservations)
