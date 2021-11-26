---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: Preparación de una región
description: Implementa el plano de control (implementador, biblioteca SAP) mediante un script de shell.
ms.openlocfilehash: 2510e8a289047e21364e16c147290fa0ef9d4475
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733885"
---
# <a name="prepare_regionsh"></a>prepare_region.sh

## <a name="synopsis"></a>Sinopsis
El script `prepare_region.sh` implementa el plano de control, incluida la máquina virtual del implementador, Azure Key Vault y la biblioteca SAP.

La máquina virtual del implementador tiene instalaciones de Ansible y Terraform. Esta máquina virtual implementa los artefactos de SAP.

## <a name="syntax"></a>Sintaxis

```bash
prepare_region.sh [ --deployer_parameter_file ] <String> [ --library_parameter_file ] <String>
 [[ --subscription] <String>] [[ --spn_id  ] <String>] [[ --spn_secret ] <String>] [[ --tenant_id ] <String>]
 [[ --storageaccountname] <String>] [ --force ] [ --auto-approve ]
```

## <a name="description"></a>Descripción
Implementa el plano de control, que incluye la máquina virtual del implementador y la biblioteca SAP. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md) e [Implementación del plano de control](../automation-deploy-control-plane.md).

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

En este ejemplo se implementa el plano de control, tal como se define en los archivos de parámetros. El proceso le pide los detalles del SPN.

```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      
```

### <a name="example-2"></a>Ejemplo 2

En este ejemplo se implementa el plano de control, tal como se define en los archivos de parámetros. El proceso agrega las credenciales de implementación al almacén de claves de la implementación.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

az logout
az login

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      \
        --subscription $subscriptionID                                                                           \
        --spn_id $appID                                                                                          \
        --spn_secret "$spn_secret"                                                                               \ 
        --tenant_id $tenant
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

### `--subscription`
Establece la suscripción de Azure de destino.

```yaml
Type: String
Aliases: `-s`

Required: False
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


### `--storageaccountname`
Establece el nombre de la cuenta de almacenamiento que contiene los archivos de estado de Terraform.

```yaml
Type: String
Aliases: `-a`

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

+[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
