---
title: Eventos de auditoría de Azure SQL Database con tablas habilitadas para libro de contabilidad
description: Introducción a las funcionalidades de auditoría del libro de contabilidad de Azure SQL Database
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 18e3c5ef5d9cb0316118e38f2ffb8dccc8c06fd7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061875"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Eventos de auditoría de Azure SQL Database con tablas habilitadas para libro de contabilidad

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> El libro de contabilidad de Azure SQL Database se encuentra actualmente en versión preliminar pública.

Al realizar actividades forenses con tablas habilitadas para libro de contabilidad, los datos se capturan en la vista de libro de contabilidad y en libro de contabilidad de base de datos. También se agregan otros id. de acción a los registros de auditoría SQL. En las tablas siguientes se describen estos nuevos eventos de registro de auditoría. Las condiciones que desencadenan los eventos van a continuación de cada tabla.

## <a name="enable-ledger"></a>Habilitar libro de contabilidad

| Columna | Valor |
|--|--|
| **action_id** | ENLR |
| **name** | ENABLE LEDGER  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Condiciones que desencadenan el evento**: cuando se crea una nueva tabla de libro de contabilidad o se convierte una tabla normal en una tabla de libro de contabilidad.

## <a name="alter-ledger"></a>Modificar libro de contabilidad

| Columna | Valor |
|--|--|
| **action_id** | ALLR |
| **name** | ALTER LEDGER |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Condiciones que desencadenan el evento**: al quitar o cambiar el nombre de una tabla de libro de contabilidad, convertir una tabla de libro de contabilidad en una tabla normal y agregar, quitar o cambiar el nombre de una columna de una tabla de libro de contabilidad.


## <a name="generate-ledger-digest"></a>Generar resumen de libro de contabilidad

| Columna | Valor |
|--|--|
| **action_id** | GDLR |
| **name** | GENERATE LEDGER DIGEST |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**Condición que desencadena el evento**: cuando se genera un resumen del libro de contabilidad.

## <a name="verify-ledger"></a>Comprobar libro de contabilidad

| Columna | Valor |
|--|--|
| **action_id** | VFLR |
| **name** | VERIFY LEDGER |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Condición que desencadena el evento**: al comprobar un resumen del libro de contabilidad.

## <a name="ledger-operation-group"></a>Grupo de operaciones de libro de contabilidad

| Columna | Valor |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Condición que desencadena el evento**: N/D

| Columna | Valor |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Condición que desencadena el evento**: N/D 

## <a name="next-steps"></a>Pasos siguientes

- [Auditoría para Azure SQL Database y Azure Synapse Analytics](auditing-overview.md)
- [Introducción al libro de contabilidad de Azure SQL Database](ledger-overview.md)
- [Inicio rápido: Creación de una base de datos de Azure SQL Database con el libro de contabilidad habilitado](ledger-create-a-single-database-with-ledger-enabled.md)
