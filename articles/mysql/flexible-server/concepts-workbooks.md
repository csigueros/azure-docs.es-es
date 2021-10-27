---
title: 'Supervisión de Azure Database for MySQL: servidor flexible con libros de Azure Monitor'
description: 'En este artículo se explica cómo supervisar Azure Database for MySQL: servidor flexible con libros de Azure Monitor.'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: aa26531dd9f644b4dc5b3343674abd7920fe57dd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064597"
---
# <a name="monitor-azure-database-for-mysql-flexible-server-by-using-azure-monitor-workbooks"></a>Supervisión de Azure Database for MySQL: servidor flexible con libros de Azure Monitor

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL: servidor flexible ahora está integrado con los libros de Azure Monitor. Con los libros se obtiene un lienzo flexible para analizar datos y crear informes visuales enriquecidos dentro de Azure Portal. Los libros permiten acceder a varios orígenes de datos de Azure y combinarlos en experiencias interactivas unificadas. Las plantillas de libro sirven como informes mantenidos diseñados para que varios usuarios y equipos puedan reutilizarlos de forma flexible. 

Al abrir una plantilla, se crea un libro transitorio que se rellena con el contenido de la plantilla. Con esta integración, el servidor se vincula a los libros y a algunas plantillas de ejemplo, lo que ayuda a supervisar el servicio a gran escala. Puede editar estas plantillas, personalizarlas según los requisitos y anclarlas al panel para crear una vista centrada y organizada de los recursos de Azure.
 
En este artículo va a obtener información sobre las distintas plantillas de libro disponibles para el servidor flexible.

Azure Database for MySQL: servidor flexible tiene tres plantillas disponibles:
 
- **Información general**: muestra un resumen de instancia y métricas generales para ayudar a visualizar y comprender el uso de recursos en el servidor. Esta plantilla muestra las vistas siguientes:

    * Resumen de servidor 
    * Resumen de base de datos
    * Métricas de conexión 
    * Métricas de rendimiento 
    * Métricas de almacenamiento 

* **Auditoría**: muestra un resumen y detalles de los eventos de auditoría recopilados del servidor. Esta plantilla muestra las vistas siguientes:

    * Acciones administrativas en el servicio
    * Resumen de auditoría
    * Resumen de eventos de conexión de auditoría
    * Eventos de conexión de auditoría
    * Resumen de acceso a tablas
    * Errores identificados

* **Información de rendimiento de consultas**: muestra un resumen y detalles de la carga de trabajo de consultas en la instancia, consultas de larga duración, análisis de consultas lentas y métricas de conexión. Esta plantilla muestra las vistas siguientes:

    * Carga de consultas
    * Conexiones activas totales
    * Tendencia de consultas lentas (> 10 segundos de tiempo de consulta)
    * Detalles de la consulta lenta
    * Lista de las 5 consultas de mayor duración
    * Resuma las consultas lentas por tiempo de consulta mínimo, máximo, promedio y de desviación estándar.

También puede editar y personalizar estas plantillas según sus necesidades. Para obtener más información, vea [Libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).

 ## <a name="access-the-workbook-templates"></a>Acceso a las plantillas de libro

Para ver las plantillas en Azure Portal, vaya al panel **Supervisión** de Azure Database for MySQL: servidor flexible y seleccione **Libros**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="Captura de pantalla que muestra las plantillas &quot;Información general&quot;, &quot;Auditoría&quot; e &quot;Información de rendimiento de consultas&quot; en el panel Libros.":::

También puede mostrar la lista de plantillas si va al panel **Plantillas públicas**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="Diagrama que muestra las plantillas &quot;Información general&quot;, &quot;Auditoría&quot; e &quot;Información de rendimiento de consultas&quot; en el panel &quot;Plantillas públicas&quot;.":::


## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [control de acceso](../../azure-monitor/visualize/workbooks-access-control.md) en los libros de Azure Monitor.
- Más información sobre las [opciones de visualización](../../azure-monitor/visualize/workbooks-overview.md#visualizations) en los libros de Azure Monitor. 
