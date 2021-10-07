---
title: 'Precio de procesos reservados en Azure Database for PostgreSQL: servidor único'
description: Pago por adelantado de recursos de proceso de Azure Database for PostgreSQL con capacidad reservada
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 958663ffc0b05b55d837ae565ee48ee03a54b0ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674744"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Pago por adelantado de recursos de proceso servidor único de Azure Database for PostgreSQL con capacidad reservada

Ahora Azure Database for PostgreSQL le ayuda a ahorrar mediante el pago por adelantado de los recursos de proceso en comparación con los precios de pago por uso. Con la capacidad reservada de Azure Database for PostgreSQL, se realiza un compromiso inicial en el servidor de PostgreSQL durante un periodo de un año o tres años con el fin de obtener un descuento importante en los costos de proceso. Para comprar capacidad reservada de Azure Database for PostgreSQL, debe especificar la región de Azure, el tipo de implementación, el nivel de rendimiento y el periodo. </br>

No es necesario asignar la reserva a servidores concretos de Azure Database for PostgreSQL. Una instancia de Azure Database for PostgreSQL que ya está en ejecución (o las que se han implementado recientemente) obtendrá de forma automática la ventaja de los precios reservados. Al comprar una reserva, se adelanta el pago de los costos de proceso durante un período de uno a tres años. En cuanto se compra una reserva, los costos de proceso de Azure Database for PostgreSQL que coincidan con los atributos de reserva dejan de pagarse según las tarifas de pago por uso. La reserva no cubre los cargos por software, redes o almacenamiento asociados al servidor de bases de datos de PostgreSQL. Al final del plazo de reserva, la ventaja en la facturación expira y las instancias de Azure Database for PostgreSQL se facturan según los precios de pago por uso. Las reservas no se renuevan automáticamente. Para obtener información sobre precios, vea [Oferta de capacidad reservada de Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Los precios de la capacidad reservada están disponibles para Azure Database for PostgreSQL tanto en las opciones de implementación [de un solo servidor](./overview.md#azure-database-for-postgresql---single-server) y de [Hyperscale Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus). Para obtener información sobre los precios de la instancia reservada en Hyperscale (Citus), consulte [esta página](concepts-hyperscale-reserved-pricing.md).

Puede comprar capacidad reservada de Azure Database for PostgreSQL en [Azure Portal](https://portal.azure.com/). Pague la reserva [por adelantado o mensualmente](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar capacidad reservada:

* Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con tarifas de pago por uso.
* En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com/). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción.
* En el caso del programa Proveedor de soluciones en la nube (CSP), los únicos que pueden comprar la capacidad reservada de Azure Database for PostgreSQL son los agentes de administración o de ventas. </br>

Para información sobre cómo se les cobra a los clientes de empresa y a los de pago por uso las compras de reservas, consulte [Información sobre el uso de reservas de Azure para la inscripción Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) y [Información sobre el uso de reservas de Azure para suscripciones de pago por uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Determinación del tamaño del servidor adecuado antes de la compra

El tamaño de la reserva se debe basar en la cantidad total de proceso que van a usar los servidores existentes o que se van a implementar pronto en una región específica y con el mismo nivel de rendimiento y generación de hardware.</br>

Por ejemplo, imagine que ejecuta una base de datos PostgreSQL de propósito general Gen5 de 32 núcleos virtuales y dos bases de datos PostgreSQL optimizadas para memoria Gen5 de 16 núcleos virtuales. Además, supongamos que planea implementar en el próximo mes un servidor de bases de datos Gen5 de uso general y 8 núcleos virtuales adicional, y un servidor de bases de datos optimizado para memoria Gen5 de 32 núcleos virtuales. Vamos a suponer que sabe que necesitará estos recursos durante al menos un año. En este caso debe comprar una reserva de un año de una instancia de Gen5 con 40 núcleos virtuales (32 + 8) para una base de datos única de uso general y otra reserva de un año de una instancia de Gen5 con 64 núcleos virtuales (2 x 16 + 32) para una base de datos única optimizada para memoria


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Compra de capacidad reservada de Azure Database for PostgreSQL

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Todos los servicios** > **Reservations**.
3. Seleccione **Agregar** y, en el panel Comprar reservas, seleccione **Azure Database for PostgreSQL** para comprar una nueva reserva para las bases de datos de PostgreSQL.
4. Rellene todos los campos obligatorios. Las bases de datos existentes o nuevas que coincidan con los atributos seleccionados serán aptas para el descuento en la capacidad reservada. El número real de servidores de Azure Database for PostgreSQL que obtienen el descuento depende del ámbito y la cantidad seleccionados.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Información general sobre los precios reservados":::


En la siguiente tabla se describen los campos obligatorios.

| Campo | Descripción |
| :------------ | :------- |
| Subscription   | La suscripción usada para pagar la reserva de capacidad reservada de Azure Database for PostgreSQL. Los costos anticipados por la reserva de capacidad reservada de Azure Database for PostgreSQL se cobran mediante el método de pago de la suscripción. El tipo de suscripción debe ser Contrato Enterprise (números de oferta: MS-AZR-0017P o MS-AZR-0148P) o un contrato individual con precios de pago por uso (números de oferta: MS-AZR-0003P o MS-AZR-0023P). Para una suscripción Enterprise, los cargos se deducen del pago por adelantado de Azure (antes conocido como saldo de compromiso monetario) de la inscripción, o se cobran como uso por encima del límite. Para una suscripción individual con precios de pago por uso, los cargos se cobran en el método de pago de la factura o la tarjeta de crédito de la suscripción.
| Ámbito | El ámbito de la reserva de núcleos virtuales puede cubrir una suscripción o varias (ámbito compartido). Si selecciona: </br></br> **Compartido**, el descuento por la reserva de núcleos virtuales se aplica a los servidores de Azure Database for PostgreSQL en ejecución en cualquiera de las suscripciones en el contexto de facturación. Para los clientes Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.</br></br>**Grupo de administración**: el descuento por reserva se aplica a las instancias de Azure Database for PostgreSQL que se ejecutan en cualquier suscripción que forme parte tanto del grupo de administración como del ámbito de facturación.</br></br> **Suscripción única**, el descuento por la reserva de núcleos virtuales se aplica a los servidores de Azure Database for PostgreSQL de esta suscripción. </br></br> **Grupo de recursos único**, el descuento de reserva se aplica a los servidores de Azure Database for PostgreSQL de la suscripción seleccionada y al grupo de recursos seleccionado de esa suscripción.
| Region | La región de Azure que abarca la reserva de capacidad reservada de Azure Database for PostgreSQL.
| Tipo de implementación | El tipo de recurso de Azure Database for PostgreSQL para el que quiere comprar la reserva.
| Nivel de rendimiento | El nivel de servicio de los servidores de Azure Database for PostgreSQL.
| Término | Un año
| Cantidad | Número de recursos de proceso que se compran dentro de la reserva de capacidad reservada de Azure Database for PostgreSQL. La cantidad es un número de núcleos virtuales de la región de Azure y el nivel de rendimiento seleccionados que se están reservando y obtendrán el descuento de facturación. Por ejemplo, si ejecuta o planea ejecutar servidores de Azure Database for PostgreSQL con la capacidad total de proceso de 16 núcleos virtuales Gen5 en la región Este de EE. UU., tendrá que especificar la cantidad como 16 para maximizar las ventajas de todos los servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelación, intercambio o reembolso de reservas

Puede cancelar, intercambiar o reembolsar reservas con ciertas limitaciones. Para más información, consulte [Autoservicio de intercambios y reembolsos de reservas de Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidad de tamaño del núcleo virtual

La flexibilidad de tamaño del núcleo virtual le ayuda a escalar o reducir verticalmente dentro de un nivel de rendimiento y una región, sin perder los beneficios de la capacidad reservada. Si escala a núcleos virtuales superiores a su capacidad reservada, se le facturará el exceso de núcleos virtuales con precios de pago por uso.


## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

El descuento por la reserva de núcleos virtuales se aplica automáticamente al número de servidores de Azure Database for PostgreSQL que coincidan con el ámbito y los atributos de la reserva de capacidad reservada de Azure Database for PostgreSQL. Se puede actualizar el ámbito de la reserva de capacidad reservada de Azure Database for PostgreSQL a través de Azure Portal, PowerShell, la CLI o la API.

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

* [¿Qué es Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Administración de Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Información sobre el descuento de Azure Reservations](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Información sobre el uso de reservas para suscripciones de pago por uso](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Información sobre el uso de reservas para la inscripción Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations en el programa del Proveedor de soluciones en la nube (CSP) del Centro de partners](/partner-center/azure-reservations)