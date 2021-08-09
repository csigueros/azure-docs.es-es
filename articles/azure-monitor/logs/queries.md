---
title: Uso de consultas en Log Analytics de Azure Monitor
description: Introducción a las consultas de registros de Log Analytics de Azure Monitor, incluidos los distintos tipos de consultas y ejemplos de consultas que puede usar.
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 552e79a11da001d7834cb9640bb77c08f9491f99
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483460"
---
# <a name="using-queries-in-azure-monitor-log-analytics"></a>Uso de consultas en Log Analytics de Azure Monitor
Al abrir Log Analytics, tiene acceso a las consultas de registro existentes. Puede ejecutar estas consultas tal y como están o usarlas como punto de partida para crear las suyas propias. Las consultas disponibles incluyen ejemplos proporcionados por Azure Monitor y las consultas guardadas por su organización. En este artículo se describen las consultas disponibles y cómo puede detectarlas y usarlas.


## <a name="queries-interface"></a>Interfaz de consultas
Seleccione consultas en la interfaz de consultas que está disponible desde dos ubicaciones diferentes en Log Analytics.

### <a name="queries-dialog"></a>Cuadro de diálogo Consultas

Al abrir Log Analytics, se muestra automáticamente el cuadro de diálogo *Consultas*. Si no quiere que este cuadro de diálogo se muestre automáticamente, desactive el botón de alternancia **Always show Queries** (Mostrar consultas siempre).

![Pantalla Consultas](media/queries/query-start.png)


Cada consulta de ejemplo se representa mediante una tarjeta. Puede examinar rápidamente las consultas para encontrar lo que necesita. Puede ejecutar la consulta directamente desde el cuadro de diálogo o elegir cargarla en el editor de consultas para modificarla.

También se puede acceder a ella haciendo clic en la parte superior derecha de la pantalla en **Consultas**.

[![Botón Queries (Consultas)](media/queries/queries-button.png)](media/queries/queries-button.png#lightbox)

### <a name="query-sidebar"></a>Barra lateral de consulta

Se puede acceder a la misma funcionalidad de la experiencia del cuadro de diálogo desde el panel Consultas de la barra lateral izquierda de Log Analytics. Puede mantener el mouse sobre el nombre de una consulta para obtener su descripción y funciones adicionales.

[![Barra lateral de consulta](media/queries/query-sidebar.png)](media/queries/query-sidebar.png#lightbox)

## <a name="finding-and-filtering-queries"></a>Búsqueda y filtrado de consultas

Las opciones de esta sección están disponibles en la experiencia de consulta del cuadro de diálogo y la barra lateral, pero con una interfaz de usuario ligeramente diferente.  


### <a name="group-by"></a>Agrupar por

Para cambiar la agrupación de las consultas, haga clic en la lista desplegable *Agrupar por*. Los valores de agrupación también actúan como una tabla de contenido activa. Al hacer clic en uno de los valores en el lado izquierdo de la pantalla, la vista de consultas se desplaza directamente al elemento en el que haya hecho clic. Si su organización ha creado paquetes de consulta con etiquetas, las etiquetas personalizadas se incluirán en esta lista.

[![Agrupar por en la pantalla de consultas de ejemplo](media/queries/example-query-groupby.png)](media/queries/example-query-groupby.png#lightbox)



### <a name="filter"></a>Filter

También puede filtrar las consultas según los valores de **group by** mencionados antes. En el cuadro de diálogo de la consulta de ejemplo, los filtros se encuentran en la parte superior.

[![Filtro en la pantalla de consultas de ejemplo](media/queries/example-query-filter.png)](media/queries/example-query-filter.png#lightbox)

### <a name="combining-group-by-and-filter"></a>Combinación de Agrupar por y Filtrar

La funcionalidad de filtrar y agrupar por está diseñada para trabajar de manera conjunta. Proporcionan flexibilidad en el modo de organizar las consultas. Por ejemplo, si usa un grupo de recursos con varios recursos, es posible que quiera filtrar por un tipo de recurso específico y organizar las consultas resultantes por tema.

## <a name="query-properties"></a>Propiedades de la consulta
Cada consulta tiene varias propiedades que le ayudan a agruparlas y encontrarlas. Estas propiedades están disponibles para la ordenación y el filtrado, y puede definir varias de ellas al [guardar su propia consulta](save-query.md). Los tipos de propiedades son:

- **Tipo de recurso**: un recurso como se define en Azure, como una máquina virtual. Vea la [Referencia de tabla de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype) para obtener una asignación completa entra las tablas de registros de Azure Monitor y Log Analytics, y el tipo de recurso.  
- **Categoría**: un tipo de información como *Seguridad* o *Auditoría*. Las categorías son idénticas a las que se definen en el panel lateral Tablas. Para obtener una lista completa de las categorías, vea la [ referencia de tabla de Azure Monitor](/azure/azure-monitor/reference/tables/tables-category).  
- **Solución**: una solución de Azure Monitor asociada a las consultas
- **Tema**: el tema de la consulta de ejemplo, como *Registros de actividad* o *Registros de aplicación*. La propiedad de tema es exclusiva de las consultas de ejemplo y puede diferir según el tipo de recurso específico.
- **Marcas**: etiquetas personalizadas que puede definir y asignar al [guardar su propia consulta](save-query.md).
- **Etiquetas**: propiedades personalizadas que se pueden definir al [crear un paquete de consulta](query-packs.md). Las etiquetas permiten a su organización crear sus propias taxonomías para organizar las consultas.

## <a name="favorites"></a>Favoritos
Puede agregar como favoritas las consultas que use con frecuencia para disponer de un acceso más rápido. Haga clic en la estrella situada junto a la consulta para agregarla a la carpeta **Favoritos**. Vea sus consultas favoritas desde la opción **Favoritos** de la interfaz de consulta.

## <a name="types-of-queries"></a>Tipos de consultas
La interfaz de consulta se rellena con los siguientes tipos de consultas:

**Consultas de ejemplo**: las consultas de ejemplo pueden proporcionar conclusiones instantáneas sobre un recurso y constituyen una manera cómoda de empezar a aprender a usar KQL, lo que reduce el tiempo que se tarda en empezar a usar Log Analytics. Hemos recopilado y mantenido más de 500 consultas de ejemplo diseñadas para proporcionar un valor instantáneo y el número crece continuamente.

**Paquetes de consultas:** un [paquete de consultas](query-packs.md) contiene una colección de consultas de registro, incluidas las que guarda usted mismo. Esto incluye el [paquete de consultas predeterminado](query-packs.md#default-query-pack) y cualquier otro paquete de consultas que la organización pueda haber creado en la suscripción.

**Consultas heredadas:** consultas de registros guardadas anteriormente en la experiencia del explorador de consultas y soluciones de Azure de consultas que están instaladas en el área de trabajo. Se muestran en el cuadro de diálogo de consulta en **Legacy queries** (Consultas heredadas).

## <a name="effect-of-query-scope"></a>Efecto del ámbito de consulta
El [ámbito de consulta actual ](scope.md) determina las consultas que están disponibles al abrir Log Analytics.

- Para un **área de trabajo**: todas las consultas y ejemplos de consultas de los paquetes de consultas. Consultas heredadas en el área de trabajo.
- Para un **único recurso**: ejemplos de consultas y consultas de paquetes de consultas para el tipo de recurso. 
- Para un **grupo de recursos**: ejemplos de consultas y consultas de paquetes de consultas para los tipos de recursos del grupo de recursos. 

> [!TIP]
> Cuantos más recursos tenga en el ámbito, más tiempo tardará el portal en filtrar y mostrar el cuadro de diálogo de consulta.


## <a name="next-steps"></a>Pasos siguientes

[Introducción a las consultas KQL](get-started-queries.md)

