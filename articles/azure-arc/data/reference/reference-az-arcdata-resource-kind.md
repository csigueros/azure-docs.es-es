---
title: Referencia de az arcdata resource-kind
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de azdata arc resource-kind.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ad138bfc322aa109078e622b26d1f5254ec789ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852989"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | Enumera los tipos de recursos personalizados disponibles para Arc que se pueden definir y crear.
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | Obtiene el archivo de plantilla del tipo de recurso de Arc.
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
Enumera los tipos de recursos personalizados disponibles para Arc que se pueden definir y crear. Después de la enumeración, puede pasar a obtener el archivo de plantilla necesario para definir o crear ese recurso personalizado.
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>Ejemplos
Comando de ejemplo para enumerar los tipos de recursos personalizados disponibles para Arc.
```bash
az arcdata resource-kind list
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Obtiene el archivo de plantilla del tipo de recurso de Arc.
```bash
az arcdata resource-kind get 
```
### <a name="examples"></a>Ejemplos
Comando de ejemplo para obtener un archivo de plantilla CRD del tipo de recurso de Arc.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
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
