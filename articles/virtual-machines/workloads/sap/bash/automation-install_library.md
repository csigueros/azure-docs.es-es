---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_library.sh
description: Arranque de una nueva biblioteca SAP en el plano de control mediante un script de shell.
ms.openlocfilehash: 0fadaace80e1b349484c29bd115c4f79e3295530
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725285"
---
# <a name="install_librarysh"></a>Install_library.sh

## <a name="synopsis"></a>Sinopsis
El script `install_library.sh` configura una nueva biblioteca SAP.

## <a name="syntax"></a>Sintaxis

```bash
install_library.sh [ --parameterfile ] <String> [ --deployer_statefile_foldername ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>Descripción
El comando `install_library.sh` configura una nueva biblioteca SAP en el plano de control.
La biblioteca SAP proporciona el almacenamiento para los archivos de estado de Terraform y los soportes de instalación de SAP.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```bash

install_library.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars -deployer_statefile_foldername ../../DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE
```

## <a name="parameters"></a>Parámetros

### `--parameterfile`
Establece el archivo de parámetros para la máquina virtual del implementador. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#deployer).

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_statefile_foldername`
Establece la ruta de acceso de carpeta relativa a la carpeta que contiene el archivo de parámetros de la máquina virtual del implementador, denominado `terraform.tfstate`.

```yaml
Type: String
Aliases: `-d`

Required: True
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
