---
title: Traslado de métricas de Storage Analytics a métricas de Azure Monitor | Microsoft Docs
description: Obtenga información sobre cómo realizar la transición de métricas de Storage Analytics (métricas clásicas) a métricas de Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 77097d0d59da65acd71b42eef58a3f2aa9b3c85b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587430"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Transición a las métricas de Azure Monitor

El **31 de agosto de 2023**, las métricas de Storage Analytics, también denominadas *métricas clásicas*, se retirarán. Para obtener más información, consulte el [anuncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Si usa métricas clásicas, asegúrese de realizar la transición a las métricas de Azure Monitor antes de esa fecha. En este artículo le ayudamos a realizar la transición.

## <a name="steps-to-complete-the-transition"></a>Pasos para completar la transición

Para realizar la transición a las métricas de Azure Monitor, se recomienda el siguiente enfoque.

1. Obtenga información sobre algunas de las [diferencias clave](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) entre las métricas clásicas y las métricas de Azure Monitor.

2. Compile una lista de métricas clásicas que use actualmente.

3. Identifique [qué métricas de Azure Monitor](#metrics-mapping-between-old-metrics-and-new-metrics) proporcionan los mismos datos que las métricas que usa actualmente.

4. Cree [gráficos](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) o [paneles](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) para ver los datos de métricas.

   > [!NOTE]
   > Las métricas de Azure Monitor están habilitadas de manera predeterminada, por lo que no es necesario hacer nada para empezar a capturar métricas. Sin embargo, debe crear gráficos o paneles para ver las métricas.

5. Si ha creado reglas de alertas basadas en métricas de almacenamiento clásico, [cree reglas de alerta](../../azure-monitor/alerts/alerts-overview.md) basadas en métricas de Azure Monitor.

6. Una vez que haya podido ver todas las métricas de Azure Monitor, puede desactivar el registro clásico.

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Métricas clásicas frente a métricas de Azure Monitor

En esta sección se describen algunas diferencias clave entre estas dos plataformas de métricas.

La principal diferencia radica en cómo se administran las métricas. Las métricas clásicas se administran mediante Azure Storage, mientras que las métricas de Azure Monitor se administran mediante Azure Monitor. Con las métricas clásicas, Azure Storage recopila valores de métricas, los agrega y los almacena en tablas que se encuentran en la cuenta de almacenamiento. Con las métricas en Azure Monitor, Azure Storage envía los datos de métrica al back-end de Azure Monitor. Azure Monitor proporciona una experiencia de supervisión unificada que incluye los datos de Azure Portal, además de la ingesta de datos.

Las métricas clásicas se envían y almacenan en una cuenta de almacenamiento de Azure. Las métricas de Azure Monitor se pueden enviar a varias ubicaciones. Una cuenta de almacenamiento puede ser una de estas ubicaciones, pero no es necesario.

En lo que se refiere a la compatibilidad de las métricas, las métricas clásicas proporcionan métricas de **capacidad** solo para Azure Blob Storage. Las métricas de Azure Monitor proporcionan métricas de capacidad para Blob, Table, File, Queue y Premium Storage. Las métricas clásicas proporcionan métricas de **transacciones** sobre Blob, Table, Azure File y Queue Storage. Las métricas de Azure Monitor agregan Premium Storage a esa lista.

Si la actividad de la cuenta no desencadena una métrica, las métricas clásicas mostrarán el valor cero (0) para esa métrica. Las métricas de Azure Monitor omitirán los datos por completo, lo que genera informes más limpios. Por ejemplo, con las métricas clásicas, si no se detectan errores de tiempo de espera del servidor, el valor de `ServerTimeoutError` de la tabla de métricas se establece en 0. Azure Monitor no devuelve ningún dato cuando se consulta el valor de la métrica `Transactions` con la dimensión `ResponseType` igual a `ServerTimeoutError`.

Para más información sobre las métricas en Azure Monitor, consulte [Métricas en Azure monitor](../../azure-monitor/essentials/data-platform-metrics.md).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Asignación de métricas clásicas frente a métricas de Azure Monitor

 Use estas tablas para identificar qué métricas de Azure Monitor proporcionan los mismos datos que las métricas que usa actualmente.

**Métricas de capacidad**

| Métrica clásica | Métrica en Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` con la dimensión `BlobType` igual a `BlockBlob` o `PageBlob`. |
| `ObjectCount`        | `BlobCount` con la dimensión `BlobType` igual a `BlockBlob` o `PageBlob`. |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> También hay varias métricas de capacidad nuevas que no estaban disponibles como métricas clásicas. Para ver la lista completa, consulte [Métricas](../blobs/monitor-blob-storage-reference.md#metrics).

**Métricas de transacción**

| Métrica clásica | Métrica en Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transacciones con la dimensión `ResponseType` igual a `AuthorizationError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousClientOtherError` | Transacciones con la dimensión `ResponseType` igual a `ClientOtherError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousClientTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ClientTimeoutError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousNetworkError` | Transacciones con la dimensión `ResponseType` igual a `NetworkError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousServerOtherError` | Transacciones con la dimensión `ResponseType` igual a `ServerOtherError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousServerTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ServerTimeoutError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousSuccess` | Transacciones con la dimensión `ResponseType` igual a `Success` y la dimensión `Authentication` igual a `Anonymous`. |
| `AnonymousThrottlingError` | Transacciones con la dimensión `ResponseType` igual a `ClientThrottlingError` o `ServerBusyError` y la dimensión `Authentication` igual a `Anonymous`. |
| `AuthorizationError` | Transacciones con la dimensión `ResponseType` igual a `AuthorizationError`. |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transacciones con la dimensión `ResponseType` igual a `ClientOtherError`. |
| `ClientTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ClientTimeoutError`. |
| `NetworkError` | Transacciones con la dimensión `ResponseType` igual a `NetworkError`. |
| `PercentAuthorizationError` | Transacciones con la dimensión `ResponseType` igual a `AuthorizationError`. |
| `PercentClientOtherError` | Transacciones con la dimensión `ResponseType` igual a `ClientOtherError`. |
| `PercentNetworkError` | Transacciones con la dimensión `ResponseType` igual a `NetworkError`. |
| `PercentServerOtherError` | Transacciones con la dimensión `ResponseType` igual a `ServerOtherError`. |
| `PercentSuccess` | Transacciones con la dimensión `ResponseType` igual a `Success`. |
| `PercentThrottlingError` | Transacciones con la dimensión `ResponseType` igual a `ClientThrottlingError` o `ServerBusyError`. |
| `PercentTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ServerTimeoutError` o `ResponseType` igual a `ClientTimeoutError`. |
| `SASAuthorizationError` | Transacciones con la dimensión `ResponseType` igual a `AuthorizationError` y la dimensión `Authentication` igual a `SAS`. |
| `SASClientOtherError` | Transacciones con la dimensión `ResponseType` igual a `ClientOtherError` y la dimensión `Authentication` igual a `SAS`. |
| `SASClientTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ClientTimeoutError` y la dimensión `Authentication` igual a `SAS`. |
| `SASNetworkError` | Transacciones con la dimensión `ResponseType` igual a `NetworkError` y la dimensión `Authentication` igual a `SAS`. |
| `SASServerOtherError` | Transacciones con la dimensión `ResponseType` igual a `ServerOtherError` y la dimensión `Authentication` igual a `SAS`. |
| `SASServerTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ServerTimeoutError` y la dimensión `Authentication` igual a `SAS`. |
| `SASSuccess` | Transacciones con la dimensión `ResponseType` igual a `Success` y la dimensión `Authentication` igual a `SAS`. |
| `SASThrottlingError` | Transacciones con la dimensión `ResponseType` igual a `ClientThrottlingError` o `ServerBusyError` y la dimensión `Authentication` igual a `SAS`. |
| `ServerOtherError` | Transacciones con la dimensión `ResponseType` igual a `ServerOtherError`. |
| `ServerTimeoutError` | Transacciones con la dimensión `ResponseType` igual a `ServerTimeoutError`. |
| `Success` | Transacciones con la dimensión `ResponseType` igual a `Success`. |
| `ThrottlingError` | `Transactions` con la dimensión `ResponseType` igual a `ClientThrottlingError` o `ServerBusyError`.|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Pasos siguientes

- [Azure Monitor](../../azure-monitor/overview.md)
