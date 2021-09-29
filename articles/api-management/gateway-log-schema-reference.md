---
title: 'Referencia: Registro de recursos de Azure API Management'
description: Referencia del esquema para el registro de recursos GatewayLogs de Azure API Management
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: danlep
ms.openlocfilehash: 0da1a897f9abe55e846006136c040d3602882b5a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676511"
---
# <a name="reference-api-management-resource-log-schema"></a>Referencia: Esquema del registro de recursos de API Management

En este artículo se proporciona referencia del esquema para el registro de recursos GatewayLogs de Azure API Management. Las entradas de registro también incluyen campos en el [esquema común de nivel superior](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Para habilitar la recopilación del registro de recursos en API Management, consulte [Supervisión de las API publicadas](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>Esquema de GatewayLogs

Se registran las siguientes propiedades para cada solicitud de API.

| Propiedad  | Tipo | Descripción |
| ------------- | ------------- | ------------- |
| method | string | Método HTTP de la solicitud entrante |
| url | string | Dirección URL de la solicitud entrante |
| responseCode | integer | Código de estado de la respuesta HTTP enviada a un cliente |
| responseSize | integer | Número de bytes enviados a un cliente durante el procesamiento de solicitudes | 
| caché | string | Estado de participación de la caché de API Management en el procesamiento de la solicitud (acierto, error, ninguno) | 
| apiId | string | Identificador de la entidad de la API de la solicitud actual | 
| operationId | string | Identificador de la entidad de la operación de la solicitud actual | 
| clientProtocol | string | Versión del protocolo HTTP de la solicitud entrante |
| clientTime | integer | Número de milisegundos empleados en la E/S global del cliente (conexión, envío y recepción de bytes) | 
| apiRevision | string | Revisión de API para la solicitud actual | 
| clientTlsVersion| string | Versión de TLS usada por la solicitud de envío de cliente |
| lastError | objeto | En el caso de una solicitud incorrecta, los detalles sobre el último error de procesamiento de la solicitud | 
| backendMethod | string | Método HTTP de la solicitud enviada a un back-end |
| backendUrl | string | Dirección URL de la solicitud enviada a un back-end |
| backendResponseCode | integer | Código de la respuesta HTTP recibida de un back-end |
| backedProtocol | string | Versión del protocolo HTTP de la solicitud enviada a un back-end |
| backendTime | integer | Número de milisegundos empleados en la E/S global de back-end (conexión, envío y recepción de bytes) | 


## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre las API de supervisión en API Management, consulte [Supervisión de las API publicadas](api-management-howto-use-azure-monitor.md)
* Obtenga más información en [Esquema específico de servicio y común para los registros de recursos de Azure](../azure-monitor/essentials/resource-logs-schema.md).

