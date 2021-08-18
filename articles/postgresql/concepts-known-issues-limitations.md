---
title: 'Problemas conocidos y limitaciones de Azure Database for PostgreSQL: servidor único y servidor flexible (versión preliminar)'
description: Enumera los problemas conocidos que los clientes deben tener en cuenta.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 0806ef1a191759e769271a97fb1809298cde10db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725271"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Problemas conocidos y limitaciones de Azure Database for PostgreSQL

En esta página se proporciona una lista de problemas conocidos en Azure Database for PostgreSQL que podrían afectar a la aplicación. También se enumeran las mitigaciones y recomendaciones para solucionar el problema.

## <a name="intelligent-performance---query-store"></a>Rendimiento inteligente del almacén de consultas

Se aplica a Azure Database for PostgreSQL: servidor único.

| Aplicable | Causa | Corrección|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | La activación del parámetro de servidor `pg_qs.replace_parameter_placeholders` puede provocar un cierre del servidor en algunos escenarios poco comunes. | A través de la sección Parámetros del servidor de Azure Portal, convierta el valor del parámetro `pg_qs.replace_parameter_placeholders` en `OFF` y guárdelo.   | 


## <a name="next-steps"></a>Pasos siguientes
- Consulte los [procedimientos recomendados](./concepts-query-store-best-practices.md) del almacén de consultas
