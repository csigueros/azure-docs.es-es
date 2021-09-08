---
title: Referencia de az arcdata dc config
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos de az arcdata dc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a7936f1bbe56b105ec65dfa76cb7346fa41953ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779969"
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
az arcdata dc config init [--path -p] 
                          [--source -s]  
                          
[--force -f]
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
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--path -p`
Ruta de acceso de archivo donde quiere colocar el perfil de configuración (se usa <cwd>/custom de forma predeterminada)
#### `--source -s`
Origen del perfil de configuración: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
#### `--force -f`
Obliga a sobrescribir el archivo de destino.
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
Muestra las opciones de perfil de configuración que se pueden usar en `arcdata dc config init`.
```bash
az arcdata dc config list [--config-profile -c] 
                          
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
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--config-profile -c`
Perfil de configuración predeterminado: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
Agrega el valor a la ruta de acceso JSON del archivo de configuración. Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az arcdata dc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>Ejemplos
Incorporación de almacenamiento del controlador de datos.
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta del archivo de configuración del controlador de datos de la configuración que se quiere establecer, es decir, custom/control.json
#### `--json-values -j`
Lista de pares de clave y valor de rutas json a valores: clave1.subclave1=valor1,clave2.subclave2=valor2. Puede proporcionar valores json insertados (como key='{"kind":"cluster","name":"test-cluster"}'), o bien puede especificar una ruta de archivo (por ejemplo, clave=./valores.json). El comando add NO admite condicionales.  Si el valor en línea facilitado es en sí mismo un par clave-valor con "=" y ",", añádales caracteres de escape.  Por ejemplo, key1="key2\=val2\,key3\=val3". En http://jsonpatch.com/ encontrará ejemplos de la apariencia de su ruta de acceso.  Para acceder a una matriz, necesita indicar del índice (por ejemplo, clave.0=valor).
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
Quita el valor de la ruta de acceso JSON del archivo de configuración.  Todos los ejemplos siguientes se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az arcdata dc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Eliminación de almacenamiento del controlador de datos.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta del archivo de configuración del controlador de datos de la configuración que se quiere establecer, es decir, custom/control.json
#### `--json-path -j`
Lista de rutas json basadas en la biblioteca jsonpatch que indica qué valores quiere quitar (por ejemplo, “clave1.subclave1,clave2.subclave2”). El comando remove NO admite condicionales. En http://jsonpatch.com/ encontrará ejemplos de la apariencia de su ruta de acceso.  Para acceder a una matriz, necesita indicar del índice (por ejemplo, clave.0=valor).
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
Reemplaza el valor de la ruta de acceso JSON del archivo de configuración.  Todos los ejemplos que figuran a continuación se proporcionan en Bash.  Si usa otra línea de comandos, puede que necesite añadir caracteres de escape a las comillas según corresponda.  Como alternativa, puede usar la función del archivo de revisión.
```bash
az arcdata dc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
Ruta del archivo de configuración del controlador de datos de la configuración que se quiere establecer, es decir, custom/control.json
#### `--json-values -j`
Lista de pares de clave y valor de rutas json a valores: clave1.subclave1=valor1,clave2.subclave2=valor2. Puede proporcionar valores json insertados (como key='{"kind":"cluster","name":"test-cluster"}'), o bien puede especificar una ruta de archivo (por ejemplo, clave=./valores.json). El comando replace admite condicionales mediante la biblioteca jsonpath.  Para usar esto, inicie la ruta con un signo de $. Esto permite agregar una declaración condicional, como -j $.key1.key2[?(@.key3=="someValue"].key4=value. Si el valor en línea facilitado es en sí mismo un par clave-valor con "=" y ",", añádales caracteres de escape.  Por ejemplo, key1="key2\=val2\,key3\=val3". A continuación, se muestran algunos ejemplos. Para obtener más ayuda, consulte https://jsonpath.com/.
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
Aplica una revisión al archivo de configuración según el archivo de revisión especificado. Para obtener más información sobre cómo se deben componer las rutas de acceso, consulte http://jsonpatch.com/. La operación de reemplazar puede usar declaraciones condicionales en la ruta debido a la biblioteca jsonpath https://jsonpath.com/. Todos los archivos json de revisión tienen que iniciarse con una clave de “patch”, que tiene una matriz de revisiones con la correspondiente operación (add [agregar], replace [reemplazar], remove [quitar]), ruta y valor. La operación “remove” (quitar) no necesita un valor, solo una ruta de acceso. Consulte los ejemplos siguientes.
```bash
az arcdata dc config patch --path 
                           --patch-file -p
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
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path`
Ruta del archivo de configuración del controlador de datos de la configuración que se quiere establecer, es decir, custom/control.json
#### `--patch-file -p`
Ruta a un archivo json de revisión basado en la biblioteca jsonpatch: http://jsonpatch.com/. Tiene que empezar el archivo json de revisión con una clave denominada “patch”, cuyo valor es una matriz de operaciones de revisión que quiere realizar. Para la ruta de una operación de revisión, puede usar la notación de puntos (como clave1.clave2) para la mayoría de las operaciones. Si no quiere realizar una operación de reemplazo y va a reemplazar un valor en una matriz que necesita una condicional, use la notación jsonpath (para hacerlo, agregue un signo de $ al principio de la ruta de acceso). Esto permite agregar una declaración condicional, como $.key1.key2[?(@.key3=="someValue"].key4. Consulte los ejemplos siguientes. Para obtener más ayuda con los condicionales, consulte https://jsonpath.com/.
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
