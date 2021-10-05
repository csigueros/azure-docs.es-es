---
title: Pago por adelantado de recursos de proceso con capacidad reservada para Azure Database for MariaDB
description: Pago por adelantado de recursos de proceso de Azure Database for MariaDB con capacidad reservada
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 3a6a4eeac50a7818eea5637b81788b1b11397bce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631957"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Pago por adelantado de recursos de proceso de Azure Database for MariaDB con capacidad reservada

Azure Database for MariaDB ayuda a ahorrar dinero al pagar por adelantado los recursos de proceso en comparación con los precios de pago por uso. Con la capacidad reservada de Azure Database for MariaDB, se realiza un compromiso inicial en los servidores de MariaDB durante un periodo de un año o tres con el fin de obtener un descuento importante en los costos de proceso. Para comprar capacidad reservada de Azure Database for MariaDB, debe especificar la región de Azure, el tipo de implementación, el nivel de rendimiento y el periodo. </br>

No es necesario asignar la reserva a servidores concretos de Azure Database for MariaDB. Una instancia de Azure Database for MariaDB que ya está en ejecución, o las que se han implementado recientemente, obtendrán de forma automática la ventaja de los precios reservados. Al comprar una reserva, se adelanta el pago de los costos de proceso durante un período de uno a tres años. En cuanto se compra una reserva, los costos de proceso de Azure Database for MariaDB que coincidan con los atributos de reserva dejan de pagarse según las tarifas de pago por uso. La reserva no cubre los cargos por software, redes o almacenamiento asociados al servidor de bases de datos de MariaDB. Al final del plazo de reserva, la ventaja en la facturación expira y las instancias de Azure Database for MariaDB se facturan según los precios de pago por uso. Las reservas no se renuevan automáticamente. Para obtener información sobre precios, vea [Oferta de capacidad reservada de Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/). </br>

Puede comprar capacidad reservada de Azure Database for MariaDB en [Azure Portal](https://portal.azure.com/). Pague la reserva [por adelantado o mensualmente](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar capacidad reservada:

* Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con tarifas de pago por uso.
* En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com/). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción.
* En el caso del programa del Proveedor de soluciones en la nube (CSP), los únicos que pueden comprar la capacidad reservada de Azure Database for MariaDB son los agentes de administración o de ventas. </br>

Para información sobre cómo se les cobra a los clientes de empresa y a los de pago por uso las compras de reservas, consulte [Información sobre el uso de reservas de Azure para la inscripción Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) y [Información sobre el uso de reservas de Azure para suscripciones de pago por uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Determinación del tamaño del servidor adecuado antes de la compra

El tamaño de la reserva debe basarse en la cantidad total de proceso que usan las instancias de bases de datos existentes o que se van a implementar pronto en una región específica y con el mismo nivel de rendimiento y generación de hardware.</br>

Por ejemplo, supongamos que se está ejecutando una base de datos MariaDB de propósito general Gen5 de 32 núcleos virtuales y dos bases de datos MariaDB optimizadas para memoria Gen5 de 16 núcleos virtuales. Además, supongamos que planea implementar en el próximo mes un grupo elástico Gen5 de uso general y 32 núcleos virtuales adicionales, y un servidor de bases de datos optimizado para memoria Gen5 de 16 núcleos virtuales. Vamos a suponer que sabe que necesitará estos recursos durante al menos 1 año. En este caso debe comprar una reserva de 1 año de una instancia de Gen5 con 64 núcleos virtuales (2 × 32) para una base de datos única de uso general y otra reserva de 1 año de una instancia de Gen 5 con 48 núcleos virtuales (2 × 16 + 16) para una base de datos única optimizada para memoria.


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Compra de capacidad reservada de Azure Database for MariaDB

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Todos los servicios** > **Reservations**.
3.  Seleccione **Agregar** y, en el panel Comprar reservas, seleccione **Azure Database for MariaDB** para comprar una nueva reserva para las bases de datos de MariaDB.
4.  Rellene todos los campos obligatorios. Las bases de datos existentes o nuevas que coincidan con los atributos seleccionados serán aptas para el descuento en la capacidad reservada. El número real de servidores de Azure Database for MariaDB que obtienen el descuento depende del ámbito y la cantidad seleccionados.


![Información general sobre los precios reservados](media/concepts-reserved-pricing/mariadb-reserved-price.png)


En la siguiente tabla se describen los campos obligatorios.

| Campo | Descripción |
| :------------ | :------- |
| Subscription   | La suscripción usada para pagar la reserva de capacidad reservada de Azure Database for MariaDB. Los costos anticipados por la reserva de capacidad reservada de Azure Database for MariaDB se cobran mediante el método de pago de la suscripción. El tipo de suscripción debe ser Contrato Enterprise (números de oferta: MS-AZR-0017P o MS-AZR-0148P) o un contrato individual con precios de pago por uso (números de oferta: MS-AZR-0003P o MS-AZR-0023P). Para una suscripción Enterprise, los cargos se deducen del pago por adelantado de Azure (antes conocido como saldo de compromiso monetario) de la inscripción, o se cobran como uso por encima del límite. Para una suscripción individual con precios de pago por uso, los cargos se cobran en el método de pago de la factura o la tarjeta de crédito de la suscripción.
| Ámbito | El ámbito de la reserva de núcleos virtuales puede cubrir una suscripción o varias (ámbito compartido). Si selecciona: </br></br> **Compartido**: el descuento por la reserva de núcleos virtuales se aplica a los servidores de Azure Database for MariaDB en ejecución en cualquiera de las suscripciones en el contexto de facturación. Para los clientes Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.</br></br>**Grupo de administración**: se aplica el descuento por reserva al recurso correspondiente de la lista de suscripciones que forman parte del grupo de administración y el ámbito de facturación.</br></br> **Suscripción única**: el descuento por la reserva de núcleos virtuales se aplica a los servidores de Azure Database for MariaDB de esta suscripción. </br></br> **Grupo de recursos único**: el descuento de reserva se aplica a los servidores de Azure Database for MariaDB de la suscripción seleccionada y al grupo de recursos seleccionado de esa suscripción.
| Region | La región de Azure que abarca la reserva de capacidad reservada de Azure Database for MariaDB.
| Tipo de implementación | El tipo de recurso de Azure Database for MariaDB para el que quiere comprar la reserva.
| Nivel de rendimiento | El nivel de servicio de los servidores de Azure Database for MariaDB.
| Término | Un año
| Cantidad | Número de recursos de proceso que se compran dentro de la reserva de capacidad reservada de Azure Database for MariaDB. La cantidad es un número de núcleos virtuales de la región de Azure y el nivel de rendimiento seleccionados que se están reservando y obtendrán el descuento de facturación. Por ejemplo, si ejecuta o planea ejecutar servidores de Azure Database for MariaDB con una capacidad total de proceso de 16 núcleos virtuales Gen5 en la región Este de EE. UU., deberá especificar 16 en la cantidad para maximizar las ventajas de todos los servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelación, intercambio o reembolso de reservas

Puede cancelar, intercambiar o reembolsar reservas con ciertas limitaciones. Para más información, consulte [Autoservicio de intercambios y reembolsos de reservas de Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidad de tamaño del núcleo virtual

La flexibilidad de tamaño del núcleo virtual le ayuda a escalar o reducir verticalmente dentro de un nivel de rendimiento y una región, sin perder los beneficios de la capacidad reservada. 

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

El descuento por la reserva de núcleos virtuales se aplica automáticamente al número de servidores de Azure Database for MariaDB que coincidan con el ámbito y los atributos de la reserva de capacidad reservada de Azure Database for MariaDB. Se puede actualizar el ámbito de la reserva de capacidad reservada de Azure Database for MariaDB a través de Azure Portal, PowerShell, la CLI o la API. </br></br>
Para obtener información sobre cómo administrar la capacidad reservada de Azure Database for MariaDB, vea la sección Administración de la capacidad reservada de Azure Database for MariaDB.

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

* [¿Qué es Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Administración de Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Información sobre el descuento de Azure Reservations](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Información sobre el uso de reservas para suscripciones de pago por uso](../cost-management-billing/reservations/understand-reservation-charges-mariadb.md)
* [Información sobre el uso de reservas para la inscripción Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations en el programa del Proveedor de soluciones en la nube (CSP) del Centro de partners](/partner-center/azure-reservations)