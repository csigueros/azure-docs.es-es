---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_workloadzone.sh
description: Implemente una nueva zona de cargas de trabajo de SAP mediante un script de shell.
ms.openlocfilehash: b88f24ff1f70b3786e09f9285bd697ca3bf2aeaa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725281"
---
# <a name="install_workloadzonesh"></a>install_workloadzone.sh

## <a name="synopsis"></a>Sinopsis
Puede usar el script `install_workloadzone.sh` para implementar una nueva zona de cargas de trabajo de SAP.

## <a name="syntax"></a>Sintaxis

```bash

install_workloadzone.sh [ -p or --parameterfile ] <String> 
 [[ --deployer_tfstate_key ] <String>] [[ --deployer_environment] <String>] [[ --state_subscription] <String>] [[ --storageaccountname ]
 [[ --subscription] <String>] [[ --spn_id  ] <String>] [[ --spn_secret ] <String>] [[ --tenant_id ] <String>]
 [[ --storageaccountname] <String>] [ force] [-i | --auto-approve]
```

## <a name="description"></a>Descripción
El comando `install_workloadzone.sh` implementa una nueva zona de cargas de trabajo de SAP. La zona de cargas de trabajo contiene los recursos compartidos de todas las máquinas virtuales.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

En este ejemplo se implementa la zona de cargas de trabajo, tal como se define en los archivos de parámetros. El proceso le pide los detalles del SPN.

```bash

install_workloadzone.sh -parameterfile PROD-WEEU-SAP00-infrastructure.tfvars
```

### <a name="example-2"></a>Ejemplo 2

En este ejemplo se implementa la zona de cargas de trabajo, tal como se define en los archivos de parámetros. El proceso agrega las credenciales de implementación al almacén de claves de la implementación.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>
keyvault=<keyvaultName>
storageaccount=<storageaccountName>
statefile_subscription=<statefile_subscription>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
        --parameter_file DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars  \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret "$spn_secret"                             \ 
        --tenant_id $tenant
```
## <a name="parameters"></a>Parámetros

### `--parameter_file`
Establece el archivo de parámetros para la zona de cargas de trabajo. Para más información, consulte [Configuración de la zona de cargas de trabajo](../automation-configure-workload-zone.md).

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_tfstate_key`
Establece el nombre de archivo de estado de Terraform de la máquina virtual del implementador.

```yaml
Type: String
Aliases: `-d`

Required: False
```

### `deployer_environment`
Nombre del entorno del implementador

```yaml
Type: String
Aliases: `-e`

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
