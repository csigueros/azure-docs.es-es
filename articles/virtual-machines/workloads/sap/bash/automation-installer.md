---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: installer.sh
description: Implemente un nuevo sistema SAP mediante un script de shell.
ms.openlocfilehash: ba189394a6bd4ae30c43f4dcbfcb6a1f9c4d0f17
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725282"
---
# <a name="installersh"></a>Installer.sh

## <a name="synopsis"></a>Sinopsis
Puede usar el comando `installer.sh` para implementar un nuevo sistema SAP. El script se puede usar para implementar todos los distintos tipos de implementaciones.

## <a name="syntax"></a>Sintaxis

```bash

installer.sh [--parameterfile] <String> [--type] <String> [[--deployer_tfstate_key] <String>]
 [[ --landscape_tfstate_key] <String>] [[--storageaccountname] <String>] [[ --state_subscription ] <String>] [[ --state_subscription ] <String>] [[ --state_subscription ] [ --force ] [ --auto-approve ]<String>]
s>]
```

## <a name="description"></a>Descripción
El script `installer.sh` implementa o actualiza un nuevo sistema SAP del tipo especificado.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

Implementa o actualiza un sistema SAP.

```bash

installer.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>Ejemplo 2

Implementa o actualiza un sistema SAP.

```bash

installer.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system \ 
--deployer_tfstate_key MGMT-WEEU-DEP00-INFRASTRUCTURE.terraform.tfstate  \
--landscape_tfstate_key DEV-WEEU-SAP01-INFRASTRUCTURE.terraform.tfstate
```

### <a name="example-3"></a>Ejemplo 3

Implementa o actualiza una biblioteca SAP.

```bash
installer.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars --type sap_library
```

## <a name="parameters"></a>Parámetros

### `--parameter_file`
Establece el archivo de parámetros del sistema. Para más información, consulte [Configuración del sistema SAP](../automation-configure-system.md).

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
Establece el tipo de implementación. Los valores válidos son `sap_deployer`, `sap_library`, `sap_landscape` y `sap_system`.

```yaml
Type: String
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system
Aliases: `-t`

Required: True
```

### <a name="--deployer_tfstate_key"></a>--deployer_tfstate_key
Establece el nombre del archivo de estados para la implementación del implementador.

```yaml
Type: String
Aliases: `-d`

Required: False
```

### <a name="-landscape_tfstate_key"></a>-landscape_tfstate_key
Establece el nombre del archivo de estados para la implementación de la zona de cargas de trabajo.

```yaml
Type: String
Aliases: `-l`

Required: False
```

### `--state_subscription`
Establece el identificador de suscripción de la cuenta de almacenamiento de Terraform.

```yaml
Type: String
Aliases: `-k`

Required: False
```
### `--storageaccountname`
Establece el nombre de la cuenta de almacenamiento que contiene los archivos de estados de Terraform.

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--keyvault`
Establece el almacén de claves de las credenciales de implementación.

```yaml
Type: String
Aliases: `-v`

Required: False
```

### `--force`
Limpia la configuración local.

```yaml
Type: SwitchParameter
Aliases: `-f`

Required: False
```

### `--auto-approve`
Habilita la implementación silenciosa.

```yaml
Type: SwitchParameter
Aliases: `-i`

Required: False
```

### `--help`
Muestra la ayuda del script.

```yaml
Type: SwitchParameter
Aliases: `-h`

Required: False
```


## <a name="notes"></a>Notas
v0.9: Versión inicial


Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## <a name="related-links"></a>Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
