---
title: Aislamiento de cargas de trabajo
description: Instrucciones para establecer el aislamiento de cargas de trabajo con grupos de cargas de trabajo en Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dec042c66ebed15a51d5c6c1f3ef6a3e3e2fb456
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428033"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Aislamiento de grupos de cargas de trabajo de Azure Synapse Analytics

En este artículo se explica cómo se pueden usar los grupos de cargas de trabajo para configurar el aislamiento de la carga de trabajo, contener recursos y aplicar reglas en tiempo de ejecución para la ejecución de consultas.

## <a name="workload-groups"></a>Grupos de carga de trabajo

Los grupos de cargas de trabajo son contenedores para un conjunto de solicitudes y son la base de la configuración de la administración de cargas de trabajo en un sistema, incluido el aislamiento de la carga de trabajo.  Los grupos de cargas de trabajo se crean con la sintaxis [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Una configuración sencilla de administración de cargas de trabajo puede administrar cargas de datos y consultas de usuario.  Por ejemplo, un grupo de cargas de trabajo denominado `wgDataLoads` definirá los aspectos de la carga de trabajo para los datos que se cargan en el sistema. Además, un grupo de cargas de trabajo denominado `wgUserQueries` definirá los aspectos de la carga de trabajo para los usuarios que ejecutan consultas para leer datos del sistema.

En las secciones siguientes se resaltará el modo en que los grupos de cargas de trabajo proporcionan la capacidad de definir el aislamiento, la contención, la definición de recursos de solicitud y el cumplimiento de las reglas de ejecución.

## <a name="workload-isolation"></a>Aislamiento de cargas de trabajo

El aislamiento de la carga de trabajo significa que los recursos se reservan, de forma exclusiva, para un grupo de cargas de trabajo.  El aislamiento de la carga de trabajo se logra al configurar el parámetro MIN_PERCENTAGE_RESOURCE en un valor mayor que cero en la sintaxis [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  En el caso de las cargas de trabajo de ejecución continua que necesiten cumplir con acuerdos de nivel de servicio estrictos, el aislamiento garantiza que los recursos siempre estén disponibles para el grupo de cargas de trabajo.

La configuración del aislamiento de cargas de trabajo define implícitamente un nivel garantizado de simultaneidad. Por ejemplo, un grupo de cargas de trabajo con `MIN_PERCENTAGE_RESOURCE` establecido en el 30 % y `REQUEST_MIN_RESOURCE_GRANT_PERCENT` en el 2 % garantiza una simultaneidad de 15.  El nivel de simultaneidad está garantizado, ya que 15 ranuras de recursos del 2 % están reservadas en el grupo de cargas de trabajo en todo momento (independientemente de la configuración de `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`).  Si `REQUEST_MAX_RESOURCE_GRANT_PERCENT` es mayor que `REQUEST_MIN_RESOURCE_GRANT_PERCENT` y `CAP_PERCENTAGE_RESOURCE` es mayor que `MIN_PERCENTAGE_RESOURCE` se agregan recursos adicionales cuando se soliciten.  Si `REQUEST_MAX_RESOURCE_GRANT_PERCENT` y `REQUEST_MIN_RESOURCE_GRANT_PERCENT` son iguales y `CAP_PERCENTAGE_RESOURCE` es mayor que `MIN_PERCENTAGE_RESOURCE`, es posible que haya más simultaneidad.  Considere el método siguiente para determinar la simultaneidad garantizada:

[Simultaneidad garantizada] = [`MIN_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Hay valores mínimos de nivel de servicio viables para min_percentage_resource.  Para obtener más información, vea [Valores efectivos](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values).

En ausencia del aislamiento de la carga de trabajo, las solicitudes operan en el [grupo compartido](#shared-pool-resources) de recursos.  El acceso a los recursos del grupo compartido no está garantizado y se asigna en función de la [importancia](sql-data-warehouse-workload-importance.md).

La configuración del aislamiento de la carga de trabajo se debe hacer con precaución, ya que los recursos se asignan al grupo de cargas de trabajo incluso si no hay ninguna solicitud activa en el grupo de cargas de trabajo. La configuración excesiva del aislamiento puede reducir el uso general del sistema.

Los usuarios deben evitar una solución de administración de cargas de trabajo que configure el aislamiento del 100 % de las cargas de trabajo: El aislamiento del 100 % se consigue cuando la suma de min_percentage_resource configurada en todos los grupos de cargas de trabajo es igual a 100 %.  Este tipo de configuración es excesivamente restrictiva y rígida, lo que deja poco espacio para las solicitudes de recursos que se clasifican de forma incorrecta. Hay una disposición para permitir que una solicitud se ejecute desde grupos de cargas de trabajo que no estén configurados para el aislamiento. Los recursos asignados a esta solicitud se mostrarán como un cero en las DMV de los sistemas y tomarán un nivel de concesión de recursos smallrc de los recursos reservados del sistema.

> [!NOTE]
> Para garantizar un uso óptimo de los recursos, considere la posibilidad de usar una solución de administración de cargas de trabajo que aproveche cierto aislamiento para asegurarse de que se cumplen y se mezclan con recursos compartidos a los que se accede en función de la [importancia de la carga de trabajo](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Contención de la carga de trabajo

La contención de la carga de trabajo hace referencia a la limitación de la cantidad de recursos que puede consumir un grupo de cargas de trabajo.  La contención de la carga de trabajo se logra al configurar el parámetro CAP_PERCENTAGE_RESOURCE en un valor menor que 100 en la sintaxis [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Considere el escenario en el que los usuarios necesitan acceso de lectura al sistema para ejecutar un análisis de hipótesis mediante consultas ad hoc.  Estos tipos de solicitudes podrían tener un impacto negativo en otras cargas de trabajo que se ejecutan en el sistema.  Al configurar la contención se garantiza que la cantidad de recursos es limitada.

La configuración de la contención de cargas de trabajo define implícitamente un nivel de simultaneidad máximo.  Con un valor CAP_PERCENTAGE_RESOURCE establecido en 60 % y REQUEST_MIN_RESOURCE_GRANT_PERCENT establecido en 1 %, se permite un nivel de simultaneidad máximo de 60 para el grupo de cargas de trabajo.  Tenga en cuenta el método que se incluye a continuación para determinar la simultaneidad máxima:

[Simultaneidad máxima] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> El valor CAP_PERCENTAGE_RESOURCE efectivo de un grupo de cargas de trabajo no alcanzará el 100 % cuando se crean grupos de cargas de trabajo con un valor MIN_PERCENTAGE_RESOURCE en un nivel mayor que cero.  Vea [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obtener los valores en tiempo de ejecución efectivos.

## <a name="resources-per-request-definition"></a>Recursos por definición de solicitud

Los grupos de cargas de trabajo proporcionan un mecanismo para definir la cantidad mínima y máxima de recursos que se asignan por solicitud con los parámetros REQUEST_MIN_RESOURCE_GRANT_PERCENT y REQUEST_MAX_RESOURCE_GRANT_PERCENT en la sintaxis [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  En este caso, el recurso es memoria. Los recursos de CPU están limitados por el valor CAP_PERCENTAGE_RESOURCE del grupo de cargas de trabajo y no se rigen en el nivel de solicitud individual. La configuración de estos valores determina la cantidad de recursos y el nivel de simultaneidad que se puede lograr en el sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT es un parámetro opcional cuyo valor predeterminado es el mismo que se especifica para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Como sucede al elegir una clase de recursos, la configuración de REQUEST_MIN_RESOURCE_GRANT_PERCENT establece el valor de los recursos utilizados por una solicitud.  La cantidad de recursos indicada por el valor establecido se garantiza para la asignación a la solicitud antes de que comience la ejecución.  Para los clientes que migren desde clases de recursos a grupos de cargas de trabajo, considere el artículo de [procedimientos](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) para asignar las clases de recursos a los grupos de cargas de trabajo como punto de partida.

La configuración de REQUEST_MAX_RESOURCE_GRANT_PERCENT en un valor mayor que REQUEST_MIN_RESOURCE_GRANT_PERCENT permite al sistema asignar más recursos por solicitud.  Durante la programación de una solicitud, el sistema determina la asignación de recursos real a la solicitud, que se encuentra entre REQUEST_MIN_RESOURCE_GRANT_PERCENT y REQUEST_MAX_RESOURCE_GRANT_PERCENT, en función de la disponibilidad de los recursos en el grupo compartido y la carga actual en el integrado.  Los recursos deben existir en el [grupo compartido](#shared-pool-resources) de recursos cuando se programa la consulta.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT y REQUEST_MAX_RESOURCE_GRANT_PERCENT tienen valores efectivos que dependen de los valores efectivos de MIN_PERCENTAGE_RESOURCE y CAP_PERCENTAGE_RESOURCE.  Vea [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obtener los valores en tiempo de ejecución efectivos.

## <a name="execution-rules"></a>Reglas de ejecución

En los sistemas de informes ad hoc, los clientes pueden ejecutar por accidente consultas descontroladas que afectan gravemente a la productividad de terceros.  Los administradores del sistema tendrán que dedicar tiempo a eliminar las consultas descontroladas para liberar recursos del sistema.  Los grupos de cargas de trabajo ofrecen la posibilidad de configurar una regla de tiempo de expiración de ejecución de consulta para cancelar las consultas que hayan superado el valor especificado.  Para configurar la regla se establece el parámetro `QUERY_EXECUTION_TIMEOUT_SEC` en la sintaxis [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="shared-pool-resources"></a>Recursos de grupo compartidos

Los recursos de grupo compartidos son los que no están configurados para el aislamiento.  Los grupos de cargas de trabajo con un valor MIN_PERCENTAGE_RESOURCE establecido en cero aprovechan los recursos del grupo compartido para ejecutar solicitudes.  Los grupos de cargas de trabajo con un valor CAP_PERCENTAGE_RESOURCE mayor que MIN_PERCENTAGE_RESOURCE también usan recursos compartidos.  La cantidad de recursos disponibles en el grupo compartido se calcula como se indica a continuación.

[Grupo compartido] = 100 - [suma de `MIN_PERCENTAGE_RESOURCE` en todos los grupos de cargas de trabajo]

El acceso a los recursos del grupo compartido se asigna en función de la [importancia](sql-data-warehouse-workload-importance.md).  Las solicitudes con el mismo nivel de importancia tendrán acceso a los recursos de grupo compartidos en función del esquema primero en entrar, primero en salir.

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido: Configuración del aislamiento de cargas de trabajo](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Conversión de las clases de recursos en grupos de cargas de trabajo](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Supervisión del portal de administración de cargas de trabajo](sql-data-warehouse-workload-management-portal-monitor.md).  
