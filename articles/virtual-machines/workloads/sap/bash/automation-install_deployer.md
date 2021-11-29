---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_deployer.sh
description: Arranque de un nuevo implementador en el plano de control mediante un script de shell.
ms.openlocfilehash: 5b9bea75379c1dd99917424eff23b976291c073b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725291"
---
# <a name="install_deployersh"></a>install_deployer.sh

## <a name="synopsis"></a>Sinopsis
Puede usar el script `install_deployer.sh` para configurar una nueva máquina virtual del implementador en el plano de control.

## <a name="syntax"></a>Sintaxis

```bash
install_deployer.sh [ --parameterfile ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>Descripción
El script `install_deployer.sh` permite configurar un nuevo implementador en el plano de control.

La máquina virtual del implementador tiene instalaciones de Ansible y Terraform. La máquina virtual del implementador se usa para implementar los artefactos de SAP.


## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

```bash
install_deployer.sh --parameterfile MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars
```

## <a name="parameters"></a>Parámetros

### `--parameterfile`
Establece el archivo de parámetros para la máquina virtual del implementador. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#deployer).

```yaml
Type: String
Aliases: `-p`

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
