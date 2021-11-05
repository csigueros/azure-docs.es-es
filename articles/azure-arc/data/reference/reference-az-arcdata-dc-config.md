---
title: Referencia de az arcdata dc config
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az arcdata dc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 72df8db00f1ab0366cc4daf8af8e5613645fc813
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852529"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | Inicializa un perfil de configuración de controlador de datos que puede usarse con `az arcdata dc create`.
[az arcdata dc config list](#az-arcdata-dc-config-list) | Muestra las opciones de perfil de configuración disponibles.
[az arcdata dc config add](#az-arcdata-dc-config-add) | Agrega un valor a una ruta de acceso json en un archivo de configuración.
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | Quita un valor de una ruta de acceso json en un archivo de configuración.
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | Reemplaza un valor de una ruta de acceso json en un archivo de configuración.
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | Aplica una revisión a un archivo de configuración basándose en un archivo de revisión JSON.
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
Inicializa un perfil de configuración de controlador de datos que puede usarse con `az arcdata dc create`. El origen concreto del perfil de configuración se puede especificar en los argumentos.
```bash
az arcdata dc config init 
```
### <a name="examples"></a>Ejemplos
Experiencia de inicialización de configuración de controlador de datos guiada: se le van a solicitar los valores necesarios.
```bash
az arcdata dc config init
```
arcdata dc config init con argumentos, crea un perfil de configuración de aks-dev-test en ./custom.
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
Muestra las opciones de perfil de configuración que se pueden usar en `arcdata dc config init`.
```bash
az arcdata dc config list 
```
### <a name="examples"></a>Ejemplos
Muestra todos los nombres disponibles del perfil de configuración.
```bash
az arcdata dc config list
```
Muestra código json de un perfil de configuración específico.
```bash
az arcdata dc config list --config-profile aks-dev-test
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
Agrega el valor a la ruta de acceso JSON del archivo de configuración. Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az arcdata dc config add 
```
### <a name="examples"></a>Ejemplos
Incorporación de almacenamiento del controlador de datos.
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
Quita el valor de la ruta de acceso JSON del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az arcdata dc config remove 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Eliminación de almacenamiento del controlador de datos.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
Reemplaza el valor de la ruta de acceso JSON del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az arcdata dc config replace 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Reemplazo del puerto de un único punto de conexión (punto de conexión del controlador de datos).
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Ejemplo 2: Reemplazo de almacenamiento del controlador de datos.
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
Aplica una revisión al archivo de configuración según el archivo de revisión especificado. Para obtener más información sobre cómo se deben componer las rutas de acceso, consulte http://jsonpatch.com/. La operación de reemplazar puede usar declaraciones condicionales en la ruta debido a la biblioteca jsonpath https://jsonpath.com/. Todos los archivos json de revisión tienen que iniciarse con una clave de “patch”, que tiene una matriz de revisiones con la correspondiente operación (add [agregar], replace [reemplazar], remove [quitar]), ruta y valor. La operación “remove” (quitar) no necesita un valor, solo una ruta de acceso. Consulte los ejemplos siguientes.
```bash
az arcdata dc config patch 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Reemplazo del puerto de un único punto de conexión (punto de conexión del controlador de datos) por un archivo de revisión.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Ejemplo 2: Reemplazo de almacenamiento del controlador de datos por un archivo de revisión.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

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
