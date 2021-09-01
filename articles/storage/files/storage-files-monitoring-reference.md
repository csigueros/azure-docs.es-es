---
title: Referencia de datos de supervisión de Azure Files | Microsoft Docs
description: Referencia de registro y métricas para la supervisión de datos de Azure Files.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 5dea90773340bb16b4f3c01e11affb2d4d2851b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723386"
---
# <a name="azure-files-monitoring-data-reference"></a>Referencia de datos de supervisión de Azure Files

Vea [Supervisión de Azure Files](storage-files-monitoring.md) para más información sobre la recopilación y el análisis de datos de supervisión de Azure Files.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |

## <a name="metrics"></a>Métricas

En las tablas siguientes se indican las métricas de plataforma recopiladas para Azure Files. 

### <a name="capacity-metrics"></a>Métricas de capacidad

Los valores de las métricas de capacidad se actualizan diariamente (hasta 24 horas). El intervalo de agregación define el intervalo de tiempo para el que se presentan los valores de las métricas. El intervalo de agregación que se admite en las métricas de capacidad es una hora (PT1H).

Azure Files proporciona las siguientes métricas de capacidad en Azure Monitor.

#### <a name="account-level"></a>Nivel de cuenta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

En esta tabla se muestran las [métricas de Azure Files](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Métrica | Descripción |
| ------------------- | ----------------- |
| FileCapacity | La cantidad de almacenamiento de archivos que utiliza la cuenta de almacenamiento. <br/><br/> Unidad: Bytes <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024 |
| FileCount   | El número de archivos que hay en la cuenta de almacenamiento. <br/><br/> Unidad: Count <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024 |
| FileShareCapacityQuota | Límite superior de la cantidad de almacenamiento que puede usar el servicio Azure Files en bytes. <br/><br/> Unidad: Bytes <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024|
| FileShareCount | El número de recursos compartidos de archivo que hay en la cuenta de almacenamiento. <br/><br/> Unidad: Count <br/> Tipo de agregación: Average <br/> Ejemplo de valor: 1024 |
| FileShareProvisionedIOPS | El número de IOPS aprovisionadas en un recurso compartido de archivos. Esta métrica solo se puede aplicar a un almacenamiento de archivos Premium. <br/><br/> Unidad: CountPerSecond <br/> Tipo de agregación: Average |
| FileShareSnapshotCount | Número de instantáneas presentes en el recurso compartido en el servicio Azure Files de la cuenta de almacenamiento. <br/><br/> Unidad: Count <br/> Tipo de agregación: Average | 
|FileShareSnapshotSize|Cantidad de almacenamiento que usan las instantáneas del servicio Azure Files de la cuenta de almacenamiento. <br/><br/> Unidad: Bytes <br/> Tipo de agregación: Average|

### <a name="transaction-metrics"></a>Métricas de transacciones

Se emiten en todas las solicitudes enviadas a una cuenta de almacenamiento de Azure Storage a Azure Monitor. En caso de que no haya actividad en la cuenta de almacenamiento, no habrá ningún dato en las métricas de transacciones del período. Todas las métricas de transacción están disponibles en el nivel de cuenta y de servicio de Azure Files. El intervalo de agregación define el intervalo de tiempo en el que se presentan los valores de las métricas. Los intervalos de agregación compatible para todas las métricas de transacciones son PT1H y PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensiones de métricas

Azure Files admite las siguientes dimensiones para las métricas en Azure Monitor.

> [!NOTE] 
> La dimensión de recurso compartido de archivos no está disponible para recursos compartidos de archivos estándar (solo para recursos compartidos de archivos prémium). Al usar recursos compartidos de archivos estándar, las métricas proporcionadas son para todos los recursos compartidos de archivos de la cuenta de almacenamiento. Para obtener métricas por recurso compartido para recursos compartidos de archivos estándar, cree un recurso compartido de archivos por cuenta de almacenamiento.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Registros de recursos (versión preliminar)

> [!NOTE]
> Los registros de Azure Storage en Azure Monitor están en versión preliminar pública, además de estar disponibles para pruebas de versión preliminar en todas las regiones de la nube pública. Esta versión preliminar habilita los registros de blobs (incluido Azure Data Lake Storage Gen2), archivos, colas, tablas, cuentas de almacenamiento Premium en cuentas de almacenamiento de uso general v1 y v2. Las cuentas de almacenamiento clásico no son compatibles.

En la tabla siguiente se indican las propiedades de los registros de recursos de Azure Storage cuando se recopilan en registros de Azure Monitor o Azure Storage. Las propiedades describen la operación, el servicio y el tipo de autorización que se ha usado para realizar la operación.

### <a name="fields-that-describe-the-operation"></a>Campos que describen la operación


[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que describen cómo se ha autenticado la operación

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que describen el servicio

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Consulta también

- Vea [Supervisión de Azure Files](storage-files-monitoring-reference.md) para obtener una descripción de la supervisión de Azure Storage.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
