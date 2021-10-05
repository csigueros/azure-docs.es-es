---
title: Referencia de datos de supervisión de Azure Table Storage | Microsoft Docs
description: Referencia de registro y métricas para la supervisión de datos en Azure Table Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 452948a86fa8b76e385d169d70b155eb978d50ea
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776925"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Referencia de datos de supervisión de Azure Table Storage

Consulte [Supervisión de Azure Storage](monitor-table-storage.md) para más información sobre la recopilación y el análisis de datos de supervisión de Azure Storage.

## <a name="metrics"></a>Métricas

En las tablas siguientes se indican las métricas de plataforma recopiladas de Azure Storage. 

### <a name="capacity-metrics"></a>Métricas de capacidad

Los valores de las métricas de capacidad se envían a Azure Monitor cada hora. Los valores se actualizan a diario. El intervalo de agregación define el intervalo de tiempo para el que se presentan los valores de las métricas. El intervalo de agregación que se admite en las métricas de capacidad es una hora (PT1H).

Azure Storage proporciona las siguientes métricas de capacidad en Azure Monitor.

#### <a name="account-level"></a>Nivel de cuenta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>Almacenamiento de tablas

En esta tabla se muestran [métricas de almacenamiento de tablas](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices).

| Métrica | Descripción |
| ------------------- | ----------------- |
| TableCapacity | La cantidad de almacenamiento de tablas que utiliza la cuenta de almacenamiento. <br/><br/> Unidad: Bytes <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024 |
| TableCount   | El número de tablas de la cuenta de almacenamiento. <br/><br/> Unidad: Count <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024 |
| TableEntityCount | El número de entidades de tabla que hay en la cuenta de almacenamiento. <br/><br/> Unidad: Count <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024 |

### <a name="transaction-metrics"></a>Métricas de transacciones

Se emiten en todas las solicitudes enviadas a una cuenta de almacenamiento de Azure Storage a Azure Monitor. En caso de que no haya actividad en la cuenta de almacenamiento, no habrá ningún dato en las métricas de transacciones del período. Todas las métricas de transacción están disponibles en el nivel de cuenta y de servicio de Table Storage. El intervalo de agregación define el intervalo de tiempo en el que se presentan los valores de las métricas. Los intervalos de agregación compatible para todas las métricas de transacciones son PT1H y PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensiones de métricas

Azure Storage admite las siguientes dimensiones para las métricas en Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Registros de recursos (versión preliminar)

> [!NOTE]
> Los registros de Azure Storage en Azure Monitor están en versión preliminar pública, además de estar disponibles para pruebas de versión preliminar en todas las regiones de nube pública y de US Government. Esta versión preliminar habilita los registros de blobs (incluido Azure Data Lake Storage Gen2), archivos, colas, tablas, cuentas de almacenamiento Premium en cuentas de almacenamiento de uso general v1 y v2. Las cuentas de almacenamiento clásico no son compatibles.

En la tabla siguiente se indican las propiedades de los registros de recursos de Azure Storage cuando se recopilan en registros de Azure Monitor o Azure Storage. Las propiedades describen la operación, el servicio y el tipo de autorización que se ha usado para realizar la operación.

### <a name="fields-that-describe-the-operation"></a>Campos que describen la operación

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que describen cómo se ha autenticado la operación

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que describen el servicio

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Consulta también

- Vea [Supervisión de Azure Table Storage](monitor-table-storage.md) para ver una descripción de la supervisión de Azure Storage.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).