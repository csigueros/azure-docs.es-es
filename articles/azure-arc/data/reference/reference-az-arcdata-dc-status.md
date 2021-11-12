---
title: Referencia de az arcdata dc status
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az arcdata dc status.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 542f0f9568150abf0646e1a494e14eec2be6e524
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841643"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | Muestra el estado del controlador de datos.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
Muestra el estado del controlador de datos.
```bash
az arcdata dc status show 
```
### <a name="examples"></a>Ejemplos
Muestra el estado del controlador de datos en un espacio de nombres de Kubernetes determinado.
```bash
az arcdata dc status show --k8s-namespace namespace --use-k8s
```
Muestra el estado de un controlador de datos conectado directamente en un grupo de recursos determinado.
```bash
az arcdata dc status show --resource-group resource-group    
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
