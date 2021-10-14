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
ms.openlocfilehash: 7c93ae2808629bf7b4413e6cb703e2c42863c0f7
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400560"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Instalación de la solución Azure Data Factory Analytics desde Azure Marketplace

Esta solución proporciona un resumen del estado general de Data Factory, con opciones para profundizar en los detalles y solucionar problemas de patrones de comportamiento inesperados. Gracias a las vistas enriquecidas y completas, puede obtener información sobre el procesamiento de claves, por ejemplo:

* Un breve resumen de la canalización, la actividad y las ejecuciones de desencadenadores de factoría de datos.
* Posibilidad de profundizar en las ejecuciones de actividad de factoría de datos por tipo.
* Resumen de los principales errores de actividad y canalización de factoría de datos.

1. Vaya a **Azure Marketplace**, elija el filtro **Analytics** y busque **Azure Data Factory Analytics (versión preliminar)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Captura de pantalla que muestra cómo se va a Azure Marketplace y se selecciona el filtro Analytics y Azure Data Factory Analytics (versión preliminar).":::

1. Revise los detalles sobre **Azure Data Factory Analytics (versión preliminar)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Captura de pantalla que muestra detalles sobre Azure Data Factory Analytics (versión preliminar).":::

1. Seleccione **Crear** y, después, cree o seleccione **Área de trabajo de Log Analytics**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="Captura de pantalla que muestra la creación de una solución.":::

## <a name="monitor-data-factory-metrics"></a>Supervisión de métricas de Data Factory

La instalación de esta solución crea un conjunto predeterminado de vistas dentro de la sección de libros del área de trabajo de Log Analytics elegida. Como resultado, se habilitan las siguientes métricas:

* Ejecuciones de datos: 1) Ejecuciones de canalización por factoría de datos
* Ejecuciones de ADF: 2) Ejecuciones de actividad por factoría de datos
* Ejecuciones de ADF: 3) Ejecuciones de desencadenador por factoría de datos
* Errores de ADF: 1) 10 errores principales de canalización por factoría de datos
* Errores de ADF: 2) 10 ejecuciones de actividad principales por factoría de datos
* Errores de ADF: 3) 10 errores de desencadenador principales por factoría de datos
* Estadísticas de ADF: 1) Ejecuciones de actividad por tipo
* Estadísticas de ADF: 2) Ejecuciones de desencadenador por tipo
* Estadísticas de ADF: 3) Duración máxima de las ejecuciones de canalización

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Captura de pantalla que muestra una ventana con Libros (versión preliminar) y AzureDataFactoryAnalytics resaltado.":::

Puede visualizar las métricas anteriores, examinar las consultas detrás de estas métricas, editar las consultas, crear alertas y realizar otras acciones.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="Captura de pantalla que muestra una representación gráfica de las ejecuciones de canalización por factoría de datos.":::

> [!NOTE]
> Azure Data Factory Analytics (versión preliminar) envía los registros de diagnóstico a tablas de destino _específicas del recurso_. Puede escribir consultas en las tablas siguientes: _ADFPipelineRun_, _ADFTriggerRun_ y _ADFActivityRun_.

## <a name="next-steps"></a>Pasos siguientes

[Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md)
