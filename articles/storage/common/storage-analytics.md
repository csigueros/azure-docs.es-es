---
title: Uso del análisis de Azure Storage para recopilar datos de métricas y registros | Microsoft Docs
description: Storage Analytics permite realizar un seguimiento de los datos de métricas para todos los servicios de almacenamiento y recopilar registros de Blob Storage, Queue Storage y Table Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 3ccd5cf9ebc1c5183571dcdfbe3beeabe1b28a63
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448123"
---
# <a name="storage-analytics"></a>Storage Analytics

El análisis de Azure Storage realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento.

Para utilizar Storage Analytics, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde [Azure Portal](https://portal.azure.com). Para saber más, vea [Supervisión de una cuenta de almacenamiento en Azure Portal](./manage-storage-analytics-logs.md). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones [Set Blob Service Properties](/rest/api/storageservices/set-blob-service-properties), [Set Queue Service Properties](/rest/api/storageservices/set-queue-service-properties), [Set Table Service Properties](/rest/api/storageservices/set-table-service-properties) y [Set File Service Properties](/rest/api/storageservices/Get-File-Service-Properties) para habilitar Storage Analytics para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API de Blob service y de Table service.

Storage Analytics tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Para más información sobre los límites de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento para cuentas de almacenamiento estándar](scalability-targets-standard-account.md).

Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con Azure Storage, consulte [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Facturación para Storage Analytics

Todos los datos de métricas los escriben los servicios de una cuenta de almacenamiento. Como resultado, cada una de las operaciones de escritura realizadas por Storage Analytics es facturable. También lo es la cantidad de almacenamiento utilizado por los datos de métricas.

Son facturables las acciones siguientes realizadas por Storage Analytics:

* Solicitudes para crear blobs para el registro
* Solicitudes para crear entidades de tabla para las métricas

Si ha configurado una directiva de retención de datos, puede reducir el gasto mediante la eliminación de datos antiguos de registro y métricas. Para obtener más información acerca de las directivas de retención, consulte [Establecer una directiva de retención de datos de Storage Analytics](/rest/api/storageservices/Setting-a-Storage-Analytics-Data-Retention-Policy).

### <a name="understanding-billable-requests"></a>Descripción de las solicitudes facturables

Las solicitudes realizadas al servicio de almacenamiento de una cuenta son facturables o no facturables. Storage Analytics registra cada solicitud realizada a un servicio, incluyendo un mensaje de estado que indica cómo se administró la solicitud. De igual forma, Storage Analytics guarda las métricas para un servicio y para las operaciones de la API de dicho servicio, incluidos los porcentajes y el recuento de algunos mensajes de estado. Todas estas características pueden ayudarle a analizar las solicitudes facturables, a llevar a cabo mejoras en la aplicación y a diagnosticar problemas en las solicitudes a los servicios. Para obtener más información sobre la facturación, consulte [Descripción de la facturación de Azure Storage: ancho de banda, transacciones y capacidad](/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

Al examinar los datos de Storage Analytics, puede usar las tablas del tema [Operaciones y mensajes de estado registrados por Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) para determinar qué solicitudes son facturables. De esta manera, podrá comparar los datos de métricas y de registro con los mensajes de estado para ver si se le cobró por una solicitud determinada. También puede usar las tablas del tema anterior para investigar la disponibilidad de un servicio de almacenamiento o de una operación de API determinada.

## <a name="next-steps"></a>Pasos siguientes
* [Supervisión de una cuenta de almacenamiento en Azure Portal](./manage-storage-analytics-logs.md)
* [Métricas de Storage Analytics](storage-analytics-metrics.md)
* [Registro de Storage Analytics](storage-analytics-logging.md)