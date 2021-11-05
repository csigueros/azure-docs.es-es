---
title: Referencia de az arcdata dc debug
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az arcdata dc debug.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e230d788a06865a8868f90fb1195c9fb701c2750
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847800"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | Copie registros.
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | Volcado de memoria del desencadenador.
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
Copia los registros de depuración del controlador de datos; es necesario que Kubernetes esté configurado en el sistema.
```bash
az arcdata dc debug copy-logs 
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
Desencadene el volcado de registros y cópielo del contenedor; es necesario que Kubernetes esté configurado en el sistema.
```bash
az arcdata dc debug dump 
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
