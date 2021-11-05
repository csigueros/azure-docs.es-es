---
title: Referencia de az sql mi-arc endpoint
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az sql mi-arc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: db7c1fb6366cda40a5978a7be55100ae7a12ac4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852409"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | Enumera los puntos de conexión de SQL.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
Enumera los puntos de conexión de SQL.
```bash
az sql mi-arc endpoint list 
```
### <a name="examples"></a>Ejemplos
Enumere los puntos de conexión de una instancia administrada de SQL.
```bash
az sql mi-arc endpoint list -n sqlmi1
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
