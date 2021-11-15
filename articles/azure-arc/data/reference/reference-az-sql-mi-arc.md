---
title: Referencia de az sql mi-arc
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql mi-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 0a98cd873de2da0daaf793a2e40df5c4d5c38285
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852301"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | Permite ver y administrar puntos de conexión de SQL.
[az sql mi-arc create](#az-sql-mi-arc-create) | Crea una instancia de SQL Managed Instance.
[az sql mi-arc update](#az-sql-mi-arc-update) | Actualiza la configuración de una instancia administrada de SQL.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | Elimina una instancia de SQL Managed Instance.
[az sql mi-arc show](#az-sql-mi-arc-show) | Muestra los detalles de una instancia de SQL Managed Instance.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | Recupera el certificado del punto de conexión de creación de reflejo del grupo de disponibilidad de SQL MI y lo almacena en un archivo.
[az sql mi-arc upgrade](#az-sql-mi-arc-upgrade) | Actualiza la instancia administrada de SQL.
[az sql mi-arc list](#az-sql-mi-arc-list) | Enumera instancias de SQL Managed Instance.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | Comandos de configuración.
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | Crea o elimina un grupo de disponibilidad distribuido.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
Para establecer la contraseña de la instancia de SQL Managed Instance, establezca la variable de entorno AZDATA_PASSWORD.
```bash
az sql mi-arc create 
```
### <a name="examples"></a>Ejemplos
Cree una instancia administrada de SQL conectada indirectamente.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace --use-k8s
```
Cree una instancia administrada de SQL conectada indirectamente con tres réplicas en el escenario de alta disponibilidad.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3  --k8s-namespace namespace --use-k8s
```
Cree una instancia administrada de SQL conectada directamente.
```bash
az sql mi-arc create --name name --resource-group group  --location location --subscription subscription   --custom-location custom-location
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
## <a name="az-sql-mi-arc-update"></a>az sql mi-arc update
Actualiza la configuración de una instancia administrada de SQL.
```bash
az sql mi-arc update 
```
### <a name="examples"></a>Ejemplos
Actualiza la configuración de una instancia administrada de SQL.
```bash
az sql mi-arc update --path ./spec.json -n sqlmi1 --use-k8s
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
Elimina una instancia de SQL Managed Instance.
```bash
az sql mi-arc delete 
```
### <a name="examples"></a>Ejemplos
Elimine una instancia administrada de SQL con el espacio de nombres proporcionado.
```bash
az sql mi-arc delete --name sqlmi1 --k8s-namespace namespace --use-k8s
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
Muestra los detalles de una instancia de SQL Managed Instance.
```bash
az sql mi-arc show 
```
### <a name="examples"></a>Ejemplos
Muestre los detalles de una instancia administrada de SQL conectada indirectamente.
```bash
az sql mi-arc show --name sqlmi1 --k8s-namespace namespace --use-k8s
```
Muestre los detalles de una instancia administrada de SQL conectada directamente.
```bash
az sql mi-arc show --name sqlmi1 --resource-group resource-group            
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
Recupera el certificado del punto de conexión de creación de reflejo del grupo de disponibilidad de SQL MI y lo almacena en un archivo.
```bash
az sql mi-arc get-mirroring-cert 
```
### <a name="examples"></a>Ejemplos
Recupera el certificado del punto de conexión de creación de reflejo de sqlmi1 y lo almacena en el archivo fileName1.
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
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
## <a name="az-sql-mi-arc-upgrade"></a>az sql mi-arc upgrade
Actualice la instancia administrada de SQL a la versión deseada especificada.  Si no se especifica la versión deseada, se utilizará la versión del controlador de datos.
```bash
az sql mi-arc upgrade 
```
### <a name="examples"></a>Ejemplos
Actualiza la instancia administrada de SQL.
```bash
az sql mi-arc upgrade -n sqlmi1 -k arc --desired-version v1.1.0 --use-k8s
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
Enumera instancias de SQL Managed Instance.
```bash
az sql mi-arc list 
```
### <a name="examples"></a>Ejemplos
Enumera instancias de SQL Managed Instance.
```bash
az sql mi-arc list --use-k8s
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
