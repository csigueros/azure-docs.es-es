---
title: az postgres arc-server reference
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az postgres arc-server.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9a5746f4a18cf0df2045c22c58cf26a9aa44fc74
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845710"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Cree un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Edite la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Elimine un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server show](#az-postgres-arc-server-show) | Muestre los detalles de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server list](#az-postgres-arc-server-list) | Enumere los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Permite administrar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
Para establecer la contraseña del grupo de servidores, establezca la variable de entorno AZDATA_PASSWORD.
```bash
az postgres arc-server create 
```
### <a name="examples"></a>Ejemplos
Cree un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
Cree un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc con configuración de motor. Los dos ejemplos siguientes son válidos.
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
Cree un grupo de servidores de PostgreSQL con montajes de notificaciones de volumen.
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
Cree un grupo de servidores PostgreSQL con un límite de memoria específico para distintos roles de nodo.
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Edite la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server edit 
```
### <a name="examples"></a>Ejemplos
Edite la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
Edite un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc con configuración de motor para el nodo de coordinación.
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Edite un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc y reemplace la configuración de motor existente por la nueva configuración "key1=val1".
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Elimine un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server delete 
```
### <a name="examples"></a>Ejemplos
Elimine un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Muestre los detalles de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server show 
```
### <a name="examples"></a>Ejemplos
Muestre los detalles de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Enumere los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server list 
```
### <a name="examples"></a>Ejemplos
Enumere los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
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
