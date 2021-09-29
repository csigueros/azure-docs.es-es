---
title: Análisis de cargos inesperados de Azure
description: Obtenga información sobre cómo analizar los cargos inesperados de su suscripción de Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: afdbcd6dd0e00f6829a82c136ba495a7097f5bb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662027"
---
# <a name="analyze-unexpected-charges"></a>Análisis de cargos inesperados

Seguramente la infraestructura de recursos en la nube que haya creado para su organización sea compleja. Muchos tipos de recursos de Azure pueden tener distintos tipos de cargos. Los recursos de Azure pueden ser propiedad de distintos equipos de la organización y pueden tener diferentes tipos de modelos de facturación que se aplican a varios recursos. Para entender mejor los cargos, comience el análisis con una o varias de las estrategias de las siguientes secciones.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Revisión de la factura del recurso responsable del cargo

La forma de adquirir los servicios de Azure le ayuda a determinar la metodología y las herramientas que tiene a su disposición a la hora de identificar el recurso asociado a un cargo. Para determinar qué metodología es aplicable, primero [determine el tipo de oferta de Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). A continuación, identifique la categoría de cliente en la lista de [ofertas de Azure admitidas](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

En los artículos siguientes se proporcionan pasos detallados que explican cómo revisar la factura en función del tipo de cliente. En cada artículo hay instrucciones sobre cómo descargar un archivo CSV que contiene detalles de uso y de costos de un período de facturación determinado.

- [Proceso de revisión de factura de pago por uso](review-individual-bill.md#charges)
- [Proceso de revisión de factura para el contrato Enterprise](review-enterprise-agreement-bill.md)
- [Proceso de revisión para Contrato de cliente de Microsoft](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Proceso de revisión para Microsoft Partner Agreement](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Los cargos de la factura de Azure se suman para el mes por _medidor_. Los medidores se usan para realizar un seguimiento del uso de un recurso a lo largo del tiempo y para calcular la factura. Cuando se crea un único recurso de Azure, como una máquina virtual, se crean una o varias instancias de medidores para el recurso.

Filtre el archivo CSV de uso basándose en el parámetro _MeterName_ que se muestra en la factura que desea analizar para ver todos los elementos de línea que se aplican al medidor. El parámetro _InstanceID_ para el elemento de línea corresponde al recurso de Azure que generó el cargo.

Cuando haya identificado el recurso en cuestión, puede usar el análisis de costos en Azure Cost Management para analizar aún más los costos relacionados con el recurso. Para más información sobre el uso del análisis de costos, consulte [Inicio del análisis de costos](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Revisión de los cargos facturados en Análisis de costos

Para ver los detalles de la factura en Azure Portal, vaya a Análisis de costos para el ámbito asociado a la factura que está analizando. Cambie a la vista **Detalles de la factura**. Los detalles de la factura muestran los cargos según se muestran en la factura.

[![Ejemplo que muestra los detalles de la factura](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Al ver los detalles de la factura, puede identificar el servicio que tiene costos inesperados y determinar qué recursos se asocian directamente con el recurso en Análisis de costos. Por ejemplo, si desea analizar los cargos del servicio Virtual Machines, vaya a la vista **Costo acumulado**. A continuación, establezca la granularidad en **Diario** y filtre los cargos **Nombre de servicio: Máquinas virtuales** y agrupe los cargos por **Recurso**.

[![Ejemplo que muestra los costos acumulados de las máquinas virtuales](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identificación de picos en el costo a lo largo del tiempo

En ocasiones, es posible que no sepa qué costos recientes produjeron cambios en los cargos facturados. Para saber qué ha cambiado, puede usar el análisis de costos para [ver un desglose diario o mensual de los costos a lo largo del tiempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Después de crear la vista, agrupe los cargos por **servicio** o **recurso** para identificar los cambios. También puede cambiar la vista a un gráfico de **Línea** para visualizar mejor los datos.

![Ejemplo que muestra costos a lo largo del tiempo en el análisis de costos](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Determinación de los precios de los recursos y el modelo de facturación

Un único recurso puede acumular cargos en varios productos y servicios de Azure. Vea la página [Precios de Azure por producto](https://azure.microsoft.com/pricing/#product-pricing) para obtener más información sobre los precios de cada servicio de Azure. Por ejemplo, una única máquina virtual creada en Azure puede tener los siguientes medidores creados para realizar el seguimiento de su uso. Cada uno de ellos puede tener distintos precios.

- Horas de proceso
- Horas de dirección IP
- Transferencia de datos de entrada
- Transferencia de datos de salida
- Disco administrado Estándar
- Operaciones de disco administrado estándar
- E/S estándar: disco
- E/S estándar: lectura de blobs en bloques
- E/S estándar: escritura de blobs en bloques
- E/S estándar: Eliminación de blob en bloques

Cuando se crea la máquina virtual, cada medidor comienza a emitir registros de uso. El uso y el precio del medidor se registran en el sistema de medición de Azure. Puede ver los medidores que se usaron para calcular la factura en el archivo CSV de uso.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Búsqueda de las personas responsables del recurso y su participación

A menudo, el equipo responsable de un recurso determinado conocerá los cambios que se han realizado para el recurso. Es útil contar con estas personas para identificar qué cargos pueden aparecer. Por ejemplo, el equipo propietario puede haber creado recientemente el recurso, actualizado su SKU (lo que cambia la tasa de recursos) o aumentado la carga en el recurso debido a cambios en el código. Siga leyendo las siguientes secciones para conocer más técnicas para determinar quién es el propietario de un recurso.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Análisis de los registros de auditoría para el recurso

Si tiene permisos para ver un recurso, debe poder acceder a sus registros de auditoría. Revise los registros para buscar el usuario responsable de los cambios más recientes en un recurso. Para más información, consulte [Visualización y recuperación de eventos del registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Análisis de los permisos de usuario en el ámbito principal del recurso

Las personas con acceso de escritura a una suscripción o grupo de recursos suelen tener información sobre los recursos que se han creado. Deben ser capaces de explicar el propósito de un recurso o dirigirle a la persona que lo sepa. Para identificar a las personas con permisos para un ámbito de suscripción, consulte [Visualización del acceso de un usuario a los recursos de Azure](../../role-based-access-control/check-access.md). Puede usar un proceso similar para los grupos de recursos.

## <a name="get-help-to-identify-charges"></a>Ayuda para identificar cargos

Si ha usado las estrategias anteriores y todavía no entiende por qué ha recibido un cargo o si necesita ayuda con problemas de facturación, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [Optimización de la inversión en la nube con Azure Cost Management](../costs/cost-mgt-best-practices.md).