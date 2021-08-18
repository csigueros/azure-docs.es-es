---
title: Descripción del descuento por reserva para flujos de datos de Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo se aplica un descuento por reserva a los flujos de datos de ADF en ejecución. El descuento se aplica a dichos flujos de datos cada hora.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 6796006bbac8987370bddab979356d5123fab204
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726871"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Procedimiento para aplicar un descuento por reserva a flujo de datos de Azure Data Factory

Después de comprar la capacidad reservada del flujo de datos de ADF, el descuento por reserva se aplica automáticamente a los flujos de datos mediante un entorno de ejecución de integración de Azure que coincide con el tipo de proceso y el recuento de núcleos de la reserva.

## <a name="how-reservation-discount-is-applied"></a>Aplicación del descuento por reserva

Un descuento de reserva significa "*usarlo o perderlo*". Por lo tanto, si no usa ningún recurso de integración de Azure coincidente durante alguna hora, perderá una cantidad de reserva para esa hora. No se pueden arrastrar las horas reservadas no utilizadas.

Cuando deje de usar el entorno de ejecución de integración para los flujos de datos, el descuento por reserva se aplica automáticamente a otro recurso que coincida con el ámbito especificado. Si no se encuentran recursos coincidentes en el ámbito especificado, las horas reservadas se *pierden*.

## <a name="discount-applied-to-adf-data-flows"></a>Descuento aplicado a los flujos de datos de ADF

El descuento de la capacidad reservada del flujo de datos de ADF se aplica a la ejecución de los entornos de ejecución de integración cada hora. La reserva que compra se compara con el tipo de procesos que usan los entornos de ejecución de integración para los flujos de datos. En el caso de los flujos de datos que no se ejecutan durante una hora entera, la reserva se aplica automáticamente a otros flujo de datos que coincidan con los atributos de reserva. El descuento también se puede aplicar a flujos de datos que se ejecuten de forma simultánea. Si no tiene flujos de datos que se ejecuten durante toda la hora y que coincidan con los atributos de reserva, no obtendrá todas las ventajas del descuento por reserva para esa hora.

En los ejemplos siguientes se muestra cómo se aplica el descuento en la capacidad reservada del flujo de datos de ADF en función del número de núcleos adquiridos y su momento de ejecución.

- Escenario 1: Compra una reserva de flujo de datos de ADF de una hora con 80 núcleos de proceso con optimización para memoria. Para ello, escribe 80 como la cantidad para el tipo de proceso con optimización para memoria. Se ejecuta un flujo de datos con un entorno de ejecución de integración de Azure establecido en 144 núcleos optimizados para memoria durante una hora. Se le cobra el precio de pago por uso correspondiente a 64 núcleos de uso de flujo de datos durante una hora. Obtiene el descuento por reserva para una hora de 80 núcleos de uso optimizado para memoria.
- Escenario 2: Compra una reserva de flujo de datos de ADF de una hora con 32 núcleos de proceso de uso general. Para ello, escribe 32 como la cantidad para el tipo de proceso de uso general. Los flujos de datos se depuran durante 1 hora usando 32 núcleos de entorno de ejecución de integración de Azure de proceso general. Se obtiene el descuento por reserva para toda la hora de uso.

Para obtener información sobre la aplicación de Azure Reservations en informes de uso de facturación, consulte el artículo [Información sobre el uso de Azure Reservations](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Reservations, consulte el siguiente artículo:

- [¿Qué es Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)