---
title: Obtención de conclusiones mediante el Centro de copias de seguridad
description: Obtenga información sobre cómo analizar las tendencias históricas y sacar conclusiones más profundas sobre las copias de seguridad con el Centro de copias de seguridad.
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 5244ba6edaac3b58550107c2519b90447ff197e0
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177847"
---
# <a name="obtain-insights-using-backup-center"></a>Obtención de conclusiones mediante el Centro de copias de seguridad

Para analizar las tendencias históricas y obtener información más detallada sobre las copias de seguridad, el Centro de copias de seguridad proporciona una interfaz para [Informes de Backup](configure-reports.md), que usa [Registros de Azure Monitor](../azure-monitor/logs/data-platform-logs.md) y [Libros de Azure](../azure-monitor/visualize/workbooks-overview.md). Informes de Backup ofrece las siguientes funcionalidades:

- Asignación y previsión del almacenamiento en la nube consumido.

- Auditoría de copias de seguridad y restauraciones.

- Identificación de las tendencias clave con diferentes niveles de granularidad.

- Obtención de visibilidad y conclusiones sobre las oportunidades de optimización de costos para las copias de seguridad.

## <a name="supported-scenarios"></a>Escenarios admitidos

- Informes de Backup no se admite actualmente para cargas de trabajo de las que se hace una copia de seguridad mediante almacenes de copia de seguridad.

- Consulte la [matriz de compatibilidad](backup-center-support-matrix.md) para obtener una lista detallada de escenarios admitidos y no admitidos.

## <a name="get-started"></a>Introducción

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Configuración de almacenes para enviar datos a un área de trabajo de Log Analytics

[Obtenga información sobre cómo configurar los valores de diagnóstico a gran escala para los almacenes](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Visualización de Informes de Backup en el portal del Centro de copias de seguridad

Al seleccionar el elemento de menú **Informes de Backup** en el Centro de copias de seguridad, se abren los informes. Elija una o varias áreas de trabajo de Log Analytics para ver y analizar la información clave de las copias de seguridad.

![Informes de Backup en el Centro de copias de seguridad](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

A continuación se muestran las vistas disponibles:

1. **Resumen**: use esta pestaña para obtener información general sobre el conjunto de copias de seguridad. [Más información](./configure-reports.md#summary)

2. **Elementos de copia de seguridad**: use esta pestaña para ver información y tendencias sobre el almacenamiento en la nube consumido en el nivel de elemento de Backup. [Más información](./configure-reports.md#backup-items)

3. **Uso**: use esta pestaña para ver los parámetros de facturación principales de las copias de seguridad. [Más información](./configure-reports.md#usage)

4. **Trabajos**: use esta pestaña para ver tendencias de ejecución prolongada de los trabajos, como el número de trabajos con errores por día y las causas principales de los errores de los trabajos. [Más información](./configure-reports.md#jobs)

5. **Directivas**: use esta pestaña para ver información sobre todas las directivas activas, como el número de elementos asociados y el almacenamiento en la nube total consumido por los elementos de los que se ha realizado una copia de seguridad por una directiva determinada. [Más información](./configure-reports.md#policies)

6. **Optimizar**: use esta pestaña para obtener visibilidad sobre las posibles oportunidades de optimización de costos para las copias de seguridad. [Más información](./configure-reports.md#optimize)

7. **Adhesión a la directiva**: use esta pestaña para visualizar si cada instancia de copia de seguridad tiene al menos una copia de seguridad correcta al día. [Más información](./configure-reports.md#policy-adherence)

También puede configurar los mensajes de correo electrónico para estos informes mediante la característica [Informe de correo electrónico](backup-reports-email.md).

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y funcionamiento de las copias de seguridad](backup-center-monitor-operate.md)
- [Gobernanza del conjunto de copias de seguridad](backup-center-govern-environment.md)
- [Acciones con el Centro de copias de seguridad](backup-center-actions.md)