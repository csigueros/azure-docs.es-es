---
title: 'Exportación de métricas del conector de IoT a través de la configuración de diagnóstico: API de Azure Healthcare'
description: En este artículo se explica cómo exportar las métricas de IoT Connector mediante la configuración de diagnóstico.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: eda593fcbb1f7116d7f6a7a1c29af375ca354d6b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337232"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>Exportación de métricas del conector de IoT mediante la configuración de diagnóstico

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a exportar los registros de métricas del conector de IoT. La característica que permite el registro de métricas es la [**configuración de diagnóstico**](../../azure-monitor/essentials/diagnostic-settings.md) de Azure Portal. 

## <a name="enable-metrics-logging-for-iot-connector"></a>Habilitación del registro de métricas para el conector de IoT
1. Para habilitar el registro de métricas para el conector de IoT, seleccione el servicio Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) en el Azure Portal. 

2. Vaya a **Configuración de diagnóstico**. 

3. Seleccione **+ Agregar configuración de diagnóstico**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="Conector de IoT1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Escriba un nombre en el cuadro de diálogo **Diagnostic setting name** (Nombre de la configuración de diagnóstico).

5. Seleccione el método que desea usar para acceder a los registros de diagnóstico:

    1. **Archivar en una cuenta de almacenamiento** para auditoría o inspección manual. Es necesario que la cuenta de almacenamiento que desea usar ya esté creada.
    2. **Transmitir al centro de eventos** para la ingesta por parte de un servicio de terceros o una solución de analítica personalizada. Deberá crear un espacio de nombres del centro de eventos y una directiva del centro de eventos para poder configurar este paso.
    3. **Transmitir al área de trabajo de Log Analytics** en Azure Monitor. Tendrá que crear el área de trabajo de Log Analytics para poder seleccionar esta opción.

6. Seleccione **Errores, tráfico y latencia para el** conector de IoT.  Seleccione las categorías de métricas adicionales que quiera capturar para el servicio FHIR.

7. Seleccione **Guardar**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="Conector de IoT2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Los primeros registros de métricas pueden tardar hasta 15 minutos en mostrarse en el repositorio de su elección.  
 
Para más información sobre cómo trabajar con los registros de diagnóstico, consulte la [documentación del registro de recursos de Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="conclusion"></a>Conclusión 
Tener acceso a los registros de métricas es fundamental para las tareas de supervisión y solución de problemas.  El conector de IoT permite realizar estas acciones a través de registros de métricas. 

## <a name="next-steps"></a>Pasos siguientes

Consulte las preguntas más frecuentes sobre el conector de IoT.

>[!div class="nextstepaction"]
>[Preguntas más frecuentes sobre el conector de IoT](iot-connector-faqs.md)

(FHIR&#174;) es una marca comercial registrada de HL7 y se usa con el permiso HL7.
