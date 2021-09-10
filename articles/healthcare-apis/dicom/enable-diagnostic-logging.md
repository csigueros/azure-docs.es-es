---
title: 'Habilitación de registros de diagnóstico en el servicio DICOM: Azure Healthcare APIs'
description: En este artículo se explica cómo habilitar registros de diagnóstico en el servicio DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 51a162ccbad5813a559cf27c26adcd964adeaf07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780443"
---
# <a name="enable-diagnostic-logging-in-the-dicom-service"></a>Habilitación de registros de diagnóstico en el servicio DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo obtendrá información sobre cómo habilitar registros de diagnóstico en el servicio DICOM y podrá revisar consultas de ejemplo de estos registros. El acceso a los registros de diagnóstico es esencial para cualquier servicio del sector sanitario en el que sea imprescindible cumplir requisitos normativos. La característica del servicio DICOM que habilita los registros de diagnóstico es [Configuración de diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md) de Azure Portal. 

## <a name="enable-audit-logs"></a>Habilitación de los registros de auditoría

1. Para habilitar registros de diagnóstico en el servicio DICOM, seleccione el servicio en Azure Portal.
2. Seleccione la hoja **Registro de actividad** y, a continuación, **Configuración de diagnóstico**.

   [ ![Registro de actividad de Azure](media/dicom-activity-log.png) ](media/dicom-activity-log.png#lightbox)

3. Seleccione **+ Agregar configuración de diagnóstico**.

   [ ![Cómo agregar la configuración de diagnóstico](media/add-diagnostic-settings.png) ](media/add-diagnostic-settings.png#lightbox)

4. Indique el **nombre de la configuración de diagnóstico**.

   [ ![Configuración de diagnóstico](media/configure-diagnostic-settings.png) ](media/configure-diagnostic-settings.png#lightbox)

5. Seleccione los detalles **Categoría** y **Destino** para acceder a los registros de diagnóstico.

   * **Envío al área de trabajo de Log Analytics** en Azure Monitor. Tendrá que crear el área de trabajo de Log Analytics para poder seleccionar esta opción. Para obtener más información, consulte [Introducción a los registros de la plataforma Azure](../../azure-monitor/essentials/platform-logs-overview.md).
   * **Archivar en una cuenta de almacenamiento** para auditoría o inspección manual. Es necesario que la cuenta de almacenamiento que desea usar ya esté creada.
   * **Transmisión a un centro de eventos**, para la ingesta mediante un servicio de terceros o una solución de análisis personalizada. Tendrá que crear un espacio de nombres y una directiva de centro de eventos para poder configurar este paso.
   * **Envío a una solución de partner**, esto es, con la que trabaje como organización asociada en Azure. Para obtener información sobre posibles integraciones de partners, consulte la [documentación de soluciones de partner de Azure](../../partner-solutions/overview.md).

     Para obtener información sobre las métricas admitidas, consulte [Métricas admitidas con Azure Monitor](.././../azure-monitor/essentials/metrics-supported.md).

6. Seleccione **Guardar**.


   > [!Note] 
   > Los primeros registros pueden tardar hasta quince minutos en mostrarse en Log Analytics. Además, si el servicio DICOM se mueve de un grupo de recursos a otro o de una suscripción a otra, deberá actualizar la configuración una vez completado el traslado. 
 
   Para obtener información sobre cómo trabajar con registros de diagnóstico, consulte la [documentación de registros de recursos de Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="audit-log-details"></a>Detalles del registro de auditoría

El servicio DICOM devuelve los siguientes campos en el registro de auditoría: 

|Nombre del campo  |Tipo  |Notas  |
|---------|---------|---------|
|correlationId|String|Id. de correlación
|category|String|Categoría de registro (actualmente tenemos "AuditLogs"). 
|operationName|String|Describe el tipo de operación (por ejemplo, Recuperar, Almacenar, Consulta, etc.). 
|time|DateTime|Fecha y hora del evento. 
|resourceId|String| Ruta de acceso de Azure al recurso.
|identidad|Dinámica|Bolsa de propiedades genérica que contiene información de identidad (actualmente no se aplica a DICOM).
|callerIpAddress|String|Dirección IP del autor de la llamada.
|Location|String|Ubicación del servidor que ha procesado la solicitud.
|uri|String|URI de la solicitud.
|resultType|String| Los valores disponibles actualmente son "Started", "Succeeded" o "Failed".
|resultSignature|Int|Código de estado HTTP (por ejemplo, 200).
|properties|String|Describe las propiedades, como el tipo y el nombre del recurso, el Id. de suscripción, la acción de auditoría, etc.
|type|String|Tipo de registro (siempre MicrosoftHealthcareApisAuditLog en este caso)
|Nivel|String|Nivel de registro ("Informational" o "Error").
|operationVersion|String| Actualmente está vacío. Se utilizará para mostrar la versión de la API.


## <a name="sample-queries"></a>Consultas de ejemplo

A continuación figuran algunas consultas básicas de Application Insights que puede utilizar para explorar sus datos de registro.

Ejecute la siguiente consulta para ver los **100 registros más recientes**:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Ejecute la siguiente consulta para agrupar las operaciones por **tipo de recurso DICOM**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by DICOMResourceType
```

Ejecute la siguiente consulta para obtener todos los **resultados con errores**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusión

Tener acceso a los registros de diagnóstico es esencial para supervisar un servicio y proporcionar informes de cumplimiento. Con el servicio DICOM puede realizar estas acciones mediante registros de diagnóstico. 

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha obtenido información sobre cómo habilitar registros de auditoría para el servicio DICOM. Para obtener información sobre el registro de actividad de Azure, consulte
 
>[!div class="nextstepaction"]
>[Esquema de eventos del registro de actividad de Azure](.././../azure-monitor/essentials/activity-log-schema.md)
