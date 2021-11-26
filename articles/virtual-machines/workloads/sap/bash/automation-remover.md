---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: remover.sh
description: Elimine un nuevo sistema SAP mediante un script de shell.
ms.openlocfilehash: a6d99d966a6be9ede32d26bef3757038260b0579
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725336"
---
# <a name="removersh"></a>Remover.sh

## <a name="synopsis"></a>Sinopsis
Puede usar el comando `remover.sh` para eliminar un nuevo sistema SAP. El script se puede usar para eliminar zonas de cargas de trabajo y sistemas SAP.

## <a name="syntax"></a>Sintaxis

```bash

remover.sh [--parameterfile] <String> [--type] <String> [ --help ]<String>]
s>]
```

## <a name="description"></a>Descripción
El script `remover.sh` implementa o actualiza una nueva implementación del tipo especificado.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

Elimina una implementación del sistema SAP.

```bash

remover.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>Ejemplo 2

Elimina una implementación de carga de trabajo.

```bash
remover.sh --parameterfile DEV-WEEU-SAP00-INFRASTRUCTURE.tfvars --type sap_landscape
```

## <a name="parameters"></a>Parámetros

### `--parameter_file`
Establece el archivo de parámetros del sistema

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
