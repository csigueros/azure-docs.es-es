---
title: Referencia de az arcdata dc endpoint
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az arcdata dc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9d07d5e5263fd887af49552bb45eb98ae7f35441
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845748"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | Muestra el punto de conexión del controlador de datos.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
Muestra el punto de conexión del controlador de datos.
```bash
az arcdata dc endpoint list 
```
### <a name="examples"></a>Ejemplos
Enumere todos los puntos de conexión de controlador de datos disponibles.
```bash
az arcdata dc endpoint list --k8s-namespace namespace
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
