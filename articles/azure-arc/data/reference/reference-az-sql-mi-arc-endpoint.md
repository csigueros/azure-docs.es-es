---
title: Referencia de az sql mi-arc endpoint
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az sql mi-arc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ca3685c6769e45d43b513ea11355795af332a849
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780481"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | Enumera los puntos de conexión de SQL.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
Enumera los puntos de conexión de SQL.
```bash
az sql mi-arc endpoint list [--name -n] 
                            [--k8s-namespace -k]  
                            
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Enumere los puntos de conexión de una instancia administrada de SQL.
```bash
az sql mi-arc endpoint list -n sqlmi1
```
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--name -n`
Especifica el nombre de la instancia de SQL que se va a mostrar. Si se omite, se mostrarán todos los puntos de conexión de todas las instancias.
#### `--k8s-namespace -k`
Espacio de nombres en el que existen las instancias administradas de SQL. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--use-k8s`
Use las API de Kubernetes locales para realizar esta acción.
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
