---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: set_secrets.sh
description: Establece los secretos de SPN en Azure Key Vault mediante un script de shell.
ms.openlocfilehash: 7f1c6102648aef85dbcb69c5adbdb4c2eeddb490
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733879"
---
# <a name="set_secretssh"></a>set_secrets.sh

## <a name="synopsis"></a>Sinopsis
Establece los secretos de la entidad de servicio en Azure Key Vault.

## <a name="syntax"></a>Sintaxis

```bash

set_secrets.sh [--region] <String> [--environment] <String> [--vault] <String> [--subscription] <String> [--spn_id] <String>
 [--spn_secret] <String> [--tenant_id] <String>
```

## <a name="description"></a>Descripción
Establece los secretos de Key Vault que requiere la automatización de la implementación.

## EXAMPLES

### <a name="example-1"></a>EJEMPLO 1

```bash

set_secrets.sh --environment DEV                        \
    --region weeu                                       \
    --vault MGMTWEEUDEP00userABC                        \
    --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --spn_id yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy       \
    --spn_secret ************************               \
    --tenant_id zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz     
```

## <a name="parameters"></a>Parámetros

### `--region`
Establece el nombre de la región de Azure para la implementación.

```yaml
Type: String
Aliases: `-r`

Required: True
```

### `--environment`
Establece el nombre del entorno de implementación.

```yaml
Type: String
```yaml
Type: String
Aliases: `-e`

Required: True
```

### `--vault`
Establece el nombre del almacén de claves.

```yaml
Type: String
Aliases: `-v`

Required: True
```

### `-spn_id`
Establece el identificador de aplicación de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-control-plane.md#prepare-the-deployment-credentials).

```yaml
Type: String
Aliases: `-c`

Required: False
```

### `--spn_secret`
Establece la contraseña de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-control-plane.md#prepare-the-deployment-credentials). 

```yaml
Type: String
Aliases: `-p`

Required: False
```

### `--tenant_id`
Establece el identificador del inquilino de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-control-plane.md#prepare-the-deployment-credentials). 

```yaml
Type: String
Aliases: `-t`

Required: False
```

## <a name="notes"></a>Notas
v0.9: Versión inicial


Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.
## <a name="related-links"></a>Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
