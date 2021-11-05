---
title: Referencia de az postgres arc-server endpoint
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az postgres arc-server.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 78111c030c1641cab22664d889e26505de879cf7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847705"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az postgres arc-server endpoint list](#az-postgres-arc-server-endpoint-list) | Permite enumerar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint list
Permite enumerar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server endpoint list 
```
### <a name="examples"></a>Ejemplos
Permite enumerar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
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
