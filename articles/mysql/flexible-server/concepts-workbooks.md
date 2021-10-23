---
title: Supervisión de la opción de servidor flexible de Azure Database for MySQL con Libros de Azure Monitor
description: Describe cómo se puede supervisar la opción de servidor flexible de Azure Database for MySQL con Libros de Azure Monitor.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: a773048b2d1ddf8ad7b7993ef7975517506bbd07
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620917"
---
# <a name="monitoring-azure-database-for-mysql---flexible-server-with-azure-monitor-workbooks"></a>Supervisión de la opción de servidor flexible de Azure Database for MySQL con Libros de Azure Monitor

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

La opción de servidor flexible de Azure Database for MySQL ahora está integrada con Libros de Azure Monitor. Los libros proporcionan un lienzo flexible para el análisis de datos y la creación de informes visuales completos en el Azure Portal. Permiten acceder a varios orígenes de datos desde Azure y combinarlos en experiencias interactivas unificadas. Las plantillas de los libros sirven como informes seleccionados que están diseñados para que varios usuarios y equipos puedan reutilizarlos de forma flexible. Al abrir una plantilla se crea un libro transitorio que se rellena con el contenido de la plantilla. Con esta integración, el servidor tiene un vínculo a libros y algunas plantillas de ejemplo que ayudan a supervisar el servicio a gran escala. Estas plantillas se pueden editar, personalizar según los requisitos del cliente y anclarse al panel para crear una vista centrada y organizada de los recursos de Azure.
 
En este artículo, obtendrá información sobre las distintas plantillas de libros disponibles para el servidor flexible.

Tenemos tres plantillas predeterminadas disponibles para la opción de servidor flexible de Azure Database for MySQL.
 
- **Información general:** proporciona el resumen de instancia y las métricas generales para ayudar a comprender el uso de los recursos en el servidor. Podrá ver los detalles siguientes para el servidor flexible de Azure Database for MySQL.

    * Resumen de servidor 
    * Resumen de base de datos
    * Métricas de conexión 
    * Métricas de rendimiento 
    * Métricas de almacenamiento 

* **Auditoría:** resumen y detalles de los eventos de auditoría recopilados del servidor. Están disponibles las vistas siguientes con esta plantilla para el servidor flexible de Azure Database for MySQL.

    * Acciones administrativas en el servicio
    * Resumen de auditoría
    * Resumen de eventos de conexión de auditoría
    * Eventos de conexión de auditoría
    * Resumen de acceso a tablas
    * Errores identificados

* **Información de rendimiento de consultas:** resumen y detalles de la carga de trabajo de consultas en la instancia, consultas de larga duración, análisis de consultas lento y métricas de conexión. Está disponible la vista siguiente con esta plantilla para el servidor flexible de Azure Database for MySQL.

    * Carga de consultas
    * Conexiones activas totales
    * Tendencia de consultas lentas (Tiempo de consulta > 10 segundos)
    * Detalles de consultas lentas
    * Muestre una lista de las cinco consultas más largas.
    * Resuma las consultas lentas por tiempo de consulta mínimo, máximo, promedio y de desviación estándar.

También puede editar estas plantillas y personalizarlas según sus necesidades. Para más información, consulte [Libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).

 ## <a name="how-to-access-workbook-templates"></a>Acceso a las plantillas de libro

Para ver las plantillas en Azure Portal, vaya a la hoja **Supervisión** del servidor flexible de Azure Database for MySQL y seleccione **Libros**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="Diagrama que muestra los libros.":::

También puede ver la lista de plantillas; para ello, vaya a **Plantillas públicas**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="Diagrama que muestra las plantillas de libros":::


## <a name="next-steps"></a>Pasos siguientes
- [Control de acceso](../../azure-monitor/visualize/workbooks-access-control.md) y más información sobre las opciones enriquecidas de visualización de libros.
- [Visualizaciones](../../azure-monitor/visualize/workbooks-overview.md#visualizations) y más información sobre las opciones enriquecidas de visualización de libros.
