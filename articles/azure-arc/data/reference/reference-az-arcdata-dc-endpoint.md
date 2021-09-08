---
title: Referencia de az arcdata dc endpoint
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az arcdata dc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c024050777d01bb93fbbcba63a636d4e34c163b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779968"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | Muestra el punto de conexión del controlador de datos.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
Muestra el punto de conexión del controlador de datos.
```bash
az arcdata dc endpoint list --k8s-namespace -k 
                            [--endpoint-name -e]  
                            
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Enumere todos los puntos de conexión de controlador de datos disponibles.
```bash
az arcdata dc endpoint list --k8s-namespace namespace
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes en el que existe el controlador de datos.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--endpoint-name -e`
Nombre del punto de conexión del controlador de datos de Arc.
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
