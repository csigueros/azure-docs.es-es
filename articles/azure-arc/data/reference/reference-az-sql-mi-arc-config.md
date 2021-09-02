---
title: Referencia de az sql mi-arc config
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql mi-arc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a8fad1be3245792c6967f56c9026bf8efee4d6e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780459"
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
az sql mi-arc config init --path -p 
                          
```
### <a name="examples"></a>Ejemplos
Inicializa los archivos CRD y de especificación de una instancia administrada de SQL.
```bash
az sql mi-arc config init --path ./template
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta de acceso donde se deben escribir los archivos CRD y de especificación de la instancia administrada de SQL.
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
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
Agrega el valor a la ruta de acceso json del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az sql mi-arc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Adición de almacenamiento.
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta de acceso a la especificación de recursos personalizada, es decir, custom/spec.json.
#### `--json-values -j`
Lista de pares de clave y valor de rutas json a valores: clave1.subclave1=valor1,clave2.subclave2=valor2. Puede proporcionar valores json insertados (como key='{"kind":"cluster","name":"test-cluster"}'), o bien puede especificar una ruta de archivo (por ejemplo, clave=./valores.json). El comando "add" NO admite condicionales.  Si el valor en línea facilitado es en sí mismo un par clave-valor con "=" y ",", escape esos caracteres.  Por ejemplo, key1="key2\=val2\,key3\=val3". Vea ejemplos de la apariencia de la ruta en http://jsonpatch.com/.  Para acceder a una matriz, necesita indicar del índice (por ejemplo, clave.0=valor).
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
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
Quita el valor de la ruta de acceso json del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az sql mi-arc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Eliminación de almacenamiento.
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta de acceso a la especificación de recursos personalizada, es decir, custom/spec.json.
#### `--json-path -j`
Lista de rutas json basadas en la biblioteca jsonpatch que indica qué valores quiere quitar (por ejemplo, “clave1.subclave1,clave2.subclave2”). El comando "remove" NO admite condicionales. Vea ejemplos de la apariencia de la ruta en http://jsonpatch.com/.  Para acceder a una matriz, necesita indicar del índice (por ejemplo, clave.0=valor).
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
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
Reemplaza el valor en la ruta de acceso json del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az sql mi-arc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta de acceso a la especificación de recursos personalizada, es decir, custom/spec.json.
#### `--json-values -j`
Lista de pares de clave y valor de rutas json a valores: clave1.subclave1=valor1,clave2.subclave2=valor2. Puede proporcionar valores json insertados (como key='{"kind":"cluster","name":"test-cluster"}'), o bien puede especificar una ruta de archivo (por ejemplo, clave=./valores.json). El comando replace admite condicionales mediante la biblioteca jsonpath.  Para usar esto, inicie la ruta con un signo de $. Esto permite agregar una declaración condicional, como -j $.key1.key2[?(@.key3=="someValue"].key4=value. Si el valor en línea facilitado es en sí mismo un par clave-valor con "=" y ",", escape esos caracteres.  Por ejemplo, key1="key2\=val2\,key3\=val3". A continuación, se muestran algunos ejemplos. Para obtener ayuda, vea: https://jsonpath.com/
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
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
Aplica una revisión en el archivo de configuración según el archivo de revisión especificado. Para obtener información sobre cómo crear las rutas de acceso, consulte http://jsonpatch.com/. La operación de reemplazar puede usar declaraciones condicionales en la ruta debido a la biblioteca jsonpath https://jsonpath.com/. Todos los archivos json de revisión tienen que iniciarse con una clave de “patch”, que tiene una matriz de revisiones con la correspondiente operación (add [agregar], replace [reemplazar], remove [quitar]), ruta y valor. La operación “remove” (quitar) no necesita un valor, solo una ruta de acceso. Consulte los ejemplos siguientes.
```bash
az sql mi-arc config patch --path -p 
                           --patch-file
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
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta de acceso a la especificación de recursos personalizada, es decir, custom/spec.json.
#### `--patch-file`
Ruta a un archivo json de revisión basado en la biblioteca jsonpatch: http://jsonpatch.com/. Tiene que empezar el archivo json de revisión con una clave denominada “patch”, cuyo valor es una matriz de operaciones de revisión que quiere realizar. Para la ruta de una operación de revisión, puede usar la notación de puntos (como clave1.clave2) para la mayoría de las operaciones. Si no quiere realizar una operación de reemplazo y va a reemplazar un valor en una matriz que necesita una condicional, use la notación jsonpath (para hacerlo, agregue un signo de $ al principio de la ruta de acceso). Esto permite agregar una declaración condicional, como $.key1.key2[?(@.key3=="someValue"].key4. A continuación, se muestran algunos ejemplos. Para obtener más información sobre las declaraciones condicionales, vea: https://jsonpath.com/.
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
