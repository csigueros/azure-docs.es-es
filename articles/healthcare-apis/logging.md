---
title: Registro de las API de Azure Healthcare
description: En este artículo se explica cómo funciona el registro y cómo habilitar el registro para las API de Azure Healthcare.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: b71e6088a1a60c17ec16c1a5a265cf20fec53777
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787534"
---
# <a name="logging-for-azure-healthcare-apis-preview"></a>Registro de las API de Azure Healthcare (versión preliminar)

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

La plataforma Azure proporciona tres tipos de registros, registros de actividad, registros de recursos y Azure Active Directory registros. Consulte más detalles sobre los [registros de actividad.](../azure-monitor/essentials/platform-logs-overview.md) En este artículo, aprenderá cómo funciona el registro para las API de Azure Healthcare.

## <a name="auditlogs"></a>AuditLogs
Aunque los registros de actividad están disponibles para cada recurso de Azure de Azure Portal, las API de atención sanitaria emiten registros de recursos, que incluyen dos categorías de registros, AuditLogs y DiagnosticLogs.

- AuditLogs proporciona una pista de auditoría para los servicios sanitarios, por ejemplo, la dirección IP del autor de la llamada y la dirección URL del recurso cuando un usuario o una aplicación accede al servicio FHIR. Cada servicio emite las propiedades necesarias y, opcionalmente, implementa propiedades adicionales.
- DiagnosticLogs proporciona información sobre el funcionamiento del servicio, por ejemplo, el nivel de registro (información, advertencia o error) y el mensaje de registro.

Actualmente, las API de atención sanitaria solo admiten AuditLogs para la versión preliminar pública. DiagnosticLogs estará disponible cuando el servicio esté disponible con carácter general.

A continuación se muestra un ejemplo de AuditLog.

```
{
    "time": "2021-08-02 16:01:29Z",
    "resourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/xxx/PROVIDERS/MICROSOFT.HEALTHCAREAPIS/SERVICES/xxx",
    "operationName": "Microsoft.HealthcareApis/services/fhir-R4/search-type",
    "category": "AuditLogs",
    "resultType": "Started",
    "resultSignature": 0,
    "durationMs": 0,
    "callerIpAddress": "::ffff:73.164.17.31",
    "correlationId": "5d04211aaf172d43b83d9eb500464ec5",
    "identity": {
        "claims": {
            "iss": "https://sts.windows.net/xxx/",
            "oid": "xxx"
        }
    },
    "level": "Informational",
    "location": "South Central US",
    "uri": "https://xxx.azurehealthcareapis.com:443/Patient",
    "properties": {
        "fhirResourceType": "Patient"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar las métricas admitidas más recientes para las API de atención sanitaria Azure Monitor [aquí.](../azure-monitor/essentials/metrics-supported.md)

Para más información sobre las métricas y los registros del servicio DICOM, [consulte aquí](./dicom/enable-diagnostic-logging.md).

Para obtener más información sobre IoT Connector registros y métricas, consulte [aquí](./azure-api-for-fhir/iot-metrics-display.md).