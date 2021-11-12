---
title: Referencia de az sql mi-arc dag
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql mi-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f568f7086f2730bde53a1cd4dd31cf25092c5dd7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848655"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | Creación de un recurso personalizado de grupo de disponibilidad distribuido.
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | Eliminación de un recurso personalizado de grupo de disponibilidad distribuido en una instancia de sqlmi.
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | Presentación de un recurso personalizado de grupo de disponibilidad distribuido.
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
Creación de un recurso personalizado de grupo de disponibilidad distribuido para crear un grupo de disponibilidad distribuido.
```bash
az sql mi-arc dag create 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Creación de un recurso personalizado de grupo de disponibilidad distribuido, dagCr1, para crear el grupo de disponibilidad distribuido dagName1 entre la instancia local de sqlmi, sqlmi1, y la instancia remota de sqlmi, sqlmi2. Requiere el reflejo principal de sqlmi remoto, remotePrimary:5022, y el archivo de certificado del punto de conexión de reflejo de sqlmi remoto, ./sqlmi2.cer.
```bash
az sql mi-arc dag create --name dagCr1 --dag-name dagName1 --local-instance-name sqlmi1 --local-primary local --remote-instance-name sqlmi2 --remote-mirroring-url remotePrimary:5022 --remote-mirroring-cert-file ./sqlmi2.cer --use-k8s
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
Eliminación de un recurso personalizado de grupo de disponibilidad distribuido en una instancia de sqlmi para eliminar un grupo de disponibilidad distribuido. Se requiere un nombre de recurso personalizado.
```bash
az sql mi-arc dag delete 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Eliminación de recursos del grupo de disponibilidad distribuido llamado dagCr1.
```bash
az sql mi-arc dag delete --name dagCr1 --use-k8s
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
Presentación de un recurso personalizado de grupo de disponibilidad distribuido. Se requiere un nombre de recurso personalizado.
```bash
az sql mi-arc dag show 
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Presentación de recursos del grupo de disponibilidad distribuido llamado dagCr1.
```bash
az sql mi-arc dag show --name dagCr1 --use-k8s
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
