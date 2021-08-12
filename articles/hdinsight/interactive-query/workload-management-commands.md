---
title: Comandos de Workload Management de Hive LLAP
titleSuffix: Azure HDInsight
description: Comandos de Workload Management de Hive LLAP
ms.service: hdinsight
ms.topic: reference
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: dc05ae5ec7cad35d5cda549e654caf3d44d91a03
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483132"
---
# <a name="hive-llap-workload-management-commands"></a>Comandos de Workload Management de Hive LLAP

La característica de administración de cargas de trabajo se puede controlar y administrar con la ayuda de los siguientes comandos de Hive. Estos comandos son similares a las instrucciones ALTER, CREATE, DROP y SHOW existentes.

## <a name="alter-mapping"></a>Alter Mapping 
Cambia el enrutamiento de las consultas a un grupo de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
ALTER { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ]
```
#### <a name="example"></a>Ejemplo 
```hql
ALTER USER MAPPING 'hive' IN demo_plan TO etl WITH ORDER 1;
```

## <a name="alter-pool"></a>Alter Pool 
Modifica las propiedades del grupo de consultas, agrega desencadenadores y los quita. 
#### <a name="syntax"></a>Sintaxis 
```hql
ALTER POOL plan_name.pool_path [ SET {property=value, ... } | UNSET { property, ... } ];
ALTER POOL plan_name.pool_path [ ADD | DROP ] TRIGGER name;
```
#### <a name="example"></a>Ejemplo 
```hql
ALTER POOL demo_plan.default ADD TRIGGER defaultToETL;
```

## <a name="alter-resource-plan"></a>Alter Resource Plan 
Habilita, deshabilita, activa, valida o cambia un plan. 
#### <a name="syntax"></a>Sintaxis 
```hql
ALTER RESOURCE PLAN name [ VALIDATE | DISABLE | ENABLE | ACTIVATE | RENAME TO another_name | SET {property=value, ... } | UNSET {property, ... } ];
```
#### <a name="example"></a>Ejemplo 
```hql
ALTER RESOURCE PLAN demo_plan SET DEFAULT POOL=etl, QUERY_PARALLELISM=3;
```

## <a name="alter-trigger"></a>Alter Trigger 
Agrega o quita un desencadenador de un grupo de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
ALTER TRIGGER plan_name.name { ADD TO | DROP FROM } { POOL path | UNMANAGED };
```
#### <a name="example"></a>Ejemplo 
```hql
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;
```

## <a name="create-mapping"></a>Create Mapping 
Enruta las consultas a un grupo de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
CREATE { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ];
```
#### <a name="example"></a>Ejemplo 
```hql
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
```

## <a name="create-pool"></a>Create Pool 
Crea y agrega un grupo de consultas para un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
CREATE POOL plan_name.path WITH ALLOC_FRACTION = decimal, QUERY_PARALLELISM = num, [ SCHEDULING_POLICY = scheduling_value ];
```
#### <a name="example"></a>Ejemplo 
```hql
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
```

## <a name="create-resource-plan"></a>Create Resource Plan 
Crea un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
CREATE RESOURCE PLAN plan_name [ WITH QUERY PARALLELISM=number | LIKE name];
```
#### <a name="example"></a>Ejemplo 
```hql
CREATE RESOURCE PLAN demo_plan;
```

## <a name="create-trigger"></a>Crear desencadenador 
Crea y agrega un desencadenador a un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
CREATE TRIGGER plan_name.name WHEN condition DO action;
```
#### <a name="example"></a>Ejemplo 
```hql
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
```

## <a name="disable-workload-management"></a>Disable Workload Management 
Desactiva el plan de recursos activo. 
#### <a name="syntax"></a>Sintaxis 
```hql
DISABLE WORKLOAD MANAGEMENT;
```
#### <a name="example"></a>Ejemplo 
```hql
DISABLE WORKLOAD MANAGEMENT
```

## <a name="drop-mapping"></a>Drop Mapping 
Quita una asignación de un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
DROP { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name;
```
#### <a name="example"></a>Ejemplo 
```hql
DROP USER MAPPING 'hive' IN demo_plan;
```

## <a name="drop-pool"></a>Drop Pool 
Quita un grupo de consultas de un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
DROP POOL plan_name.pool_path;
```
#### <a name="example"></a>Ejemplo 
```hql
CREATE POOL demo_plan.etl;
```

## <a name="drop-resource-plan"></a>Drop Resource Plan 
Elimina un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
DROP RESOURCE PLAN plan_name;
```
#### <a name="example"></a>Ejemplo 
```hql
DROP RESOURCE PLAN demo_plan;
```

## <a name="drop-trigger"></a>Drop Trigger 
Elimina un desencadenador de un plan de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
DROP TRIGGER plan_name.trigger_name;
```
#### <a name="example"></a>Ejemplo 
```hql
DROP TRIGGER demo_plan.defaultToETL;
```

## <a name="replace-resource-plan-with"></a>Replace Resource Plan With 
Reemplaza el contenido de un plan de recursos por el contenido de otro. 
#### <a name="syntax"></a>Sintaxis 
```hql
REPLACE RESOURCE PLAN name1 WITH name2; 
REPLACE ACTIVE RESOURCE PLAN name1 WITH name2;
```
#### <a name="example"></a>Ejemplo 
```hql
REPLACE RESOURCE PLAN rp_plan1 WITH rp_plan2;
```

## <a name="show-resource-plan"></a>Show Resource Plan 
Muestra el contenido del plan. 
#### <a name="syntax"></a>Sintaxis 
```hql
SHOW RESOURCE PLAN plan_name;
```
#### <a name="example"></a>Ejemplo 
```hql
SHOW RESOURCE PLAN demo_plan;
```

## <a name="show-resource-plans"></a>Show Resource Plans 
Muestra una lista de todos los planes de recursos. 
#### <a name="syntax"></a>Sintaxis 
```hql
SHOW RESOURCE PLANS;
```
#### <a name="example"></a>Ejemplo 
```hql
SHOW RESOURCE PLANS;
```
