---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: advanced_state_management
description: Actualiza el archivo de estados de Terraform mediante un script de shell
ms.openlocfilehash: 40a7810227802415fa69578936f00d7715d40191
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725283"
---
# <a name="advanced_state_managementsh"></a>advanced_state_management.sh

## <a name="synopsis"></a>Sinopsis
Actualiza el archivo de estados de Terraform.

## <a name="syntax"></a>Sintaxis

```bash

advanced_state_management.sh [--parameterfile] <String> 
[--type] <String> 
[--terraform_keyfile] <String>
[--subscription] <String> 
[--storage_account_name] <String> 
[--tf_resource_name] <String>
[--azure_resource_id] <String> 
[--help]
```

## <a name="description"></a>Descripción
Puede usar este cmdlet para agregar recursos que faltan o modificados al archivo de estados de Terraform.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

```bash

advanced_state_management.sh --parameterfile DEV-WEEU-SAP01-X00.tfvars --type sap_system
 --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
 --storage_account_name devweeutfstateABC 
 --terraform_keyfile DEV-WEEU-SAP01-X00.terraform.tfstate 
 --tf_resource_name module.sap_system.azurerm_resource_group.deployer[0] 
 --azure_resource_id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/DEV-WEEU-SAP01-X00
```

## <a name="parameters"></a>Parámetros

### `--parameterfile`
Establece el archivo de parámetros del sistema.

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
Establece el tipo de sistema. Los valores válidos son `sap_deployer`, `sap_library`, `sap_landscape` y `sap_system`.

```yaml
Type: String
Aliases: `-t`
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system

Required: True
```

### `--terraform_keyfile`
Establece el nombre del archivo de estados de Terraform.

```yaml
Type: String
Aliases: `-k`

Required: True
```

### `--subscription`
Establece la suscripción de Azure de destino.

```yaml
Type: String
Aliases: `-s`

Required: False
```

### `--storageaccountname`
Establece el nombre de la cuenta de almacenamiento que contiene los archivos de estados de Terraform.

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--tf_resource_name`
Establece el nombre del recurso en el archivo de estados de Terraform.

```yaml
Type: String
Aliases: `-n`

Required: False
```

### `--azure_resource_id`
Establece el identificador de recurso del recurso de Azure que se importará.

```yaml
Type: String
Aliases: `-i`

Required: False
```

## <a name="notes"></a>Notas
v0.9: Versión inicial


Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## <a name="related-links"></a>Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
