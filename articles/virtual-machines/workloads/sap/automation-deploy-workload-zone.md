---
title: Acerca de la implementación de la zona de cargas de trabajo en el marco de automatización
description: Introducción al proceso de implementación de la zona de cargas de trabajo de SAP dentro del marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 1942eb957d601696c6dcb8044386d7c2304748c8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725306"
---
# <a name="workload-zone-deployment-in-sap-automation-framework"></a>Implementación de la zona de cargas de trabajo en el marco de automatización de SAP

Normalmente, las [aplicaciones de SAP](automation-deployment-framework.md#sap-concepts) tienen varios niveles de desarrollo. Por ejemplo, puede tener los niveles de desarrollo, control de calidad y producción. El [marco de automatización de implementaciones de SAP en Azure](automation-deployment-framework.md) hace referencia a estos niveles como [zonas de cargas de trabajo](automation-deployment-framework.md#deployment-components).

Puede usar zonas de cargas de trabajo en varias regiones de Azure. Cada zona de cargas de trabajo tiene su propia instancia de Azure Virtual Network (Azure Vnet)

La zona de cargas de trabajo de SAP proporciona los siguientes servicios:

- Azure Virtual Network, incluidas las subredes y los grupos de seguridad de red.
- Azure Key Vault, para las credenciales del sistema.
- Una cuenta de almacenamiento para los diagnósticos de arranque
- Una cuenta de almacenamiento para los testigos en la nube

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="Diagrama de la zona de cargas de trabajo de SAP.":::

Las zonas de cargas de trabajo normalmente se implementan en radios en una arquitectura de red en estrella tipo hub-and-spoke. Pueden estar en sus propias suscripciones.

Admite el DNS privado desde el plano de control.


## <a name="core-configuration"></a>Configuración básica

En el siguiente archivo de parámetros de ejemplo solo se muestran los parámetros necesarios.

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="DEV"

# The location value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# The network logical name is mandatory - it is used in the naming convention and should map to the workload virtual network logical name 
network_name="SAP01"

# network_address_space is a mandatory parameter when an existing Virtual network is not used
network_address_space="10.110.0.0/16"

# admin_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
admin_subnet_address_prefix="10.110.0.0/19"

# db_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
db_subnet_address_prefix="10.110.96.0/19"

# app_subnet_address_prefix is a mandatory parameter if the subnets are not defined in the workload or if existing subnets are not used
app_subnet_address_prefix="10.110.32.0/19"

# The automation_username defines the user account used by the automation
automation_username="azureadm"

```

## <a name="preparing-the-workload-zone-deployment-credentials"></a>Preparación de las credenciales de implementación de la zona de carga de trabajo

Los marcos de implementación de SAP usan entidades de servicio al realizar la implementación. Puede crear la entidad de servicio para la implementación de la zona de cargas de trabajo mediante los pasos siguientes con una cuenta con permisos para crear entidades de servicio:


```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>" --name="<environment>-Deployment-Account"
  
```

> [!IMPORTANT]
> El nombre de la entidad de servicio debe ser único.
>
> Registre los valores de salida del comando.
   > - appId
   > - password
   > - tenant

Asigne los permisos correctos a la entidad de servicio: 

```azurecli-interactive
az role assignment create --assignee <appId> --role "User Access Administrator"
```

## <a name="deploying-the-sap-workload-zone"></a>Implementación de la zona de cargas de trabajo de SAP
   
El archivo de configuración de la zona de cargas de trabajo de ejemplo `DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars` se encuentra en carpeta `~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE`.

Al ejecutar el siguiente comando se implementará la zona de cargas de trabajo de SAP.

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> Realice esta tarea desde el implementador.

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
        --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars   \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret "$spn_secret"                             \
        --tenant_id $tenant_id
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell
$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"
$keyvault=<keyvaultName>
$storageaccount=<storageaccountName>
$statefile_subscription=<statefile_subscription>

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

New-SAPWorkloadZone -Parameterfile .DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
-Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
-State_subscription $statefile_subscription -Vault $keyvault -$StorageAccountName $storageaccount
```

---

> [!NOTE]
> Asegúrese de reemplazar el valor de ejemplo `<subscriptionID>` por su identificador de suscripción.
> Reemplace los valores `<appID>`, `<password>`, `<tenant>` por los valores de salida de la creación del SPN. Reemplace `<keyvaultName>` por el nombre del almacén de claves del implementador. Reemplace `<storageaccountName>` por el nombre de la cuenta de almacenamiento que contiene los archivos de estado de Terraform. Reemplace `<statefile_subscription>` por el identificador de suscripción de la cuenta de almacenamiento que contiene los archivos de estado de Terraform.

## <a name="next-step"></a>Paso siguiente

> [!div class="nextstepaction"]
> [Acerca de la implementación del sistema SAP en el marco de automatización](automation-configure-system.md)
