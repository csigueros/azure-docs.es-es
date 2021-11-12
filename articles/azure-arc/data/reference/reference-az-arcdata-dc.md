---
title: Referencia de az arcdata dc
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az arcdata dc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 23cbe9c98e3c0ad86704061dadfad0e83f627ac3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852967"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | Crear un controlador de datos.
[az arcdata dc upgrade](#az-arcdata-dc-upgrade) | Actualizar el controlador de datos.
[az arcdata dc list-upgrades](#az-arcdata-dc-list-upgrades) | Enumerar las versiones de actualización disponibles.
[az arcdata dc delete](#az-arcdata-dc-delete) | Eliminar un controlador de datos.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | Comandos de punto de conexión.
[az arcdata dc status](reference-az-arcdata-dc-status.md) | Comandos de estado.
[az arcdata dc config](reference-az-arcdata-dc-config.md) | Comandos de configuración.
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | Depurar controlador de datos.
[az arcdata dc export](#az-arcdata-dc-export) | Exportar métricas, registros o utilización.
[az arcdata dc upload](#az-arcdata-dc-upload) | Cargar el archivo de datos exportado.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
Crear el controlador de datos. Se requiere kubeconfig en el sistema junto con las credenciales de los paneles de supervisión proporcionados por las siguientes variables de entorno: AZDATA_LOGSUI_USERNAME y AZDATA_LOGSUI_PASSWORD para el panel de registros, y AZDATA_METRICSUI_USERNAME y AZDATA_METRICSUI_PASSWORD para el panel de métricas. Como alternativa, AZDATA_USERNAME y AZDATA_PASSWORD se usarán como reserva si faltan conjuntos de variables de entorno.
```bash
az arcdata dc create 
```
### <a name="examples"></a>Ejemplos
Implemente un controlador de datos conectado indirectamente.
```bash
az arcdata dc create --name name --k8s-namespace namespace --connectivity-mode indirect --resource-group group  --location location --subscription subscription --use-k8s
```
Implemente un controlador de datos conectado directamente.
```bash
az arcdata dc create --name name  --connectivity-mode direct --resource-group group  --location location --subscription subscription  --custom-location custom-location         
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
## <a name="az-arcdata-dc-upgrade"></a>az arcdata dc upgrade
Actualice el controlador de datos a la versión deseada especificada.  Si la versión deseada no se ha especificado, se intentará actualizar a la versión más reciente. Si no está seguro de la versión deseada, puede usar el comando list-upgrades para ver las versiones disponibles o usar el argumento --dry-run para mostrar la versión que se usaría.
```bash
az arcdata dc upgrade 
```
### <a name="examples"></a>Ejemplos
Actualización del controlador de datos.
```bash
az arcdata dc upgrade --k8s-namespace namespace --use-k8s
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
## <a name="az-arcdata-dc-list-upgrades"></a>az arcdata dc list-upgrades
Intenta enumerar las versiones que están disponibles en el registro de imágenes de Docker para su actualización. - Se requiere kubeconfig en el sistema junto con las variables de entorno siguientes: ["AZDATA_USERNAME", "AZDATA_PASSWORD"].
```bash
az arcdata dc list-upgrades 
```
### <a name="examples"></a>Ejemplos
Actualización del controlador de datos.
```bash
az arcdata dc list-upgrades --k8s-namespace namespace --use-k8s            
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
Elimina el controlador de datos: se requiere kubeconfig en el sistema.
```bash
az arcdata dc delete 
```
### <a name="examples"></a>Ejemplos
Elimine un controlador de datos conectado indirectamente.
```bash
az arcdata dc delete --name name --k8s-namespace namespace --use-k8s
```
Elimine un controlador de datos conectado directamente.
```bash
az arcdata dc delete --name name --resource-group resource-group            
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
Exporta métricas, registros o utilización a un archivo.
```bash
az arcdata dc export 
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
Carga el archivo de datos exportado desde un controlador de datos a Azure.
```bash
az arcdata dc upload 
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
