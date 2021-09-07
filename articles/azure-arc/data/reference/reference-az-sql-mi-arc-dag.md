---
title: Referencia de az sql mi-arc dag
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql mi-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4ebd6e71c14c9a319e99e68eca15f19a21d52033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781243"
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
az sql mi-arc dag create --name -n 
                         --dag-name -d  
                         
--local-instance-name -l  
                         
--local-primary -p  
                         
--remote-instance-name -r  
                         
--remote-mirroring-url -u  
                         
--remote-mirroring-cert-file -f  
                         
[--k8s-namespace -k]  
                         
[--path]  
                         
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Creación de un recurso personalizado de grupo de disponibilidad distribuido, dagCr1, para crear el grupo de disponibilidad distribuido dagName1 entre la instancia local de sqlmi, sqlmi1, y la instancia remota de sqlmi, sqlmi2. Requiere el reflejo principal de sqlmi remoto, remotePrimary:5022, y el archivo de certificado del punto de conexión de reflejo de sqlmi remoto, ./sqlmi2.cer.
```bash
az sql mi-arc dag create --name=dagCr1 --dag-name=dagName1  --local-instance-name=sqlmi1 --local-primary=true --remote-instance-name=sqlmi2  --remote-mirroring-url==remotePrimary:5022 --remote-mirroing-cert-file="./sqlmi2.cer"
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre del recurso de grupo de disponibilidad distribuido.
#### `--dag-name -d`
Nombre del grupo de disponibilidad distribuido de esta instancia administrada de SQL. Tanto si es local como remota, tienen que usar el mismo nombre.
#### `--local-instance-name -l`
Nombre de la instancia administrada de SQL local.
#### `--local-primary -p`
True indica que la instancia administrada de SQL local es la región geográfica primaria. False indica que la instancia administrada de SQL local es la región geográfica secundaria.
#### `--remote-instance-name -r`
Nombre de la instancia administrada de SQL remota o del grupo de disponibilidad de SQL remoto.
#### `--remote-mirroring-url -u`
Dirección URL del punto de conexión de reflejo de la instancia administrada de SQL remota o del grupo de disponibilidad de SQL remoto.
#### `--remote-mirroring-cert-file -f`
Nombre de archivo del certificado público del punto de conexión de reflejo de la instancia administrada de SQL remota o del grupo de disponibilidad de SQL remoto. Solo se admite el formato PEM.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--path`
Ruta de acceso a la especificación de recursos personalizada, es decir, custom/spec.json.
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
Eliminación de un recurso personalizado de grupo de disponibilidad distribuido en una instancia de sqlmi para eliminar un grupo de disponibilidad distribuido. Se requiere un nombre de recurso personalizado.
```bash
az sql mi-arc dag delete --name 
                         [--k8s-namespace -k]  
                         
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Eliminación de recursos del grupo de disponibilidad distribuido llamado dagCr1.
```bash
az sql mi-arc dag delete --name=dagCr1
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name`
Nombre del recurso de grupo de disponibilidad distribuido.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
Presentación de un recurso personalizado de grupo de disponibilidad distribuido. Se requiere un nombre de recurso personalizado.
```bash
az sql mi-arc dag show --name 
                       [--k8s-namespace -k]  
                       
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Ejemplo 1: Presentación de recursos del grupo de disponibilidad distribuido llamado dagCr1.
```bash
az sql mi-arc dag show --name=dagCr1
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name`
Nombre del recurso de grupo de disponibilidad distribuido.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
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
