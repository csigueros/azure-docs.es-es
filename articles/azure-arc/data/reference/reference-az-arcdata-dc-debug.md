---
title: Referencia de az arcdata dc debug
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az arcdata dc debug.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 219471cf793394c024061f39319a98cc89477c76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780200"
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
az arcdata dc debug copy-logs --k8s-namespace -k 
                              [--container -c]  
                              
[--target-folder -d]  
                              
[--pod]  
                              
[--resource-kind]  
                              
[--resource-name]  
                              
[--timeout -t]  
                              
[--skip-compress]  
                              
[--exclude-dumps]  
                              
[--exclude-system-logs ]  
                              
[--use-k8s]
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--k8s-namespace -k`
Espacio de nombres de Kubernetes del controlador de datos.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--container -c`
Copie los registros de los contenedores con un nombre similar. Opcional, de forma predeterminada, copia los registros de todos los contenedores. No se puede especificar varias veces. Si se especifica varias veces, se usará la última.
#### `--target-folder -d`
Ruta de acceso de la carpeta de destino en la que copiar los registros. Opcional, de forma predeterminada, crea el resultado en la carpeta local.  No se puede especificar varias veces. Si se especifica varias veces, se usará la última.
#### `--pod`
Copie los registros de los pods con un nombre similar. Opcional, de forma predeterminada copia los registros de todos los pods. No se puede especificar varias veces. Si se especifica varias veces, se usará la última.
#### `--resource-kind`
Copia los registros para el recurso de un tipo determinado. No se puede especificar varias veces. Si se especifica varias veces, se usará la última. Si se especifica, también se debe especificar--resource-name para identificar el recurso.
#### `--resource-name`
Copia los registros para el recurso del nombre especificado. No se puede especificar varias veces. Si se especifica varias veces, se usará la última. Si se especifica, también se debe especificar--resource-kind para identificar el recurso.
#### `--timeout -t`
Número de segundos que se debe esperar a que se complete el comando. El valor predeterminado es 0, es decir, sin límite.
#### `--skip-compress`
Indica si se omite o no la compresión de la carpeta de resultados. El valor predeterminado es False, que comprime la carpeta de resultados.
#### `--exclude-dumps`
Indica si se excluyen o no los volcados de la carpeta de resultados. El valor predeterminado es False, que incluye los volcados.
#### `--exclude-system-logs `
Indica si se excluyen o no los registros del sistema de la colección. El valor predeterminado es "false", que incluye los registros del sistema.
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
Desencadene el volcado de registros y cópielo del contenedor; es necesario que Kubernetes esté configurado en el sistema.
```bash
az arcdata dc debug dump --k8s-namespace -k 
                         [--container -c]  
                         
[--target-folder -d]  
                         
[--use-k8s]
```
### <a name="required-parameters"></a>Parámetros obligatorios
#### `--k8s-namespace -k`
Espacio de nombres de Kubernetes del controlador de datos.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--container -c`
Contenedor de destino que se va a desencadenar para volcar los procesos en ejecución.
`controller`
#### `--target-folder -d`
Carpeta de destino en la que se va a copiar el volcado. `./output/dump`
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
