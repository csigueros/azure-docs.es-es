---
title: Instalación de la solución Azure Data Factory Analytics desde Azure Marketplace
description: Obtenga información sobre cómo instalar una solución Azure Data Factory Analytics desde Azure Marketplace.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4ae7015c575dc10c9f8c2ec2b896e75e1a2a2a91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837885"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Instalación de la solución Azure Data Factory Analytics desde Azure Marketplace

Esta solución proporciona un resumen del estado general de Data Factory, con opciones para profundizar en los detalles y solucionar problemas de los patrones de comportamiento inesperados. Con las vistas enriquecidas y completas, puede obtener información sobre el procesamiento de claves, entre lo que se incluye:

* Un breve resumen de la canalización, la actividad y las ejecuciones de desencadenadores de factoría de datos
* Capacidad de profundizar en las ejecuciones de actividad de factoría de datos por tipo
* Resumen de los principales errores de actividad y canalización de factoría de datos

1. Vaya a **Azure Marketplace**, elija el filtro **Analytics** y busque **Azure Data Factory Analytics (versión preliminar)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Vaya a &quot;Azure Marketplace&quot;, escriba &quot;Filtro de Analytics&quot; y seleccione &quot;Azure Data Factory Analytics (versión preliminar)&quot;":::.

1. Detalles sobre **Azure Data Factory Analytics (versión preliminar)**

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Detalles sobre &quot;Azure Data Factory Analytics (versión preliminar)&quot;":::

1. Seleccione **Crear** y, después, cree o seleccione el **Área de trabajo Log Analytics**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="Creación de una solución":::

## <a name="monitor-data-factory-metrics"></a>Supervisión de métricas de Data Factory

La instalación de esta solución crea un conjunto predeterminado de vistas dentro de la sección de libros del área de trabajo de Log Analytics elegida. Como resultado, se habilitan las siguientes métricas:

* Ejecuciones de ADF: 1) Ejecuciones de canalización por Data Factory
* Ejecuciones de ADF: 2) Ejecuciones de actividad por Data Factory
* Ejecuciones de ADF: 3) Ejecuciones de desencadenador por Data Factory
* Errores de ADF: 1) 10 errores de canalización principales por Data Factory
* Errores de ADF: 2) 10 ejecuciones de actividad principales por Data Factory
* Errores de ADF: 3) 10 errores de desencadenador principales por Data Factory
* Estadísticas de ADF: 1) Ejecuciones de actividad por tipo
* Estadísticas de ADF: 2) Ejecuciones de desencadenador por tipo
* Estadísticas de ADF: 3) Duración máxima de las ejecuciones de canalización

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Ventana con &quot;Libros (versión preliminar)&quot; y &quot;AzureDataFactoryAnalytics&quot; resaltados":::

Puede visualizar las métricas anteriores, examinar las consultas detrás de estas métricas, editar las consultas, crear alertas y realizar otras acciones.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="Representación gráfica de las ejecuciones de canalización por factoría de datos":::

> [!NOTE]
> Azure Data Factory Analytics (versión preliminar) envía los registros de diagnóstico a tablas de destino _específicas del recurso_. Puede escribir consultas en las tablas siguientes: _ADFPipelineRun_, _ADFTriggerRun_ y _ADFActivityRun_.

## <a name="next-steps"></a>Pasos siguientes

[Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md)
