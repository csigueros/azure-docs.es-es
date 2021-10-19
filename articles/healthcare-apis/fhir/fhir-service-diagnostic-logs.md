---
title: 'Visualización y habilitación de la configuración de diagnóstico en el servicio FHIR: API de Azure Healthcare'
description: En este artículo se describe cómo habilitar la configuración de diagnóstico en el servicio FHIR y revisar algunas consultas de ejemplo para los registros de auditoría.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: how-to
ms.date: 10/12/2021
ms.author: zxue
ms.openlocfilehash: 7574d4abf20e07019016796e937cf80c7d915128
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130187755"
---
# <a name="view-and-enable-diagnostic-settings-in-the-fhir-service"></a>Visualización y habilitación de la configuración de diagnóstico en el servicio FHIR

El acceso a los registros de diagnóstico es esencial para cualquier servicio sanitario. Es obligatorio cumplir los requisitos normativos, como la Ley de portabilidad y responsabilidad de seguros de salud (HIPAA). En este artículo, aprenderá a elegir la configuración de los registros de diagnóstico en el servicio FHIR dentro de las API de Azure Healthcare. También revisará algunas consultas de ejemplo para estos registros.

> [!IMPORTANT]
> El servicio Azure Healthcare API se encuentra actualmente en versión preliminar. Los [Términos de](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) uso complementarios para las versiones preliminares de Microsoft Azure incluyen términos legales que se aplican a las características de Azure que están en versión beta, en versión preliminar o que aún no se han publicado en disponibilidad general.

## <a name="steps-to-enable-diagnostic-logs"></a>Pasos para habilitar los registros de diagnóstico

1. Seleccione el servicio FHIR en la Azure Portal.

2. En **Supervisión**, seleccione **Configuración de diagnóstico**.

3. Seleccione **+ Agregar configuración de diagnóstico.**

   [![Captura de pantalla de la página de configuración de diagnóstico de la Azure Portal. ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png) ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png#lightbox)

4. Escriba el nombre **de la configuración de diagnóstico**.
 
   [![Captura de pantalla de los detalles de destino y la casilla para habilitar o deshabilitar los registros de auditoría. ](media/diagnostic-logs/fhir-diagnostic-settings-add.png) ](media/diagnostic-logs/fhir-diagnostic-settings-add.png#lightbox)

5. Seleccione el método que desea usar para acceder a los registros:

   - **Enviar al área de trabajo de Log Analytics** es para enviar registros y métricas a un área de trabajo de Log Analytics en Azure Monitor. Debe crear el área de trabajo de Log Analytics para poder seleccionar esta opción.
   
   - **Archivar en una cuenta de almacenamiento** es para auditoría o inspección manual. La cuenta de almacenamiento que desea usar ya debe crearse. La opción de retención solo se aplica a una cuenta de almacenamiento. La directiva de retención oscila entre 1 y 365 días. Si no desea aplicar ninguna directiva de retención y conservar los datos indefinidamente, establezca la retención (días) en 0.

   - **La transmisión a un centro de** eventos es para la ingesta por parte de un servicio de terceros o una solución de analítica personalizada. Debe crear un espacio de nombres del centro de eventos y una directiva de centro de eventos para poder configurar esta opción.
   
   - **Se debe seleccionar Enviar a la** solución de asociado si ha habilitado una solución de asociado compatible con Azure. Para más información, consulte [Extensión de Azure con soluciones de asociados.](../../partner-solutions/overview.md)

6. Seleccione **AuditLogs**.

7. Seleccione **Guardar**.

> [!NOTE]
> Los primeros registros pueden tardar hasta 15 minutos en aparecer en el área de trabajo de Log Analytics, si seleccionó ese método. Si el servicio FHIR se mueve de un grupo de recursos o una suscripción a otro, actualice la configuración una vez completado el traslado.


## <a name="diagnostic-log-details"></a>Detalles del registro de diagnóstico

En este momento, el servicio FHIR devuelve los campos siguientes en un registro de diagnóstico:

|Nombre del campo|Tipo|Notas|
|----------|----|-----|
|`CallerIdentity` |Dinámica|Un bolsa de propiedades genérico que contiene información de identidad.|
|`CallerIdentityIssuer` | String| Emisor.|
|`CallerIdentityObjectId` | String| Identificador de objeto.|
|`CallerIPAddress` | String| Dirección IP del autor de la llamada.|
|`CorrelationId` | String| Identificador de correlación.|
|`FhirResourceType` | String| Tipo de recurso para el que se ejecutó la operación.|
|`LogCategory` | String| Categoría del registro. (En este artículo, vamos a devolver `AuditLogs` ).|
|`Location` | String| Ubicación del servidor que ha procesado la solicitud. Por ejemplo: `South Central US`.|
|`OperationDuration` | Int| Tiempo que tardó en completar esta solicitud, en segundos.|
|`OperationName` | String| Tipo de operación. Por ejemplo, `update` o `search-type`.|
|`RequestUri` | String| URI de la solicitud.|
|`ResultType` | String| Estado del registro. Los valores disponibles `Started` son `Succeeded` , o `Failed` .|
|`StatusCode` | Int| El código de estado HTTP. Por ejemplo: `200`.|
|`TimeGenerated` | DateTime| Fecha y hora del evento.|
|`Properties` | String| Propiedades de `FhirResourceType` .|
|`SourceSystem` | String| Sistema de origen, que siempre `Azure` está en este caso.|
|`TenantId` | String | Identificador de inquilino.|
|`Type` | String| Tipo de registro, que siempre está `MicrosoftHealthcareApisAuditLog` en este caso.|
|`_ResourceId` | String| Detalles sobre el recurso.|       
        
## <a name="sample-queries"></a>Consultas de ejemplo

Puede usar estas consultas básicas de application Ideas para explorar los datos de registro:

- Ejecute la consulta siguiente para ver los *100 registros más* recientes:

  `Insights
  MicrosoftHealthcareApisAuditLogs
  | limit 100`

- Ejecute la siguiente consulta para agrupar las operaciones por tipo *de recurso de FHIR*:

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | summarize count() by FhirResourceType`

- Ejecute la consulta siguiente para obtener todos los *resultados con errores*:

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | where ResultType == "Failed"`   

## <a name="conclusion"></a>Conclusión

Tener acceso a los registros de diagnóstico es esencial para supervisar un servicio y proporcionar informes de cumplimiento. En este artículo, ha aprendido a habilitar estos registros para el servicio FHIR. 

> [!NOTE]
> Las métricas se agregarán cuando el servicio Azure Healthcare API esté disponible con carácter general.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre el servicio FHIR, consulte:

>[!div class="nextstepaction"]
>[¿Qué es el servicio FHIR?](overview.md)   

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
