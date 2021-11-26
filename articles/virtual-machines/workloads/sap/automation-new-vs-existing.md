---
title: Configuración del marco de automatización tanto para implementaciones nuevas como existentes
description: Configuración del marco de automatización de la implementación de SAP en Azure para escenarios nuevos y existentes.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 0f5d426cc6291d6c7cc527668e8d009d1590c366
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725341"
---
# <a name="configuring-for-new-and-existing-deployments"></a>Configuración de implementaciones nuevas y existentes

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) se puede usar en escenarios de implementación nuevos y existentes. 

En los escenarios de implementación nuevos, el marco de automatización no usa la infraestructura de Azure existente. El proceso de implementación crea las redes virtuales, las subredes, los almacenes de claves, etc.

En los escenarios de implementación existentes, el marco de automatización usa la infraestructura de Azure existente. Por ejemplo, la implementación usa redes virtuales existentes.
## <a name="new-deployment-scenarios"></a>Escenarios de implementación nuevos

A continuación se muestra un ejemplo de escenarios de implementación nuevos que crean recursos.

> [!IMPORTANT]
> Modifique todas las configuraciones de ejemplo según sea necesario para el escenario.
### <a name="new-deployment"></a>Nueva implementación

En este escenario, el marco de automatización crea todos los componentes de Azure y usa el [implementador](automation-deployment-framework.md#deployment-components). Esta implementación de ejemplo contiene:

- Dos entornos de la región Oeste de Europa de Azure
  - Administración (`MGMT`) hospeda el plano de control
  - Desarrollo (`DEV`) hospeda el entorno de desarrollo
- Un implementador
- Biblioteca de SAP
- Sistema SAP (`SID X00`) con:
  - Dos servidores de aplicaciones
  - Una instancia de servicios centrales de alta disponibilidad (HA)
  - Un distribuidor web con un back-end HANA de nodo único que usa SUSE 12 SP5

| Componente       | Ubicación del archivo de parámetros                                                       |
| --------------- | ----------------------------------------------------------------------------- |
| Implementador        | DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars |
| Biblioteca         | LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                    |
| Zona de cargas de trabajo   | LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE/DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars  |
| Sistema          | SYSTEM/DEV-WEEU-SAP01-X00/DEV-WEEU-SAP01-X00.tfvars                           |


Para probar este escenario: 

Clone el repositorio del [marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana/) y copie los archivos de ejemplo en la carpeta raíz para los archivos de parámetros:

```bash
cd ~/Azure_SAP_Automated_Deployment
mkdir -p WORKSPACES/DEPLOYER
cp sap-hana/deploy/samples/WORKSPACES/DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE WORKSPACES/DEPLOYER/. -r

mkdir -p WORKSPACES/LIBRARY
cp sap-hana/deploy/samples/WORKSPACES/LIBRARY/MGMT-WEEU-SAP_LIBRARY WORKSPACES/LIBRARY/. -r

mkdir -p WORKSPACES/LANDSCAPE
cp sap-hana/deploy/samples/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE WORKSPACES/LANDSCAPE/. -r

mkdir -p WORKSPACES/SYSTEM
cp sap-hana/deploy/samples/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X00 WORKSPACES/SYSTEM/. -r
cd WORKSPACES
```
    
Prepare el plano de control instalando el implementador y la biblioteca. Asegúrese de reemplazar los valores de ejemplo por la información de la entidad de servicio.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-hana/"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

$DEPLOYMENT_REPO_PATH/scripts/prepare_region.sh
    --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars \
    --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                     \
    --subscription $subscriptionID                                                                          \
    --spn_id $appID                                                                                         \
    --spn_secret $spn_secret                                                                                \
    --tenant_id $tenant
    --auto-approve
```

También puede usar PowerShell para realizar la implementación.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

$Subscription=<subscriptionID>
$SPN_id=<appID>
$SPN_password=<password>
$Tenant_id=<tenant>

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
-LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
-Subscription $Subscription
-SPN_id $SPN_id
-SPN_password $SPN_password
-Tenant_id $Tenant_id
```

Implemente la zona de cargas de trabajo mediante la ejecución del script de Bash o de PowerShell. 

Asegúrese de reemplazar las credenciales de ejemplo por la información de la entidad de servicio. Puede usar las mismas credenciales de entidad de servicio que usó en la implementación del plano de control; sin embargo, en el caso de las implementaciones de producción se recomienda usar entidades de servicio diferentes en cada zona de cargas de trabajo.

```bash

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
    --parameterfile DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars       \
    --deployer_environment 'MGMT'                              \           
    --subscription $subscriptionID                             \
    --spn_id $appID                                            \
    --spn_secret $spn_secret                                   \
    --tenant_id $tenant                                        \
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\DEV-WEEU-SAP01-INFRASTRUCTURE

$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPWorkloadZone --parameterfile .\DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars 
    -DeployerEnvironment MGMT
    -Subscription $subscription
    -SPN_id $appId
    -SPN_password $spn_secret
    -Tenant_id $tenant_id
```

Implemente el sistema SAP. Ejecute el comando de Bash o de PowerShell.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh --parameterfile DEV-WEEU-SAP01-X00.tfvars --type sap_system --auto-approve
```

```powershell
Import-Module "SAPDeploymentUtilities.psd1"
cd \Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\DEV-WEEU-SAP01-X00

New-SAPSystem --parameterfile .\DEV-WEEU-SAP01-X00.tfvars
        -Type sap_system
```
## <a name="existing-example-scenarios"></a>Escenarios de ejemplo existentes

A continuación se muestran escenarios existentes de ejemplo que usan recursos de Azure existentes.

> [!IMPORTANT]
> Modifique todas las configuraciones de ejemplo según sea necesario para el escenario.
> Actualización de todos los marcadores de posición "<arm_resource_id>"

### <a name="existing-environment-scenario"></a>Escenario del entorno existente

En este escenario, el marco de automatización usa tanto los componentes de Azure existentes como el [implementador](automation-deployment-framework.md#deployment-components). Estos componentes existentes incluyen grupos de recursos, cuentas de almacenamiento, redes virtuales, subredes y grupos de seguridad de red. Esta implementación de ejemplo contiene:

- Dos entornos en la región Este de EE. UU. 2
  - Administración (`MGMT`) hospeda el plano de control
  - Control de calidad (`QA`) hospeda el entorno de control de calidad de SAP
- Un implementador
- La biblioteca de SAP
- Sistema SAP (`SID X01`) con:
  - Dos servidores de aplicaciones
  - Una instancia de servicios centrales de alta disponibilidad
  - Una base de datos que usa un back-end de Microsoft SQL Server que ejecuta Windows Server 2016
  - Web Dispatcher 

| Componente       | Ubicación del archivo de parámetros                                                       |
| --------------- | ----------------------------------------------------------------------------- |
| Implementador        | DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE/MGMT-EUS2-DEP01-INFRASTRUCTURE.tfvars |
| Biblioteca         | LIBRARY/MGMT-EUS2-SAP_LIBRARY/MGMT-EUS2-SAP_LIBRARY.tfvars                    |
| Zona de cargas de trabajo   | LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE/QA-EUS2-SAP03-INFRASTRUCTURE.tfvars    |
| Sistema          | SYSTEM/QA-EUS2-SAP03-X01/QA-EUS2-SAP03-X01.tfvars                             |


Copie los archivos de ejemplo en la carpeta raíz para los archivos de parámetros:

```bash
cd ~/Azure_SAP_Automated_Deployment
mkdir -p WORKSPACES/DEPLOYER
cp sap-hana/deploy/samples/WORKSPACES/DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE WORKSPACES/DEPLOYER/. -r
    
mkdir -p WORKSPACES/LIBRARY
cp sap-hana/deploy/samples/WORKSPACES/LIBRARY/MGMT-EUS2-SAP_LIBRARY WORKSPACES/LIBRARY/. -r
    
mkdir -p WORKSPACES/LANDSCAPE
cp sap-hana/deploy/samples/WORKSPACES/LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE WORKSPACES/LANDSCAPE/. -r
    
mkdir -p WORKSPACES/SYSTEM
cp sap-hana/deploy/samples/WORKSPACES/SYSTEM/QA-EUS2-SAP03-X01 WORKSPACES/SYSTEM/. -r
cd WORKSPACES
```

El archivo tfvars de ejemplo tiene marcadores de posición "<azure_resource_id>" que debe reemplazar por los identificadores de recursos reales de Azure para grupos de recursos, redes virtuales y subredes.

Implemente el plano de control instalando el implementador y la biblioteca de SAP. Ejecute el comando de Bash o de PowerShell. Asegúrese de reemplazar las credenciales de ejemplo por la información de la entidad de servicio.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-hana/"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

$DEPLOYMENT_REPO_PATH/scripts/prepare_region.sh
    --deployer_parameter_file DEPLOYER/MGMT-EUS2-DEP01-INFRASTRUCTURE/MGMT-EUS2-DEP01-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-EUS2-SAP_LIBRARY/MGMT-EUS2-SAP_LIBRARY.tfvars                      \                      
    --subscription $subscriptionID                                                                           \
    --spn_id $appID                                                                                          \
    --spn_secret $spn_secret                                                                                 \
    --tenant_id $tenant
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES


$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPAutomationRegion 
    -DeployerParameterfile .\DEPLOYER\MGMT-EUS2-DEP01-INFRASTRUCTURE\MGMT-EUS2-DEP01-INFRASTRUCTURE.json 
    -LibraryParameterfile .\LIBRARY\MGMT-EUS2-SAP_LIBRARY\MGMT-EUS2-SAP_LIBRARY.json 
    -Subscription $subscription 
    -SPN_id $appId 
    -SPN_password $spn_secret 
    -Tenant_id $tenant_id
    -Silent      
```

Implemente la zona de cargas de trabajo mediante la ejecución del script de Bash o de PowerShell. 

Asegúrese de reemplazar las credenciales de ejemplo por la información de la entidad de servicio. Puede usar las mismas credenciales de entidad de servicio que usó en la implementación del plano de control; sin embargo, en el caso de las implementaciones de producción se recomienda usar entidades de servicio diferentes en cada zona de cargas de trabajo.

```bash

cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/QA-EUS2-SAP03-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
    --parameterfile QA-EUS2-SAP03-INFRASTRUCTURE.tfvars        \
    --deployer_environment MGMT                                \           
    --subscription $subscriptionID                             \
    --spn_id $appID                                            \
    --spn_secret $spn_secret                                   \
    --tenant_id $tenant                                        \
    --auto-approve
```

```powershell

cd \Azure_SAP_Automated_Deployment\WORKSPACES\LANDSCAPE\QA-EUS2-SAP03-INFRASTRUCTURE

$subscription="<subscriptionID>"
$appId="<appID>"
$spn_secret="<password>"
$tenant_id="<tenant>"

New-SAPWorkloadZone --parameterfile .\QA-EUS2-SAP03-INFRASTRUCTURE.tfvars 
    -DeployerEnvironment MGMT
    -Subscription $subscription
    -SPN_id $appId
    -SPN_password $spn_secret
    -Tenant_id $tenant_id
```


Implemente el sistema SAP en el entorno de control de calidad. Ejecute el comando de Bash o de PowerShell.


```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/QA-EUS2-SAP03-X01

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh --parameterfile QA-EUS2-SAP03-X01.tfvars --type sap_system --auto-approve
```

```powershell
cd \Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\QA-EUS2-SAP03-X01

New-SAPSystem --parameterfile .\QA-EUS2-SAP03-tfvars.json -Type sap_system
```
## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Instrucciones del laboratorio práctico](automation-tutorial.md)
