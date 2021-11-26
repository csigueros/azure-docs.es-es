---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: Remove_region.sh
description: Elimina el plano de control de SAP (implementador, biblioteca) mediante un script de shell.
ms.openlocfilehash: 29f7461208120ee7d88c44943b7c393a0ca47ebf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733883"
---
# <a name="remove_regionsh"></a>Remove_region.sh

## <a name="synopsis"></a>Sinopsis

Quita el plano de control, incluida la máquina virtual del implementador y la biblioteca SAP.
## <a name="syntax"></a>Sintaxis

```bash

Remove_region.sh  [-d or --deployer_parameter_file ] <String> [-l or --library_parameter_file ] <String>
```

## <a name="description"></a>Descripción
Quita el plano de control de SAP, incluida la máquina virtual del implementador y la biblioteca SAP.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      
```

## <a name="parameters"></a>Parámetros

### `--deployer_parameter_file`
Establece el archivo de parámetros para la máquina virtual del implementador. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#deployer).

```yaml
Type: String
Aliases: `-d`

Required: True
```

### `--library_parameter_file`
Establece el archivo de parámetros para la biblioteca SAP. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#sap-library).

```yaml
Type: String
Aliases: `-l`

Required: True
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
