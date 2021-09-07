---
title: Referencia de az arcdata resource-kind
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de azdata arc resource-kind.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 597bc660140ec2f0ac856a3f6c67db600480aa63
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780876"
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
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Obtiene el archivo de plantilla del tipo de recurso de Arc.
```bash
az arcdata resource-kind get --kind -k 
                             [--dest -d]
```
### <a name="examples"></a>Ejemplos
Comando de ejemplo para obtener un archivo de plantilla CRD del tipo de recurso de Arc.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--kind -k`
Tipo de recurso de Arc para el que quiere el archivo de plantilla.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--dest -d`
Directorio en el que quiere colocar los archivos de plantilla.
`template`
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
