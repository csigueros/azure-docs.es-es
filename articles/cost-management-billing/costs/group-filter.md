---
title: Opciones de agrupación y filtrado en Azure Cost Management
description: En este artículo, se explica cómo usar las opciones de agrupación y filtrado en Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 150a79c00003d390f75eb32bc386e826702a173b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997145"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Opciones de agrupación y filtrado en el análisis de costos

El análisis de costos tiene muchas opciones de agrupación y filtrado. Este artículo le ayudará a comprender cuándo debe usarlas.

Para ver un vídeo sobre las opciones de agrupación y filtrado, consulte el vídeo [Informes de Cost Management por dimensiones y etiquetas](https://www.youtube.com/watch?v=2Vx7V17zbmk). Para ver otros vídeos, visite el [canal de YouTube de Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Propiedades de agrupación y filtrado

En la tabla siguiente se enumeran algunas de las opciones de agrupación y filtrado más comunes en el análisis de costos, y también se indica cuándo se deben usar.

| Propiedad | Cuándo se usa | Notas |
| --- | --- | --- |
| **Zonas de disponibilidad** | Cuando se desglosan los costos de AWS por zona de disponibilidad. | Solo se aplica a ámbitos de AWS y grupos de administración. Los datos de Azure no incluyen la zona de disponibilidad y se mostrarán como **Sin zona de disponibilidad**. |
| **Período de facturación** | Período en que se desglosan los costos de Pago por uso en el mes en que se facturaron (o se facturarán). | Utilice el **período de facturación** para obtener una representación precisa de los gastos de Pago por uso facturados. Incluya dos días adicionales antes y después del período de facturación si se filtra por un intervalo de fechas personalizado. Limitarse a las fechas exactas del período de facturación no coincidirá con la factura. Mostrará los costos de todas las facturas en el período de facturación. Utilice el **identificador de factura** para filtrar a una factura específica. Solo se aplica a las suscripciones de Pago por uso porque EA y MCA se facturan por meses naturales. Las cuentas de EA/MCA pueden utilizar meses naturales en el selector de fecha o una granularidad mensual para lograr el mismo objetivo. |
| **Tipo de cargo** | Cuando se desglosa el uso, la compra, el reembolso y los costos de la reserva. | Las compras de reservas y los reembolsos solo están disponibles cuando se usan costos reales, no cuando se usan costos amortizados. Los costos de las reservas sin usar solo están disponibles cuando se examinan los costos amortizados. |
| **Departamento** | Cuando se desglosan los costos por departamento de Contrato Enterprise. | Solo está disponible para los grupos de Contrato Enterprise y de administración. Las suscripciones de Pago por uso no tienen un departamento y se mostrarán como **Sin departamento** o **sin asignar**. |
| **Cuenta de inscripción** | Cuando se desglosan los costos por propietario de cuenta de EA. | Esta opción solo está disponible para los departamentos, grupos de administración y cuentas de facturación de Contrato Enterprise. Las suscripciones de Pago por uso no tienen cuentas de inscripción de Contrato Enterprise y se mostrarán como **Sin cuenta de inscripción** o **sin asignar**. |
| **Frecuencia** | Cuando se desglosan los costos basados en uso, puntuales y periódficos. | |
| **Identificador de la factura** | Cuando se desglosan los costos factura facturada. | Los cargos no facturados no tienen un identificador de factura todavía, y los costos del Contrato Enterprise no incluyen los detalles de la factura y se mostrarán como **Sin identificador de factura**.  |
| **Ubicación** | Cuando se desglosan los costos por ubicación o región del recurso. | Las compras y el uso de Marketplace pueden mostrarse como **sin asignación** o **Sin ubicación de recursos**. |
| **Medidor** | Cuando se desglosan los costos por medidor de uso. | Tanto en las compras como en el uso de Marketplace se mostrará como **Sin medidor**. Consulte **Tipo de cargo** para identificar las compras y **Tipo de publicador** para identificar los cargos de Marketplace. |
| **operación** | Cuando se desglosan los costos de AWS por operación. | Solo se aplica a ámbitos de AWS y grupos de administración. Los datos de Azure no incluyen la operación y se mostrarán como **Sin operación**; en su lugar, utilice **Medidor**. |
| **Modelo de precios** | Modelo en que se desglosan los costos por el uso bajo demanda, de reserva o puntual. | Las compras se muestran como **OnDemand**. Si ve **No aplicable**, agrupe por **Reserva** para determinar si el uso es de reserva o a petición y por **Tipo de carga** para identificar las compras.
| **Proveedor** | Desglose los costos por tipo de proveedor: Azure, Microsoft 365, Dynamics 365, AWS, entre otros. | Identificador del producto y la línea de negocio. |
| **Tipo de anunciante** | Desglose los costos de Microsoft, Azure, AWS y Marketplace. | Los valores son **Microsoft** para las cuentas de MCA y **Azure** para EA y las cuentas de pago por uso. |
| **Reserva** | Cuando se desglosan los costos por reserva. | Cualquier uso o compra que no esté asociado a una reserva se mostrará como **Sin reserva**. Agrupe por **Tipo de publicador** para identificar otras compras de Azure, AWS o Marketplace. |
| **Recurso** | Cuando se desglosan los costos por recurso. | Las compras de Marketplace se muestran como **Otras compras de Marketplace**; y las compras de Azure, como los cargos de reservas y soporte técnico, se muestran como **Otras compras de Azure**. Agrupe por **Tipo de publicador**, o úselo como filtro, para identificar otras compras de Azure, AWS o Marketplace. |
| **Grupos de recursos** | Cuando se desglosan los costos por grupo de recursos. | Las compras, los recursos de inquilino que no estén asociados a las suscripciones, los recursos de suscripción que no estén implementados en un grupo de recursos y los recursos clásicos que no tengan un grupo de recursos se mostrarán como **Otras compras de Marketplace**, **Otras compras de Azure**, **Otros recursos de inquilino**, **Otros recursos de suscripción**, **$system** u **Otros cargos**. |
| **Tipo de recurso** | Cuando se desglosan los costos por tipo de recurso. | Las compras y los servicios clásicos no tienen un tipo de recurso Azure Resource Manager y se mostrarán como **otros**, **servicios clásicos** o **Sin tipo de recurso**. |
| **Nombre de servicio** o **Categoría de medidor** | Cuando se desglosa el costo por servicio de Azure. | Las compras y el uso de Marketplace se mostrarán como **Sin nombre de servicio** o **sin asignar**. |
| **Nivel de servicio** o **Subcategoría de medidor** | Cuando se desglosa el costo por subclasificación de medidor de uso de Azure. | Las compras y el uso de Marketplace estarán vacíos o se mostrarán como **sin asignar**. |
| **Suscripción** | Cuando se desglosan los costos por suscripción de Azure y por cuenta vinculada de AWS. | Las compras y los recursos de inquilino pueden mostrarse como **Sin suscripción**. |
| **Tag** | Cuando se desglosan los costos por valores de etiqueta para una clave de etiqueta concreta. | Las compras, los recursos de inquilino que no estén asociados a las suscripciones, los recursos de suscripción que no estén implementados en un grupo de recursos y los recursos clásicos no se pueden etiquetar y mostrarán el mensaje **Etiquetas no disponibles**. Los servicios que no incluyen etiquetas en los datos de uso mostrarán el mensaje **Etiquetas no compatibles**. Los casos restantes en los que no se especifiquen etiquetas para un recurso se mostrarán como **Sin etiquetar**. Obtenga más información sobre la [compatibilidad de etiquetas para cada tipo de recurso](../../azure-resource-manager/management/tag-support.md). |

Para más información acerca de los términos, consulte [Información acerca de los términos usados en el archivo de uso y de cargos de Azure](../understand/understand-usage.md).

## <a name="publisher-type-value-changes"></a>Cambios de valor de PublisherType

En Cost Management, el campo PublisherType indica si los cargos corresponden a productos de Microsoft, Marketplace o AWS (si tiene configurado un [conector Cross Cloud](aws-integration-set-up-configure.md)).

¿Qué está cambiando?

A partir del 14 de octubre de 2021, el campo PublisherType con el valor "Azure" se va a actualizar a "Microsoft" para todos los clientes con un [Contrato de cliente de Microsoft](../understand/review-customer-agreement-bill.md#check-access-to-a-microsoft-customer-agreement). Este cambio se realiza para dar cabida a próximas mejoras consistentes en la admisión de productos de Microsoft distintos de Azure, como Microsoft 365 y Dynamics 365.

Los valores "Marketplace" y "AWS" permanecen sin cambios.

Este cambio no afecta a los clientes con Contrato Enterprise ni a ofertas de pago por uso.

**Impacto y acción**
<a name="impact-action"></a>

En cuanto a los datos de Cost Management que haya descargado antes del 14 de octubre de 2021, debe pensar en los valores del campo PublisherType "Azure" anteriores y "Microsoft" nuevos. Los datos se podrían haber descargado por medio de exportaciones, detalles de uso o desde Cost Management.

Si usa llamadas API de REST de Cost Management + Billing que filtran el campo PublisherType por el valor "Azure", debe abordar el cambio y filtrar por el nuevo valor "Microsoft" después del 14 de octubre de 2021. Después, si realiza llamadas API con un filtro PublisherType = "Azure", no se devuelven datos.

No hay ningún impacto en el análisis de costos ni en los presupuestos porque los cambios se reflejan automáticamente en los filtros. Las vistas guardadas o los presupuestos creados con el filtro Publisher el filtro PublisherType = "Azure" se actualizan automáticamente.

## <a name="next-steps"></a>Pasos siguientes

- [Inicio del análisis de costos](./quick-acm-cost-analysis.md).
