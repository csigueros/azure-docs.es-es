---
title: 'Administración de un entorno Gen2: Azure Time Series | Microsoft Docs'
description: Aprenda a administra un entorno de Azure Time Series Insights Gen2.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: a77e437438fa514d20edf82eb861926db7f7b04e
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135795"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Administración de Azure Time Series Insights Gen2

Una vez que haya creado el entorno de Azure Time Series Insights Gen2 con la [CLI de Azure](./how-to-create-environment-using-cli.md) o [Azure Portal](./how-to-create-environment-using-portal.md), puede modificar las directivas de acceso y otros atributos de entorno para satisfacer sus necesidades empresariales.

## <a name="manage-the-environment"></a>Administrar el entorno

Puede administrar su entorno de Azure Time Series Insights Gen2 con [Azure Portal](https://portal.azure.com/). Hay algunas diferencias clave entre un entorno de Gen2 y los entornos de Gen1 S1 o Gen1 S2 que debe tener en cuenta al administrar el entorno a través de Azure Portal:

* El panel **Información general** del entorno Gen2 de Azure Portal tiene los cambios siguientes:

  * La capacidad se quita porque no se aplica a los entornos Gen2.
  * Se agrega la propiedad **Identificador de serie temporal**. Determina cómo se particionan los datos.
  * Se eliminan los conjuntos de datos de referencia.
  * La dirección URL mostrada lo dirige al [Explorador de Azure Time Series Insights](./concepts-ux-panels.md).
  * Su nombre de la cuenta de Azure Storage se agrega a la lista.

* Se quita el panel **Configurar** de Azure Portal porque las unidades de escalado no se aplican a los entornos de Azure Time Series Insights Gen2. Sin embargo, puede usar **Configuración de almacenamiento** para configurar el almacenamiento intermedio recién incorporado.

* Se quita el panel **Datos de referencia** de Azure Portal en Azure Time Series Insights Gen2 porque el concepto de los datos de referencia se ha reemplazado por el [modelo de serie temporal (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Entorno de Azure Time Series Insights Gen2 en Azure Portal":::

## <a name="next-steps"></a>Pasos siguientes

* Revise la lista de [procedimientos recomendados de ingesta de streaming](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices).
* Aprenda cómo [diagnosticar y solucionar problemas](./how-to-diagnose-troubleshoot.md).
