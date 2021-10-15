---
title: Solución de problemas de cargas de trabajo mal clasificadas en un grupo de SQL dedicado
description: Identifique y solucione los escenarios en los que las cargas de trabajo se han clasificado erróneamente en grupos de cargas de trabajo no deseados en un grupo de SQL dedicado en Azure Synapse Analytics.
author: SudhirRaparla
ms.author: nvraparl
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 10/01/2021
ms.custom: template-how-to
ms.reviewer: wiassaf
ms.openlocfilehash: 6cf1564ccc62d6429da56bc8116b124b44b58653
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728406"
---
# <a name="troubleshooting-a-misclassified-workload-in-azure-synapse-analytics"></a>Solución de problemas de una carga de trabajo mal clasificada en Azure Synapse Analytics

En este artículo se tratan las instrucciones para solucionar problemas de una carga de trabajo mal clasificada y cómo identificar el motivo de la clasificación, para las cargas de trabajo de un grupo de SQL dedicado.

Azure Synapse Analytics proporciona funcionalidades de administración de cargas de trabajo, como la [clasificación de cargas de trabajo en grupos de cargas de trabajo adecuados](sql-data-warehouse-workload-classification.md), la [asignación de importancia](sql-data-warehouse-workload-importance.md) y el [aislamiento de recursos](sql-data-warehouse-workload-isolation.md) para cumplir los Acuerdos de Nivel de Servicio. 

Sin embargo, en algunos escenarios, una combinación de estas funcionalidades puede dar lugar a una clasificación de la carga de trabajo que no refleje la intención del usuario. En este artículo se enumeran estos escenarios comunes y la forma de solucionarlos. En primer lugar, debe consultar la información básica para solucionar problemas de escenarios de carga de trabajo mal clasificados.

> [!NOTE]
> Este artículo no se aplica a grupos de SQL sin servidor en Azure Synapse Analytics.

## <a name="basic-troubleshooting-information"></a>Información básica de solución de problemas

Para solucionar problemas de un escenario de carga de trabajo mal clasificado, se necesita la siguiente información:

- Lista de todos los [grupos de carga de trabajo](#workload-groups)
- Lista de todos los [clasificadores de cargas de trabajo](#workload-classifiers) y grupos de cargas de trabajo asociados
- Lista de [usuarios y grupos de cargas de trabajo asignados](#users-and-mapped-resource-classes) (definidos por el sistema y el usuario) 
- Lista de [detalles del clasificador y grupo de cargas de trabajo](#workload-group-and-classifier-details-of-a-request) de una solicitud


### <a name="workload-groups"></a>Grupos de carga de trabajo

#### <a name="azure-portal"></a>Azure portal

Para obtener una lista de todos los grupos de cargas de trabajo (incluidos los grupos de cargas de trabajo del sistema) y los detalles asociados en el Azure Portal:

1. Vaya al área de trabajo de Azure Synapse en la que se ha creado el grupo de SQL dedicado de interés.
1. En el panel izquierdo, se muestran todos los tipos de grupos creados en el área de trabajo. Seleccione **Grupos de SQL** de **Analytical Pools** (Grupos analíticos).
1. Seleccione el grupo de SQL dedicado de interés.
1. En el panel izquierdo, seleccione **Administración de cargas de trabajo** en **Configuración**.
1. En la sección **Grupos de cargas de trabajo**, se enumeran todas las cargas de trabajo. De manera predeterminada, solo se enumera **User-Defined Workload groups** (Grupo de cargas de trabajo definidos por el usuario). Para ver una lista de grupos de cargas de trabajo definidos por el usuario y definidos por el sistema, edite el filtro **Seleccionar todo**.

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png" alt-text="Filtro de la lista de grupos de cargas de trabajo" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png":::

#### <a name="t-sql"></a>T-SQL

Para ver grupos de cargas de trabajo mediante T-SQL, [conéctese al grupo de SQL dedicado mediante SQL Server Management Studio (SSMS)](../sql/get-started-ssms.md) y emita la siguiente consulta:
 
```sql
SELECT * FROM sys.workload_management_workload_groups;
```

Para más información, consulte [sys.workload_management_workload_groups](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql).


### <a name="workload-classifiers"></a>Clasificadores de cargas de trabajo

#### <a name="azure-portal"></a>Azure portal

Para enumerar todos los clasificadores de cargas de trabajo (incluidos los clasificadores definidos por el sistema) por grupo de cargas de trabajo en Azure Portal, vea los números enumerados en la columna **Clasificadores** de la tabla de grupo de cargas de trabajo (consulte la sección anterior).

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png" alt-text="Lista de clasificadores de grupos de cargas de trabajo" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png":::

#### <a name="t-sql"></a>T-SQL

Para obtener la lista de todos los clasificadores de carga de trabajo (incluidos los clasificadores definidos por el sistema) mediante T-SQL, use la siguiente consulta:

```sql
SELECT * FROM sys.workload_management_workload_classifiers;
```

Para más información, consulte [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql).

### <a name="users-and-mapped-resource-classes"></a>Usuarios y clases de recursos asignadas

Para obtener una lista de clases de recursos definidas por el sistema y usuarios asignados en T-SQL, use la siguiente consulta:

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS [Member Name]
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');
```

### <a name="workload-group-and-classifier-details-of-a-request"></a>Detalles del clasificador y grupo de cargas de trabajo de una solicitud

El primer paso para solucionar un problema de carga de trabajo clasificada erróneamente es identificar el grupo de cargas de trabajo y el clasificador de cargas de trabajo para una consulta. Use la vista de administración dinámica [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para ver las consultas enviadas y su clasificación:

```sql
SELECT * FROM sys.dm_pdw_exec_requests;
```

Para más información, consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql).

## <a name="common-scenarios-of-misclassified-workloads"></a>Escenarios comunes de cargas de trabajo mal clasificadas

Estos son algunos escenarios comunes en los que pueden producirse cargas de trabajo mal clasificadas no deseadas:

### <a name="mixed-usage-of-resource-classes-and-user-defined-workload-management"></a>Utilización combinada de clases de recursos y administración de cargas de trabajo definidas por el usuario

En escenarios en los que las clases de recursos y los grupos de cargas de trabajo se usan conjuntamente, las asignaciones de roles de usuario a clases de recursos pueden entrar en conflicto con las reglas de clasificadores de cargas de trabajo y dar lugar a una clasificación de consultas no deseada. 

Considere el siguiente escenario:

1. Un usuario de base de datos, DBAUser, se asigna al rol de clase de recursos largerc mediante el procedimiento `sp_addrolemember`.
1. DBAUser ha creado un nuevo grupo de cargas de trabajo y un clasificador mediante la administración de cargas de trabajo.
1. Un clasificador de cargas de trabajo recién creado asigna el rol de base de datos DBARole a la clase de recursos mediumrc con una importancia alta. 
1. DBAUser es un miembro del rol de base de datos DBARole.
1. Cuando DBAUser ejecuta una consulta, se espera que esta se ejecute en mediumrc según el clasificador de cargas de trabajo. En su lugar, se asignará a largerc, ya que la asignación del **usuario** tiene prioridad sobre la asignación de **pertenencia a roles** para un clasificador.

Es mejor evitar mezclar la utilización de clases de recursos y grupos de administración de cargas de trabajo para realizar la administración de cargas de trabajo. Para más información sobre los pasos para convertir clases de recursos en cargas de trabajo, vea [Conversión de clases de recursos en grupos de cargas de trabajo](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md). 

Sin embargo, puede haber situaciones en las que las clases de recursos y la administración de cargas de trabajo deban usarse juntas. En esos escenarios, para simplificar la solución de problemas de clasificaciones incorrectas, se recomienda quitar las asignaciones de rol de clase de recursos a medida que se creen los clasificadores de cargas de trabajo. El código siguiente devuelve las pertenencias existentes a roles de clase de recurso. Ejecute [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para cada nombre de miembro que se devuelve desde la clase de recursos correspondiente.

```sql
SELECT  r.name AS [Resource Class]
, m.name AS membername
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
EXEC sp_droprolemember '[Resource Class]', membername;
```

### <a name="some-administrative-users-are-always-mapped-to-smallrc-workload-group"></a>Algunos usuarios administrativos siempre se asignan al grupo de cargas de trabajo smallrc

Considere un escenario para el inicio de sesión de administrador de SQL en el área de trabajo de Azure Synapse, el administrador de Azure Active Directory de Azure Synapse (usuario o miembro de grupo) o el propietario de una base de datos. Estos usuarios pueden seguir teniendo un clasificador de cargas de trabajo o pueden haberse agregado a un rol de clase de recurso que no sea smallrc. Todas las consultas ejecutadas por estos usuarios seguirán ejecutándose en la clase de recurso smallrc, aunque el usuario esté asignado a otro grupo de cargas de trabajo o clase de recursos. 

**Recomendación**: Estos usuarios administrativos no pueden cambiar su grupo de cargas de trabajo predeterminado. Para más información, consulte [Administración de cargas de trabajo con clases de recursos](resource-classes-for-workload-management.md#default-resource-class). Se recomienda que las cargas de trabajo críticas o sensibles al rendimiento no se ejecuten como uno de estos usuarios administrativos en el grupo de SQL dedicado.

El inicio de sesión de administrador de SQL en el área de trabajo de Azure Synapse y el administrador de Azure Active Directory de Azure Synapse (usuario o miembro del grupo) se especifican en Azure Portal:

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png" alt-text="Identificación del administrador del servicio mediante el campo de inicio de sesión del administrador del área de trabajo" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png":::

De manera similar, el propietario de la base de datos (dbo) y los roles de base de datos **db_owner** no pueden cambiar su clase de recurso predeterminada. Si un usuario es el propietario de la base de datos o se agrega al rol de base de datos **db_owner**, todas las consultas ejecutadas por el usuario van a smallrc de forma predeterminada. Estos roles no se pueden agregar a una clase de recurso que no sea smallrc. Sin embargo, si un usuario que forma parte de este rol quiere clasificar sus consultas en un grupo de cargas de trabajo diferente, puede usar la opción MEMBERNAME en la [definición del clasificador de cargas de trabajo](sql-data-warehouse-workload-classification.md).


### <a name="use-workload-group-precedence-for-better-classification"></a>Uso de la precedencia del grupo de cargas de trabajo para una mejor clasificación

En escenarios en los que los clasificadores de cargas de trabajo se asignan a varios grupos de cargas de trabajo o un usuario se asigna a varias clases de recursos, el orden de precedencia determina el grupo de cargas de trabajo y la clase de recursos elegidos. Lista de reglas de precedencia: 

> [!NOTE]
> Como se mencionó en la sección [Combinación de clases de recursos y administración de cargas de trabajo definidas por el usuario](#mixed-usage-of-resource-classes-and-user-defined-workload-management), no se recomienda combinar la utilización de clases de recursos y clases o grupos de cargas de trabajo definidos por el usuario.

#### <a name="if-resource-classes-are-being-used"></a>Si se usan clases de recursos:

En los escenarios en los que se usan clases de recursos, es mejor crear un usuario dedicado para cada tipo de carga de trabajo que se ejecuta. Sin embargo, si un usuario es miembro de varias clases de recursos, surten efecto las reglas de precedencia documentadas en [Precedencia de clases de recursos](resource-classes-for-workload-management.md#resource-class-precedence):

1. La clase de recurso dinámico tienen prioridad sobre la clase de recurso estático. Por ejemplo, si un usuario es miembro de mediumrc (dinámico) y de staticrc80 (estático), las consultas del usuario se ejecutarán con mediumrc.
1. Las clases de recursos más grandes se prefieren a las clases de recursos más pequeñas. Por ejemplo, si un usuario es miembro de staticrc20 y staticrc80, las consultas de usuario se ejecutarán con staticrc80.

#### <a name="if-workload-management-capabilities-are-used"></a>Si se usan funcionalidades de administración de cargas de trabajo

WLM proporciona la capacidad de crear varios clasificadores de cargas de trabajo para el mismo usuario o grupo de cargas de trabajo. La instrucción de definición de clasificador tiene varios parámetros en función de qué solicitudes entrantes se asignan a las cargas de trabajo. Estos parámetros tienen una puntuación de **peso** como se muestra a continuación y esta puntuación determina el orden de precedencia: 

|**Parámetro clasificador** |**Peso**   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Veamos estas reglas de precedencia en acción mediante un ejemplo. Si un usuario crea dos clasificadores de cargas de trabajo de la siguiente manera:

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,START_TIME     = '18:00' 
 ,END_TIME       = '07:30'
 ,IMPORTANCE     = 'Low')
```

Las consultas enviadas por User-1 se pueden enviar a través de ambos clasificadores. La consulta ejecutada por User-1 con la etiqueta "dimension_loads" entre las 18:00 y las 7:00 UTC se asignará a `wgDashboards`, ya que la puntuación de peso de WLM_LABEL es superior a START_TIME/END_TIME. La ponderación de `CLASSIFIER-1` es 80 (64 para el usuario, más 16 para WLM_LABEL). La ponderación de `CLASSIFIER-2` es 68 (64 para el usuario, 4 para START_TIME/END_TIME). 

Para más información sobre la clasificación de cargas de trabajo, vea la [ponderación de cargas de trabajo](sql-data-warehouse-workload-classification.md#classification-weighting) y las [etiquetas de consulta](../sql/develop-label.md).

### <a name="what-happens-if-precedence-in-workload-classification-leads-to-a-tie"></a>¿Qué ocurre si la precedencia en la clasificación de cargas de trabajo conduce a un empate?

Incluso después de aplicar la precedencia del clasificador de cargas de trabajo, es posible que una consulta se clasifique en varios grupos de cargas de trabajo. Por ejemplo, considere los siguientes clasificadores:

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'Low');
```

Si un usuario ejecuta una consulta con `OPTION (LABEL = 'dimension_loads')`, se puede clasificar en `wgDataLoad` o `wgUserqueries` en función de las reglas de precedencia. Pero ¿qué grupo de cargas de trabajo se elige?

#### <a name="workload-group-and-classifier-tie-breakers"></a>Desempate de grupos de cargas de trabajo y clasificadores

Si hay un empate en los clasificadores o grupos de cargas de trabajo, se aplica la siguiente precedencia:

1. Se elige el grupo de cargas de trabajo con la asignación de recursos más alta. Este comportamiento optimiza el rendimiento en escenarios donde los inicios de sesión son miembros de varias clases de recursos. Este comportamiento también garantiza la compatibilidad con versiones anteriores.  
    
Tenga en cuenta los dos grupos de cargas de trabajo y clasificadores de cargas de trabajo siguientes:
    
```sql
-- Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 26 
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2              
,CAP_PERCENTAGE_RESOURCE = 100 )

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 15
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5               
,CAP_PERCENTAGE_RESOURCE = 50 );

--Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');
```
                  
Si un usuario ejecuta una consulta con `OPTION (LABEL = 'dimension_loads')`, la consulta cumple los criterios de ambos clasificadores. Sin embargo, la solicitud de usuario se enrutará al grupo de cargas de trabajo `wgUserQueries`, ya que tiene una asignación de recursos mínima mayor por solicitud.

2. A continuación, se tienen en cuenta para los desempates la configuración de simultaneidad y la simultaneidad disponible para los grupos de cargas de trabajo respectivos. El grupo de cargas de trabajo con la simultaneidad más alta disponible en el momento en que llegó la solicitud dará a la consulta mayor opción de ejecutarse. 

Considere los dos grupos de cargas de trabajo y clasificadores de cargas de trabajo siguientes:

```sql
--Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 30              
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 30
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

-- Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');
```
              
Si un usuario ejecuta una consulta con `OPTION (LABEL = 'dimension_loads')`, los clasificadores empatan ya que la consulta cumple los criterios de ambos. Cuando el usuario envía la consulta, considere el escenario en el que se ejecutan cinco consultas simultáneas en el grupo `wgUserqueries` y se ejecutan diez consultas en el grupo `wgDataLoad`. La solicitud de usuario se enruta al grupo `wgUserqueries` mediante `CLASSIFIER-2`, ya que el grupo de cargas de trabajo `wgUserqueries` tiene mayor simultaneidad disponible en el momento en que el usuario envió una consulta.

3. A continuación, se tiene en cuenta para los desempates la configuración de la importancia de la solicitud. Si hay un empate en la clasificación de cargas de trabajo mediante las reglas de precedencia, la solicitud se enrutará al grupo de cargas de trabajo que tenga mayor importancia. Para más información, vea la [importancia de las cargas de trabajo](sql-data-warehouse-workload-importance.md).

4. Por último, se tiene en cuenta para los desempates la hora de creación del grupo de cargas de trabajo. La solicitud del usuario se enrutará al grupo de cargas de trabajo que se creó más recientemente. 


## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la clasificación de cargas de trabajo, consulte [Clasificación de cargas de trabajo](sql-data-warehouse-workload-classification.md).
- Para más información sobre la importancia de la carga de trabajo, consulte [Importancia de las cargas de trabajo](sql-data-warehouse-workload-importance.md).

> [!div class="nextstepaction"]
> [Ir a Configuración de la importancia de la carga de trabajo](sql-data-warehouse-how-to-configure-workload-importance.md)
 
