---
title: Habilitación del registro de diagnóstico en Azure API for FHIR
description: En este artículo se explica cómo habilitar el registro de diagnóstico en Azure API for FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: a884dac90273e98868fed6bfe1cbed23b939d286
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780542"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Habilitación del registro de diagnóstico en Azure API for FHIR

En este artículo aprenderá a habilitar el registro de diagnóstico en Azure API for FHIR y podrá revisar algunas consultas de ejemplo de estos registros. El acceso a los registros de diagnóstico es esencial para cualquier servicio del sector sanitario en el que el cumplimiento de los requisitos normativos (como HIPAA) es imprescindible. La característica de Azure API for FHIR que permite los registros de diagnóstico es [**Configuración de diagnóstico**](../../azure-monitor/essentials/diagnostic-settings.md) de Azure Portal. 

## <a name="view-and-download-fhir-metrics-data"></a>Visualización y descarga de datos de métricas de FHIR

Puede ver las métricas en Supervisión | Métricas del portal. Entre las métricas se incluyen el número de solicitudes, la latencia media, el número de errores, el tamaño de los datos, la RU usada, el número de solicitudes que superaron la capacidad y la disponibilidad (en %). En la captura de pantalla siguiente se muestra el RU usado para un entorno de ejemplo con muy pocas actividades en los últimos 7 días. Puede descargar los datos en formato JSON.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Métricas de Azure API for FHIR desde el portal" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Habilitación de los registros de auditoría
1. Para habilitar el registro de diagnóstico en Azure API for FHIR, seleccione el servicio Azure API for FHIR en Azure Portal. 
2. Vaya a **Configuración de diagnóstico**. 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Agregar la configuración de diagnóstico de Azure FHIR." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Seleccione **+ Agregar configuración de diagnóstico**.

4. Escriba un nombre para la configuración.

5. Seleccione el método que desea usar para acceder a los registros de diagnóstico:

    1. **Archivar en una cuenta de almacenamiento** para auditoría o inspección manual. Es necesario que la cuenta de almacenamiento que desea usar ya esté creada.
    2. **Transmitir a un centro de eventos** para la ingesta en un servicio de terceros o una solución de análisis personalizado. Tendrá que crear un espacio de nombres de centro de eventos y una directiva de centro de eventos para poder configurar este paso.
    3. **Transmitir al área de trabajo de Log Analytics** en Azure Monitor. Tendrá que crear el área de trabajo de Log Analytics para poder seleccionar esta opción.

6. Seleccione **AuditLogs** y/o **AllMetrics**. Las métricas incluyen el nombre del servicio, la disponibilidad, el tamaño de los datos, la latencia total, las solicitudes totales, los errores totales y la marca de tiempo. Puede encontrar más información en las [métricas admitidas](../../azure-monitor/essentials/metrics-supported.md#microsofthealthcareapisservices). 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Configuración de diagnóstico de FHIR de Azure. Seleccione AuditLogs y/o AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Seleccione **Guardar**.


> [!Note] 
> Los primeros registros pueden tardar hasta quince minutos en mostrarse en Log Analytics. Además, si Azure API for FHIR se mueve de un grupo de recursos a otro o de una suscripción a otra, deberá actualizar la configuración una vez completado el traslado. 
 
Para más información sobre cómo trabajar con los registros de diagnóstico, consulte la [documentación del registro de recursos de Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="audit-log-details"></a>Detalles del registro de auditoría
En este momento, el servicio Azure API for FHIR devuelve los campos siguientes en el registro de auditoría: 

|Nombre del campo  |Tipo  |Notas  |
|---------|---------|---------|
|CallerIdentity|Dinámica|Bolsa de propiedades genérica que contiene información de identidad
|CallerIdentityIssuer|String|Emisor 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|Dirección IP del autor de la llamada 
|CorrelationId|String| Id. de correlación
|FhirResourceType|String|El tipo de recurso para el que se ejecutó la operación
|LogCategory|String|Categoría de registro (actualmente se devuelve "AuditLogs" LogCategory)
|Location|String|La ubicación del servidor que procesó la solicitud (por ejemplo, Centro-sur de EE. UU.)
|OperationDuration|Int|El tiempo que se tardó en completar esta solicitud en segundos
|OperationName|String| Describe el tipo de operación (por ejemplo, actualizar o tipo de búsqueda)
|RequestUri|String|URI de solicitud 
|ResultType|String|Los valores disponibles actualmente son **Started**, **Succeeded** o **Failed**
|StatusCode|Int|El código de estado HTTP. (por ejemplo, 200) 
|TimeGenerated|DateTime|Fecha y hora del evento|
|Propiedades|String| Describe las propiedades de fhirResourceType
|SourceSystem|String| Sistema de origen (siempre Azure en este caso)
|TenantId|String|Id. de inquilino
|Tipo|String|Tipo de registro (siempre MicrosoftHealthcareApisAuditLog en este caso)
|_ResourceId|String|Detalles sobre el recurso

## <a name="sample-queries"></a>Consultas de ejemplo

Estas son algunas consultas básicas de Application Insights que puede utilizar para explorar los datos de registro.

Ejecute esta consulta para ver los **cien registros más recientes**:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Ejecute esta consulta para agrupar las operaciones por **tipo de recurso de FHIR**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Ejecute esta consulta para obtener todos los **resultados con errores**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusión 
Tener acceso a los registros de diagnóstico es esencial para supervisar un servicio y proporcionar informes de cumplimiento. Azure API for FHIR le permite realizar estas acciones mediante registros de diagnóstico. 
 
FHIR es la marca registrada de HL7 y se usa con su permiso.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, aprendió a habilitar los registros de auditoría para Azure API for FHIR. A continuación, obtenga información sobre otras valores adicionales que puede configurar en Azure API for FHIR.
 
>[!div class="nextstepaction"]
>[Configuración adicional](azure-api-for-fhir-additional-settings.md)