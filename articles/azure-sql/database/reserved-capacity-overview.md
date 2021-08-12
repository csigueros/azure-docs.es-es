---
title: Ahorro de costos de proceso con capacidad reservada
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Aprenda a comprar capacidad reservada de Azure SQL Database e Instancia administrada de SQL para ahorrar en los costos de proceso.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 10/13/2020
ms.openlocfilehash: 4e4f1ba5d15921f30319b001474351d637a0e338
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413760"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Ahorro de costos para los recursos con capacidad reservada: Azure SQL Database e Instancia administrada de SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Ahorre dinero con Azure SQL Database e Instancia administrada de SQL al comprometerse a una reserva de recursos de proceso en lugar de elegir el pago por uso. Con la capacidad reservada, se compromete a usar Azure SQL Database o Instancia administrada de SQL durante un período de uno a tres años para obtener un descuento considerable en los costos de proceso. Para comprar capacidad reservada, debe especificar la región de Azure, el tipo de implementación, el nivel de servicio y el período.

No es necesario asignar la reserva a una base de datos específica o instancia administrada. Al igualarse las implementaciones existentes en ejecución o las recién implementadas se obtendrá la ventaja automáticamente. Al comprar una reserva, se compromete a usar el servicio por los costos de proceso durante un período de uno a tres años. Tan pronto como se compra una reserva, los costos de proceso que coinciden con los atributos de la reserva dejan de pagarse según las tarifas de pago por uso. 

Una reserva se aplica a las réplicas de proceso principales y secundarias facturables, pero no abarca los cargos de software, redes ni almacenamiento asociados con el servicio. Al final del plazo de reserva, la ventaja en la facturación expira y la base de datos o instancia administrada se factura según los precios de pago por uso. Las reservas no se renuevan de manera automática. Para información sobre precios, consulte el artículo sobre la [oferta de capacidad reservada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Puede comprar la capacidad reservada de timbres en [Azure Portal](https://portal.azure.com). Pague la reserva [por adelantado o mensualmente](../../cost-management-billing/reservations/prepare-buy-reservation.md). Para adquirir capacidad reservada:

- Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con tarifas de pago por uso.
- En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción. Capacidad reservada.

Para obtener más información sobre cómo se les cobra a los clientes de empresa y a los de Pago por uso las compras de reservas, consulte [Información sobre el uso de reservas de Azure para la inscripción Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [Información sobre el uso de reservas de Azure para suscripciones de pago por uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> La compra de capacidad reservada no asigna previamente ni reserva recursos de infraestructura específicos (máquinas virtuales o nodos) para su uso.

## <a name="determine-correct-size-before-purchase"></a>Determinación del tamaño correcto antes de la compra

El tamaño de la reserva debe basarse en la cantidad total de proceso que usan la base de datos o la instancia administrada existentes o que se van a implementar pronto en una región específica y con el mismo nivel de rendimiento y generación de hardware.

Por ejemplo, supongamos que ejecuta un grupo elástico de propósito general Gen5 de 16 núcleos virtuales y dos bases de datos únicas Gen5 de cuatro núcleos virtuales críticas para la empresa. Además, supongamos que planea implementar en el próximo mes un grupo elástico Gen5 de propósito general y 16 núcleos virtuales adicional, y otro grupo elástico Gen5 de 32 núcleos virtuales crítico para la empresa. Y también supongamos que sabe que necesitará estos recursos durante al menos 1 año. En este caso, debe comprar una reserva de un año de una instancia de Gen5 con 32 núcleos virtuales (2 × 16) para una base de datos única y un grupo elástico de uso general y otra reserva de un año de una instancia de Gen5 con 40 núcleos virtuales (2 × 4 + 32) para una base de datos única y un grupo elástico crítico para la empresa.

## <a name="buy-reserved-capacity"></a>Compra de capacidad reservada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Reservations**.
3. Seleccione **Agregar** y, en el panel **Comprar reservas**, seleccione **SQL Database** para comprar una nueva reserva para SQL Database.
4. Rellene todos los campos obligatorios. Las bases de datos de SQL Database y SQL Managed Instance existentes que coincidan con los atributos seleccionados serán aptas para el descuento en la capacidad reservada. El número real de bases de datos o instancias administradas que obtienen el descuento depende del ámbito y la cantidad seleccionados.

    ![Captura de pantalla antes del envío de la compra de la capacidad reservada](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    En la siguiente tabla se describen los campos obligatorios.
    
    | Campo      | Descripción|
    |------------|--------------|
    |Suscripción|Suscripción que se usa para pagar la reserva de capacidad. Los costos anticipados de la reserva se cobran en el método de pago de la suscripción. El tipo de suscripción debe ser un contrato Enterprise (número de la oferta: MS-AZR-0017P o MS-AZR-0148P) o un contrato individual con precio de pago por uso (número de la oferta: MS-AZR-0003P o MS-AZR-0023P). Para una suscripción Enterprise, los cargos se deducen del pago por adelantado de Azure (antes conocido como saldo de compromiso monetario) de la inscripción, o se cobran como uso por encima del límite. Para una suscripción individual con precios de pago por uso, los cargos se cobran en el método de pago de la factura o la tarjeta de crédito de la suscripción.|
    |Ámbito       |El ámbito de la reserva de núcleos virtuales puede cubrir una suscripción o varias (ámbito compartido). Si selecciona: <br/><br/>**Compartido**: el descuento por la reserva de núcleos virtuales se aplica a la base de datos o la instancia administrada en ejecución en cualquiera de las suscripciones en el contexto de facturación. Para los clientes Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.<br/><br/>**Suscripción única**: el descuento por la reserva de núcleos virtuales se aplica a las bases de datos o las instancias administradas de esta suscripción. <br/><br/>**Grupo de recursos único**: el descuento de reserva se aplica a las instancias de bases de datos o las instancias administradas de la suscripción seleccionada y al grupo de recursos seleccionado de esa suscripción.|
    |Region      |Región de Azure que está cubierta por la reserva de capacidad.|
    |Tipo de implementación|El tipo de recurso de SQL para el que desea adquirir la reserva.|
    |Nivel de rendimiento|Nivel de servicio para las bases de datos o las instancias administradas. |
    |Término        |Un año o tres años.|
    |Cantidad    |Número de recursos de proceso que se compran dentro de la reserva de capacidad reservada. La cantidad es un número de núcleos virtuales de la región de Azure y el nivel de rendimiento seleccionados que se están reservando y obtendrán el descuento de facturación. Por ejemplo, si ejecuta o planea ejecutar varias bases de datos con la capacidad total de proceso total de 16 núcleos virtuales Gen5 en la región Este de EE. UU., deberá especificar la cantidad como 16 para maximizar las ventajas para todas las bases de datos. |

1. Revise el costo de la reserva de capacidad reservada en la sección **Costos**.
1. Seleccione **Comprar**.
1. Haga clic en **Ver esta reserva** para conocer el estado de la compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelación, intercambio o reembolso de reservas

Puede cancelar, intercambiar o reembolsar reservas con ciertas limitaciones. Para más información, consulte [Autoservicio de intercambios y reembolsos de reservas de Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidad de tamaño del núcleo virtual

La flexibilidad de tamaño del núcleo virtual le ayuda a escalar o reducir verticalmente dentro de un nivel de rendimiento y una región, sin perder los beneficios de la capacidad reservada. La capacidad reservada también le proporciona la flexibilidad para mover temporalmente las bases de datos de acceso frecuente dentro y fuera de grupos elásticos (dentro de la misma región y el mismo nivel de rendimiento) como parte de sus operaciones normales sin perder las ventajas de la capacidad reservada. Si se mantiene un búfer no aplicado en la reserva, puede administrar de manera eficaz los picos de rendimiento sin sobrepasar el presupuesto.

## <a name="limitation"></a>Limitación

No se pueden reservar bases de datos basadas en DTU (básicas, estándar o prémium) en SQL Database.

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

El descuento por la reserva de núcleos virtuales se aplica automáticamente al número de bases de datos o instancias administradas que coincidan con el ámbito y los atributos de la reserva de capacidad. Puede actualizar el ámbito de la reserva de capacidad mediante [Azure Portal](https://portal.azure.com), PowerShell, la CLI de Azure o la API.

- Para obtener información sobre los niveles de servicio de Azure SQL Database para el modelo de núcleo virtual, consulte [Introducción al modelo de núcleo virtual: Azure SQL Database](service-tiers-sql-database-vcore.md).
- Para obtener información sobre los niveles de servicio de Azure SQL Managed Instance para el modelo de núcleo virtual, consulte [Introducción al modelo de núcleo virtual: Azure SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md).

Para saber cómo administrar la reserva de capacidad, consulte el artículo sobre la [administración de capacidad reservada](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

- [¿Qué es Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Administración de Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Información sobre el descuento de Azure Reservations](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Información sobre el uso de reservas para suscripciones de pago por uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Información sobre el uso de reservas para la inscripción Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations en el programa del Proveedor de soluciones en la nube (CSP) del Centro de partners](/partner-center/azure-reservations)