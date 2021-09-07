---
title: Referencia de az postgres arc-server endpoint
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az postgres arc-server.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8b3bf65f4e38177712f6deb333ba5d4994542b2f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780875"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az postgres arc-server endpoint list](#az-postgres-arc-server-endpoint-list) | Permite enumerar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint list
Permite enumerar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server endpoint list [--name -n] 
                                     [--k8s-namespace -k]  
                                     
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Permite enumerar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--name -n`
Nombre del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
#### `--k8s-namespace -k`
Espacio de nombres de Kubernetes donde se implementa el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si no se especifica ningún espacio de nombres, se utilizará el definido en kubeconfig.
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
