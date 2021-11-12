---
title: az sql midb-arc
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql midb-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8053ea28fe1ce1f7f05c1bb710265fb86839dcd2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858481"
---
# <a name="az-sql-midb-arc"></a>az sql midb-arc
## <a name="commands"></a>Comandos
| Get-Help | Descripción|
| --- | --- |
[az sql midb-arc restore](#az-sql-midb-arc-restore) | Permite restaurar una base de datos en SQL Managed Instance habilitado para Azure Arc.
## <a name="az-sql-midb-arc-restore"></a>az sql midb-arc restore
Permite restaurar una base de datos en SQL Managed Instance habilitado para Azure Arc.
```bash
az sql midb-arc restore 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: restauración de una base de datos mediante la restauración a un momento dado.
```bash
az sql midb-arc restore --managed-instance sqlmi1 --name mysourcedb
 --dest-name mynewdb --time "2021-10-20T05:34:22Z" --k8s-namespace
 arc --use-k8s --dry-run
```
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Aumente el nivel de detalle de registro. Use `--debug` para los registros de depuración completos.
