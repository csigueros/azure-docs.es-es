---
title: Referencia de az sql mi-arc config
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql mi-arc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 2dbe786f8a33528d028b4dc5f5476fa6f38a62b4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845688"
---
# <a name="az-sql-mi-arc-config"></a>az sql mi-arc config
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az sql mi-arc config init](#az-sql-mi-arc-config-init) | Inicializa los archivos CRD y de especificación de una instancia administrada de SQL.
[az sql mi-arc config add](#az-sql-mi-arc-config-add) | Agrega un valor a una ruta de acceso json en un archivo de configuración.
[az sql mi-arc config remove](#az-sql-mi-arc-config-remove) | Quita un valor de una ruta de acceso json en un archivo de configuración.
[az sql mi-arc config replace](#az-sql-mi-arc-config-replace) | Reemplaza un valor de una ruta de acceso json en un archivo de configuración.
[az sql mi-arc config patch](#az-sql-mi-arc-config-patch) | Aplica una revisión en un archivo de configuración basándose en un archivo de revisión json.
## <a name="az-sql-mi-arc-config-init"></a>az sql mi-arc config init
Inicializa los archivos CRD y de especificación de una instancia administrada de SQL.
```bash
az sql mi-arc config init 
```
### <a name="examples"></a>Ejemplos
Inicializa los archivos CRD y de especificación de una instancia administrada de SQL.
```bash
az sql mi-arc config init --path ./template
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
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
Agrega el valor a la ruta de acceso json del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az sql mi-arc config add 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Adición de almacenamiento.
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
Quita el valor de la ruta de acceso json del archivo de configuración. Todos los ejemplos siguientes se proporcionan en Bash. Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda. Como alternativa, puede usar la función del archivo de revisión.
```bash
az sql mi-arc config remove 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Eliminación de almacenamiento.
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
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
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
Reemplaza el valor en la ruta de acceso json del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az sql mi-arc config replace 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Reemplazo del puerto de un único punto de conexión.
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Ejemplo 2: Reemplazo del almacenamiento.
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
Aplica una revisión en el archivo de configuración según el archivo de revisión especificado. Para obtener más información sobre cómo se deben componer las rutas de acceso, consulte http://jsonpatch.com/. La operación de reemplazar puede usar declaraciones condicionales en la ruta debido a la biblioteca jsonpath https://jsonpath.com/. Todos los archivos JSON de revisión tienen que iniciarse con una clave de `patch`, que tiene una matriz de revisiones con la operación (add [agregar], replace [reemplazar], remove [quitar]), ruta y valor correspondientes. La operación `remove` (quitar) no necesita un valor, solo una ruta de acceso. Consulte los ejemplos siguientes.
```bash
az sql mi-arc config patch 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Reemplazo del puerto de un único punto de conexión por un archivo de revisión.
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Ejemplo 2: Reemplazo del almacenamiento por un archivo de revisión.
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
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
