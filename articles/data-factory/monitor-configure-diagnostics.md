---
title: Configuración de las opciones de diagnóstico y el área de trabajo
description: Aprenda a configurar opciones de diagnóstico y un área de trabajo de Log Analytics para supervisar Azure Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 5673dd881d96dc14922e8a9f9bedc0501d2f5d18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837980"
---
# <a name="configure-diagnostic-settings-and-workspace"></a>Configuración de las opciones de diagnóstico y el área de trabajo

Cree o agregue la configuración de diagnóstico de su factoría de datos.

1. En el portal, vaya a Monitor. Seleccione **Configuración** > **Configuración de diagnóstico**.

1. Seleccione la factoría de datos para la que quiere establecer una configuración de diagnóstico.

1. Si no existe ninguna configuración en la factoría de datos seleccionada, se le pedirá que cree una. Seleccione **Activar diagnósticos**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="Creación de una configuración de diagnóstico si no existe ninguna configuración":::

   Si hay una configuración existente en la factoría de datos, verá una lista de configuraciones ya establecidas en la factoría de datos. Seleccione **Agregar configuración de diagnóstico**.

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="Adición de una configuración de diagnóstico si existe ninguna configuración":::

1. Asigne un nombre de la configuración, seleccione **Enviar a Log Analytics** y, después, seleccione un área de trabajo en **Área de trabajo de Log Analytics**.

    * En el modo _Azure-Diagnostics_, los registros de diagnóstico fluyen a la tabla _AzureDiagnostics_.

    * En el modo _Resource-Specific_, los registros de diagnóstico de Azure Data Factory fluyen a las tablas siguientes:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Puede seleccionar varios registros pertinentes para las cargas de trabajo que se van a enviar a las tablas de Log Analytics. Por ejemplo, si no usa SQL Server Integration Services (SSIS), no es necesario seleccionar ningún registro de SSIS. Si desea registrar las operaciones de inicio, detención y mantenimiento de SSIS Integration Runtime (IR), puede seleccionar registros de SSIS IR. Si invoca ejecuciones de paquetes SSIS a través de T-SQL en SQL Server Management Studio (SSMS), el Agente SQL Server u otras herramientas designadas, puede seleccionar los registros de paquetes SSIS. Si invoca ejecuciones de paquetes SSIS mediante actividades Ejecutar paquete SSIS en las canalizaciones de ADF, puede seleccionar todos los registros.

    * Si selecciona _AllMetrics_, varias métricas de ADF estarán disponibles para que pueda supervisarlas o generar alertas sobre ellas, incluidas las métricas de ejecuciones de actividades, canalizaciones y desencadenadores de ADF, así como las operaciones de SSIS IR y las ejecuciones de paquetes SSIS.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Asignación de un nombre a la configuración y selección de un área de trabajo de Log Analytics":::

    > [!NOTE]
    > Como una tabla de registro de Azure no puede tener más de 500 columnas, se **recomienda encarecidamente** seleccionar el _modo específico de recursos_. Para obtener más información, consulte la [referencia de registros de AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Seleccione **Guardar**.

Transcurridos unos instantes, el nuevo valor de configuración aparece en la lista de configuraciones para esta factoría de datos. Los registros de diagnóstico se transmiten en esa área de trabajo en cuanto se generan nuevos datos de evento. Pueden pasar hasta 15 minutos entre el momento en que se emite un evento y cuando aparece en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

[Configuración de los registros de diagnóstico mediante la API REST de Azure Monitor](monitor-logs-rest.md)
