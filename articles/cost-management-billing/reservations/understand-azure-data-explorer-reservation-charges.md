---
title: Aplicación del descuento por reserva a Azure Data Explorer
description: Aprenda a aplicar el descuento por reserva al medidor de incremento de Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 99f3687fc3b43461abb48b85c48cfb15286b262f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557764"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Aplicación del descuento por reserva a Azure Data Explorer

Después de comprar una capacidad reservada en Azure Data Explorer, el descuento por la reserva se aplica automáticamente a los recursos de Azure Data Explorer que coincidan con los atributos y la cantidad de la reserva. Una reserva incluye los cargos de incremento de Azure Data Explorer. No incluye el proceso, las redes, el almacenamiento ni ningún otro recurso de Azure usado para operar el clúster de Azure Data Explorer. Las reservas para estos recursos deben adquirirse por separado.

## <a name="how-reservation-discount-is-applied"></a>Aplicación del descuento por reserva

Un descuento por reserva es para "*usarlo o perderlo*". Por lo tanto, si no tiene recursos coincidentes para ninguna hora, perderá una cantidad de reserva para esa hora. No se pueden arrastrar las horas reservadas no utilizadas.

Al cerrar un recurso, el descuento por reserva se aplica automáticamente a otro recurso que coincida con el ámbito especificado. Si no se encuentran recursos coincidentes en el ámbito especificado, las horas reservadas se *pierden*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>El descuento por reserva se aplica a los clústeres de Azure Data Explorer

El descuento por reserva se aplica al consumo de incremento de Azure Data Explorer por cada hora. Para aquellos recursos de Azure Data Explorer que no se ejecutan durante una hora completa, el descuento por reserva se aplica automáticamente a otros recursos de Data Explorer que coinciden con los atributos de la reserva. El descuento se puede aplicar a los recursos de Azure Data Explorer que se están ejecutando simultáneamente. Si no tiene recursos de Azure Data Explorer que se ejecuten durante toda una hora y que coincidan con los atributos de reserva, no obtendrá todas las ventajas del descuento por reserva para esa hora.

> [!NOTE]
> * Es **muy recomendable** comprar [capacidad de reserva](../../virtual-machines/prepay-reserved-vm-instances.md) para las máquinas virtuales que se usan para el clúster de Azure Data Explorer con el fin de maximizar el ahorro de capacidad reservado.
> * La compra de la reserva aplicará los descuentos a todas las regiones.

## <a name="examples"></a>Ejemplos

En los ejemplos siguientes se muestra cómo se aplica el descuento por la capacidad reservada de Azure Data Explorer en función del número de unidades de incremento adquiridos y el momento de su ejecución.
Por ejemplo, en el caso del tamaño de clúster de motor: **2 máquinas virtuales D11_v2**, los cargos totales a petición son por cuatro unidades de medidor de incremento de Azure Data Explorer por hora.

**Escenario 1**

Compra una capacidad reservada de Azure Data Explorer para ocho unidades de incremento de Azure Data Explorer. Puede ejecutar un clúster de motor de dos máquinas virtuales D13_v2 con un total de 16 núcleos que cobra por 16 unidades de incremento de Azure Data Explorer las unidades por hora y coincide con el resto de los atributos de la reserva. Se le cobrará el precio de pago por uso de ocho núcleos de uso de proceso de Azure Data Explorer y obtendrá el descuento de reserva durante una hora de uso de la unidad de incremento por ocho núcleos de Azure Data Explorer.

En los demás ejemplos, se supone que la capacidad reservada de Azure Data Explorer que compra es para un clúster de Azure Data Explorer de 16 núcleos y el resto de atributos de la reserva coinciden con el clúster de Azure Data Explorer en ejecución.

**Escenario 2**

Se ejecutan dos clústeres del motor de Azure Data Explorer con ocho núcleos cada uno durante una hora en dos regiones diferentes. El descuento de la reserva de 16 núcleos se aplica a ambos clústeres y a las 16 unidades de las unidades de incremento de Azure Data Explorer que consumen.

**Escenario 3**

Se ejecuta el clúster del motor de Azure Data Explorer de 16 núcleos de 1 pm a 1:30 pm. Se ejecuta otro clúster del motor de Azure Data Explorer de 16 núcleos de 1:30 pm a 2 pm. Ambas están cubiertas por el descuento en la reserva.

**Escenario 4**

Se ejecuta el clúster del motor de Azure Data Explorer de 16 núcleos de 1 pm a 1:45 pm. Se ejecuta otro clúster del motor de Azure Data Explorer de 16 núcleos de 1:30 pm a 2 pm. Se le cobra el precio de pago por uso por el solapamiento de 15 minutos. El descuento por la reserva se aplica al uso de incremento de Azure Data Explorer por el resto del tiempo.

Para información sobre la aplicación de Azure Reservations en informes de uso de facturación, consulte el artículo [Información sobre el uso de Azure Reservations](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las reservas de Azure, vea los artículos siguientes:

* [Pago por adelantado de los recursos de proceso de Azure Data Explorer con la capacidad reservada de Azure Data Explorer](/azure/data-explorer/pricing-reserved-capacity)  
* [Qué son las reservas de Azure](save-compute-costs-reservations.md)  
* [Administración de reservas de Azure](manage-reserved-vm-instance.md)  
* [Información sobre el uso de reservas para suscripciones de pago por uso](understand-reserved-instance-usage.md)
* [Información sobre el uso de reservas para la inscripción Enterprise](understand-reserved-instance-usage-ea.md)
* [Información sobre el uso de reservas para suscripciones de CSP](/partner-center/azure-reservations)