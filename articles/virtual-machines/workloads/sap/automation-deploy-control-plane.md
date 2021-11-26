---
title: Acerca de la implementación del plano de control para el marco de automatización de la implementación de SAP
description: Información general sobre el proceso de implementación del plano de control en el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 17f7b02d6a1ed57341a32573cadec9f27a25b9c8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725307"
---
# <a name="deploy-the-control-plane"></a>Implementación del plano de control

La implementación del plano de control del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) consta de los siguientes componentes:
 - Implementador
 - Biblioteca de SAP

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="Diagrama del plano de control.":::

## <a name="prepare-the-deployment-credentials"></a>Preparación de las credenciales de implementación

Los marcos de implementación de SAP usan entidades de servicio al realizar la implementación. Puede crear la entidad de servicio para la implementación del plano de control mediante los pasos siguientes con una cuenta con permisos para crear entidades de servicio:


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

## <a name="deploy-the-control-plane"></a>Implementación del plano de control
   
El archivo de configuración del implementador de ejemplo `MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars` se encuentra en la carpeta `~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE`.

El archivo de configuración del implementador de la biblioteca de SAP de ejemplo `MGMT-WEEU-SAP_LIBRARY.tfvars` se encuentra en la carpeta `~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/MGMT-WEEU-SAP_LIBRARY`.

Al ejecutar el comando siguiente, se creará el implementador, la biblioteca de SAP y se agregarán los detalles de la entidad de servicio al almacén de claves de implementación.

# <a name="linux"></a>[Linux](#tab/linux)

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
        --spn_secret  $spn_secret                                                                                \ 
        --tenant_id $tenant
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP00-INFRASTRUCTURE\MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars -Subscription $subscription -SPN_id $appId -SPN_password $spn_secret -Tenant_id $tenant_id
```
---


> [!NOTE]
> Asegúrese de reemplazar el valor de ejemplo `<subscriptionID>` por su identificador de suscripción.
> Reemplace los valores de `<appID>`, `<password>`, `<tenant>` por los valores de salida de la creación del SPN.

## <a name="next-step"></a>Paso siguiente

> [!div class="nextstepaction"]
> [Configuración de la zona de cargas de trabajo de SAP](automation-deploy-workload-zone.md)


