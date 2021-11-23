---
title: Escalado empresarial con el marco de automatización de implementaciones de SAP
description: Cómo realizar el escalado empresarial para implementaciones mediante el marco de automatización de implementaciones de SAP en Azure.
author: hdamecharla
ms.author: hdamecharla
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: tutorial
ms.service: virtual-machines-sap
ms.openlocfilehash: ab67d4ecf6588b2fd95abfc6702b58a19029e137
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725278"
---
# <a name="enterprise-scale-for-sap-automation-framework-deployment---hands-on-lab"></a>Escalado empresarial para la implementación del marco de automatización de SAP: Laboratorio práctico


En este tutorial se muestra cómo realizar el escalado empresarial para implementaciones mediante el [marco de automatización de implementaciones de SAP en Azure](automation-deployment-framework.md). En este ejemplo se Azure Cloud Shell para implementar la infraestructura del plano de control. La máquina virtual (VM) del implementador crea la infraestructura restante y las configuraciones de SAP HANA. Hay una rama bloqueada por características en el [repositorio de GitHub para el marco de automatización](https://github.com/Azure/sap-hana/), denominada `sap-level-up`, para su uso con este tutorial.

En este laboratorio, realizará las tareas siguientes:

> [!div class="checklist"]
> * Implementar el plano de control (infraestructura de Deployer y Library)
> * Implementar la zona de cargas de trabajo (Landscape, System)
> * Descargar y cargar BOM
> * Configurar los valores estándar y específicos de SAP del sistema operativo
> * Instalar la base de datos de HANA
> * Instalar el servidor SCS
> * Cargar la base de datos de HANA
> * Instalar el servidor de aplicaciones principal

La implementación de SAP en Azure con el marco de automatización consta de tres pasos principales.

1. Preparar la región. En este paso se implementan componentes para admitir el marco de automatización de SAP en una región de Azure especificada. Algunas partes de este paso son:
   1. Creación del entorno de implementación
   2. Creación de almacenamiento compartido para archivos de estado de Terraform
   3. Creación de almacenamiento compartido para medios de instalación de SAP

2. Preparar la zona de cargas de trabajo. En este paso se implementan los componentes de la zona de cargas de trabajo, como la red virtual y los almacenes de claves.

3. Implementar el sistema. Este paso incluye la infraestructura del sistema SAP.

Hay varios flujos de trabajo en el proceso de automatización de una implementación. Sin embargo, para facilitar la implementación, este tutorial se centra en un flujo de trabajo. Puede implementar este flujo de trabajo, el entorno independiente de SAP S/4HANA, mediante Bash. En el tutorial se describe la jerarquía general y las distintas fases de la implementación.
### <a name="environment-overview"></a>Introducción al entorno

El marco de automatización de implementaciones de SAP en Azure tiene dos componentes principales:

- La infraestructura de implementación (plano de control)
- La infraestructura de SAP (carga de trabajo de SAP)

En el diagrama siguiente se muestra la dependencia entre el plano de control y el plano de aplicación.

:::image type="content" source="./media/automation-deployment-framework/control-plane-sap-infrastructure.png" alt-text="Dependencia entre el plano de control y el plano de aplicación":::

El marco usa Terraform para la implementación de la infraestructura, y Ansible para la configuración del sistema operativo y la aplicación. En el diagrama siguiente se muestra la separación lógica del plano de control y la zona de cargas de trabajo.

:::image type="content" source="./media/automation-deployment-framework/automation-diagram-full.png" alt-text="Diagrama que muestra el entorno del marco de automatización de implementaciones de SAP.":::

#### <a name="management-zone"></a>Zona de administración

La zona de administración contiene la infraestructura del plano de control desde la que se implementan otros entornos. Una vez que se ha implementado la zona de administración, pocas veces, si es que alguna, tendrá que volver a implementarla.

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="Diagrama del plano de control.":::

**Deployer** es el motor de ejecución del marco de automatización de SAP. Esta máquina virtual (VM) preconfigurada se usa para ejecutar comandos de Terraform y Ansible.

**Library de SAP** proporciona el almacenamiento persistente para los archivos de estado de Terraform y los medios de instalación de SAP descargados para el plano de control.

Configure el implementador y la biblioteca en un archivo de variables `.tfvars` de Terraform. Consulte [Configuración del plano de control](automation-configure-control-plane.md).

#### <a name="workload-zone"></a>Zona de cargas de trabajo

Normalmente, una aplicación de SAP tiene varios niveles de implementación. Por ejemplo, puede tener los niveles de desarrollo, control de calidad y producción. El marco de automatización de implementaciones de SAP hace referencia a estos niveles como zonas de cargas de trabajo.

:::image type="content" source="./media/automation-deployment-framework/workload-zone.png" alt-text="Zona de cargas de trabajo.":::

La **zona de cargas de trabajo de SAP** contiene los componentes de red y compartidos de las VM de SAP. Estos componentes incluyen tablas de rutas, grupos de seguridad de red y redes virtuales (VNet). Landscape ofrece la oportunidad de dividir las implementaciones en distintos entornos. Consulte la [configuración de la zona de cargas de trabajo](automation-configure-workload-zone.md).

La implementación del sistema consta de las máquinas virtuales que ejecutarán la aplicación de SAP, incluidos los niveles web, de aplicación y de base de datos. Consulte la [configuración del sistema de SAP](automation-configure-system.md).

## <a name="hands-on-lab"></a>Laboratorio práctico

### <a name="prerequisites"></a>Requisitos previos

El [repositorio del marco de automatización de implementaciones de SAP](https://github.com/Azure/sap-hana) está disponible en GitHub.

> [!IMPORTANT]
> Antes de empezar, asegúrese de cambiar de la rama predeterminada a **sap-level-up**.

Necesita un cliente SSH para conectarse a Deployer. Use cualquier cliente SSH con el que se sienta cómodo.

#### <a name="review-the-azure-subscription-quota"></a>Revisión de la cuota de suscripción de Azure

Asegúrese de que la suscripción a Microsoft Azure tenga una oferta básica suficiente para las SKU de la familia DdSV4 y EdsV4 en la región elegida. Unos 50 núcleos disponibles para cada familia de VM deberían bastar.

#### <a name="suser-account-for-sap-software-download"></a>Cuenta de usuario para la descarga del software de SAP

Se requiere una cuenta de usuario de SAP válida (cuenta de SAP-User o S-User) con privilegios de descarga de software para descargar el software de SAP.

## <a name="set-up-cloud-shell"></a>Configuración de Cloud Shell

Vaya a [Azure Cloud Shell](https://shell.azure.com).

Inicio de sesión en la cuenta de Azure

```cloudshell-interactive
az login
```

Autentique el inicio de sesión. No cierre la ventana hasta que se le pida.

De manera opcional, cambie la suscripción activa.
  
```cloudshell-interactive
az account set -s <Subscription ID>
```

Valide que la suscripción activa ha cambiado:

```cloudshell-interactive
az account list --query "[?isDefault]"
```
**or**

```cloudshell-interactive
az  account list -o table | grep True
```

Cree la carpeta de implementación y clone el repositorio.

```cloudshell-interactive
mkdir -p ~/Azure_SAP_Automated_Deployment

cd ~/Azure_SAP_Automated_Deployment

git clone https://github.com/Azure/sap-hana.git \
    --single-branch --branch=sap-level-up

cd ~/Azure_SAP_Automated_Deployment/sap-hana
```

Como alternativa, valide la versión de Terraform y la Interfaz de la línea de comandos de Azure (CLI de Azure) disponible en la instancia de Cloud Shell.

```bash
./util/check_workstation.sh
```

Para ejecutar el marco de automatización, actualice a las siguientes versiones.

1. `az`, versión 2.28.0 o posterior

1. `terraform`, versión 1.0.8 o posterior [Actualice siguiendo las instrucciones de Terraform](https://www.terraform.io/upgrade-guides/0-12.html) según sea necesario.


## <a name="create-service-principal"></a>Creación de una entidad de servicio

El marco de automatización de implementaciones de SAP usa entidades de servicio para la implementación. Cree una entidad de servicio para la implementación del plano de control como se muestra a continuación. Asegúrese de usar una cuenta con permisos para crear entidades de servicio.

> [!NOTE] 
> Al elegir el nombre de la entidad de servicio, asegúrese de que el nombre sea único dentro del inquilino de Azure.


Conceda permisos de colaborador y administrador de acceso de usuario a la entidad de servicio.

```cloudshell-interactive
az ad sp create-for-rbac --role="Contributor" \
  --scopes="/subscriptions/<subscriptionID>" \
  --name="<environment>-Deployment-Account"
```

Revise el resultado. Por ejemplo:

```json
{
    "appId": "<AppID>",
    "displayName": "<environment>-Deployment-Account ",
    "name": "<AppID>,
    "password": "<AppSecret>",
    "tenant": "<Tenant ID>"
}
```

Copie los detalles de la salida. Asegúrese de guardar los valores de los campos siguientes: `appId`, `password` y `Tenant`.

La salida se asigna a los parámetros siguientes. Estos parámetros se usan en pasos posteriores, con comandos de automatización.

| **Nombre del parámetro de entrada** | **Nombre de salida** |
|--------------------------|-----------------|
| `spn_id`                 | `appId`         |
| `spn_secret`             | `password`      |
| `tenant_id`              | `tenant`        |

Asigne el rol **Administrador de acceso de usuario** a la entidad de servicio.

```bash
az role assignment create --assignee <appId> \
  --role "User Access Administrator"
```

## <a name="view-configuration-files"></a>Visualización de los archivos de configuración

>[!IMPORTANT]
> Trate siempre el repositorio de GitHub como de solo lectura. Trabaje en una copia de la carpeta `WORKSPACES` para realizar cambios en la configuración. Este método mantiene la configuración estable si el repositorio cambia.

Copie las configuraciones de ejemplo en un directorio local de las áreas de trabajo:

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -Rp ./sap-hana/deploy/samples/WORKSPACES ./
```

Abra VS Code desde Cloud Shell. 

```cloudshell-interactive
code .
```

Expanda el directorio **WORKSPACES**. Hay cinco subcarpetas: **DEPLOYER**, **LANDSCAPE**, **LIBRARY**, **SYSTEM** y **BOMS**. Expanda cada una de estas carpetas para buscar archivos de configuración de implementación regional.

Busque el código de cuatro caracteres adecuado que corresponda a la región de Azure que está usando.

| Nombre de región        | Código de región |
|--------------------|-------------|
| Este de Australia     | AUEA        |
| Centro de Canadá     | CACE        |
| Centro de EE. UU.         | CEUS        |
| Este de EE. UU.            | EAUS        |
| Norte de Europa       | NOEU        |
| Norte de Sudáfrica | SANO        |
| Sudeste de Asia     | SOEA        |
| Sur de Reino Unido 2           | UKSO        |
| Oeste de EE. UU. 2          | WES2        |

Busque los archivos de variables de Terraform en la subcarpeta adecuada. Por ejemplo, el archivo de variables de Terraform **DEPLOYER** podría tener el siguiente aspecto:

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example, PROD and NP.
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# management_network_address_space is the address space for management virtual network
management_network_address_space="10.10.20.0/25"
# management_subnet_address_prefix is the address prefix for the management subnet
management_subnet_address_prefix="10.10.20.64/28"
# management_firewall_subnet_address_prefix is the address prefix for the firewall subnet
management_firewall_subnet_address_prefix="10.10.20.0/26"

deployer_enable_public_ip=true
firewall_deployment=true
```

Anote las ubicaciones de los archivos de variables de Terraform para futuras modificaciones durante la implementación.

## <a name="deploy-control-plane"></a>Implementación del plano de control

Use el script [prepare_region](bash/automation-prepare-region.md) para implementar Deployer y Library. Estas piezas de implementación componen el plano de control de un área de automatización elegida. 

- La implementación pasa por ciclos de implementación de la infraestructura, actualización del estado y carga de los archivos de estado de Terraform en la cuenta de almacenamiento de Library. Todos estos pasos están empaquetados en un único script de implementación. El script necesita la ubicación del archivo de configuración para Deployer y Library, y algunos otros parámetros, como se muestra a continuación.

Por ejemplo, elija **Norte de Europa** como ubicación de implementación, con el nombre de cuatro caracteres `NOEU` descrito anteriormente. El archivo de configuración de Deployer de ejemplo `MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars` se encuentra en carpeta `~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE`.

El archivo de configuración de SAP Library de ejemplo `MGMT-NOEU-SAP_LIBRARY.tfvars` se encuentra en carpeta `~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/MGMT-NOEU-SAP_LIBRARY`.

1. Cree Deployer y SAP Library. Agregue también los detalles de la entidad de servicio al almacén de claves de la implementación.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret="<password>"
tenant_id=<tenant>

export DEPLOYMENT_REPO_PATH="${HOME}/Azure_SAP_Automated_Deployment/sap-hana"
export ARM_SUBSCRIPTION_ID="${subscriptionID}"

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                     \
    --deployer_parameter_file DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE/MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars  \
    --library_parameter_file LIBRARY/MGMT-NOEU-SAP_LIBRARY/MGMT-NOEU-SAP_LIBRARY.tfvars                      \
    --subscription $subscriptionID                                                                           \
    --spn_id $appID                                                                                          \
    --spn_secret $spn_secret                                                                                 \
    --tenant_id $tenant_id
```

Si tiene problemas de autenticación, ejecute `az logout` para cerrar sesión. Borre `token-cache` y ejecute `az login` para volver a autenticarse.

Espere a que el marco de automatización ejecute las operaciones de Terraform `plan` y `apply`.

La implementación de Deployer puede tardar unos 15 a 20 minutos.

Vaya a [Azure Portal](https://portal.azure.com).  

Seleccione **Grupos de recursos**. Busque nuevos grupos de recursos para la infraestructura del implementador y la biblioteca. Por ejemplo, `MGMT-[region]-DEP00-INFRASTRUCTURE` y `MGMT-[region]-SAP_LIBRARY`.

A continuación se muestra el contenido de los grupos de recursos Deployer y SAP Library.

:::image type="content" source="media/automation-tutorial/deployer-resource-group.png" alt-text="Recursos de Deployer":::
    
:::image type="content" source="media/automation-tutorial/sap-library-resource-group.png" alt-text="Recursos de biblioteca":::

El archivo de estado de Terraform está ahora en la cuenta de almacenamiento cuyo nombre contiene "tfstate". La cuenta de almacenamiento tiene un contenedor denominado "tfstate" con los archivos de estado de Deployer y Library. A continuación se muestra una lista del contenido del contenedor "tfstate" después de una implementación correcta del plano de control.
    
:::image type="content" source="media/automation-tutorial/terraform-state-files.png" alt-text="Archivos tfstate del plano de control":::

### <a name="common-issues-and-solutions"></a>Problemas comunes y soluciones

Si recibe el siguiente error para la creación del módulo de Deployer, asegúrese de encontrarse en el directorio **WORKSPACES** al ejecutar el script:
  
```text
Incorrect parameter file.
The file must contain the environment attribute!!
```

El siguiente error es transitorio. Vuelva a ejecutar el mismo comando, `prepare_region.sh`.
  
 ```text
Error: file provisioner error
..
timeout - last error: dial tcp
```


Si tiene problemas de autenticación directamente después de ejecutar el script `prepare_region.sh`, ejecute:

```bash
az logout

az login
```

## <a name="connect-to-deployer-vm"></a>Conexión con la VM del implementador

Después de preparar la región, el archivo de estado de Terraform se mueve a un back-end remoto, `azurerm`. Todos los secretos para conectarse a la VM del implementador están disponibles en un almacén de claves dentro del grupo de recursos Deployer.

Asegúrese de que puede conectarse a la VM del implementador:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Busque o seleccione **Almacenes de claves**.

1. En la página **Almacén de claves**, busque el almacén de claves del implementador. El nombre empieza por `MGMT[Region]DEP00user`. Si es necesario, filtre por **Grupo de recursos**.

1. En el menú del almacén de claves, seleccione **Secretos** en **Configuración**.

1. En la página del secreto, seleccione la versión actual. Luego, copie el **Valor secreto**.

1. Abra un editor de texto sin formato. Copie el valor secreto. 
 
1. Guarde el archivo donde se mantienen las claves SSH. Por ejemplo, `C:\\Users\\<your-username>\\.ssh`.
 
1. Guarde el archivo. Si se le pide **Guardar como tipo**, seleccione **Todos los archivos** si **SSH** no es una opción. Por ejemplo, use `deployer.ssh`.

1. Conéctese a la VM del implementador a través de cualquier cliente SSH. Use la dirección IP pública que anotó anteriormente y la clave SSH que descargó. Si usa PuTTY, convierta primero el archivo de clave SSH mediante PuTTYGen. 

> [!NOTE] 
>El nombre de usuario predeterminado es *azureadm*.

- Una vez que se conecte a la VM del implementador, podrá aprovisionar la zona de cargas de trabajo.


> [!IMPORTANT]
> El resto de las tareas deben ejecutarse en el implementador.

## <a name="get-sap-software-using-the-bom"></a>Obtención del software de SAP mediante BOM

El marco de automatización le brinda herramientas para descargar la lista de materiales (BOM) de SAP. SAP Library sirve como archivo para todos los medios necesarios para implementar SAP.

La lista de materiales (BOM) de SAP emula a SAP Maintenance Planner. Hay identificadores de producto pertinentes y un paquete de direcciones URL de descarga. La VM del implementador lee la BOM procesada y descarga los archivos de la cuenta de almacenamiento en SAP Central Services (SCS) para la instalación.

Un extracto de ejemplo de un archivo BOM tiene el siguiente aspecto:

```yaml

---
name:    'S41909SPS03_v0005ms'
target:  'S/4 HANA 1909 SPS 03'
version: 6

product_ids:
  dbl:       NW_ABAP_DB:S4HANA1909.CORE.HDB.ABAP
  scs:       NW_ABAP_ASCS:S4HANA1909.CORE.HDB.ABAP
  scs_ha:    NW_ABAP_ASCS:S4HANA1909.CORE.HDB.ABAPHA
  pas:       NW_ABAP_CI:S4HANA1909.CORE.HDB.ABAP
  pas_ha:    NW_ABAP_CI:S4HANA1909.CORE.HDB.ABAPHA
  app:       NW_DI:S4HANA1909.CORE.HDB.PD
  app_ha:    NW_DI:S4HANA1909.CORE.HDB.ABAPHA
  web:       NW_Webdispatcher:NW750.IND.PD
  ers:       NW_ERS:S4HANA1909.CORE.HDB.ABAP
  ers_ha:    NW_ERS:S4HANA1909.CORE.HDB.ABAPHA

materials:
  dependencies:
    - name:     HANA_2_00_055_v0004ms

  media:
    # SAPCAR 7.22
    - name:         SAPCAR
      archive:      SAPCAR_1010-70006178.EXE
      checksum:     dff45f8df953ef09dc560ea2689e53d46a14788d5d184834bb56544d342d7b
      filename:     SAPCAR
      permissions:  '0755'
      url:          https://softwaredownloads.sap.com/file/0020000002208852020

    # Kernel
    - name:         "Kernel Part I ; OS: Linux on x86_64 64bit ; DB: Database independent"
```


Para esta configuración de ejemplo, el grupo de recursos es `MGMT-NOEU-DEP00-INFRASTRUCTURE`. El nombre del almacén de claves del implementador incluiría `MGMTNOEUDEP00user` en el nombre. Esta información se usa para configurar los secretos del almacén de claves del implementador.

Agregue un secreto con el nombre de usuario de la cuenta de usuario de SAP. Reemplace `<keyvault-name>` con el nombre del almacén de claves del implementador. Además, reemplace `<sap-username>` por el nombre de usuario de SAP.

```bash
az keyvault secret set --name "S-Username" --vault-name "<keyvault-name>" --value "<sap-username>";
```

Agregue un secreto con la contraseña de la cuenta de usuario de SAP. Reemplace `<keyvault-name>` por el nombre del almacén de claves del implementador, y `<sap-password>` por la contraseña de SAP.

```bash
az keyvault secret set --name "S-Password" --vault-name "<keyvault-name>" --value "<sap-password>";
```

A continuación, configure el archivo de parámetros de SAP para el proceso de descarga. Luego, descargue el software de SAP mediante guiones de procedimientos de Ansible. Ejecute los siguientes comandos:

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/BOMS

vi sap-parameters.yaml
```
  
En `kv_name parameter`, escriba el nombre del almacén de claves del grupo de recursos Deployer.
  
  El archivo debe tener una apariencia similar a la configuración de ejemplo siguiente:

```yaml

bom_base_name:                 S41909SPS03_v0006ms
kv_name:                       MGMTNOEUDEP00user99F 

```
    
Ejecute los guiones de procedimientos de Ansible. Una manera de ejecutar los guiones de procedimientos es usar el menú de prueba del validador. Ejecute el script de menú de prueba del validador.
  
```bash
~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/validator_test_menu.sh
```
  
Seleccione el guion de procedimientos que desea ejecutar.
  
```bash
1) BoM Downloader
3) Quit
Please select playbook:
```

Seleccione el guion `1) BOM Downloader` para descargar el software de SAP descrito en el archivo BOM en la cuenta de almacenamiento. Compruebe que el contenedor `sapbits` tiene todos los medios para la instalación.
    
## <a name="collect-workload-zone-information"></a>Recopilación de información de la zona de cargas de trabajo

Recopile la siguiente información en un editor de texto:

  - Nombre de la cuenta de almacenamiento de los archivos de estado de Terraform en el grupo de recursos Library.
    - Siguiendo el ejemplo anterior, el grupo de recursos sería *MGMT-NOEU-SAP_LIBRARY*.
    - El nombre de la cuenta de almacenamiento contendrá *mgmtnoeutfstate*.

  - El nombre del archivo de estado de Deployer se puede encontrar en Grupo de recursos Library.
    - Grupo de recursos Library -> cuenta de almacenamiento de estados - > contenedores -> tfstate -> copie el **nombre** del archivo de estado de Deployer.
    - Siguiendo el ejemplo anterior, el nombre del blob sería: *MGMT-NOEU-DEP00-INFRASTRUCTURE.terraform.tfstate*.

  - El nombre del almacén de claves del grupo de recursos Deployer.
    - Siguiendo el ejemplo anterior, el grupo de recursos sería *MGMT-NOEU-DEP00-INFRASTRUCTURE*.
    - El nombre del almacén de claves contendrá *MGMTNOEUDEP00user*.

La dirección IP pública de la VM del implementador. Vaya al grupo de recursos Deployer, abra la VM del implementador y copie la dirección IP pública.
  
## <a name="prepare-the-workload-zone-deployment"></a>Preparación de la implementación de la zona de cargas de trabajo

Conéctese a la VM del implementador para los pasos siguientes. Ahora allí hay una copia del repositorio.

Vaya a la carpeta *Azure_SAP_Automated_Deployment* en el directorio principal. Quite la carpeta **WORKSPACES** existente. Copiará los ejemplos más adelante.

```bash
rm -rf WORKSPACES
```

Vaya a la carpeta **sap-hana**. Restaure la rama **sap-level-up**.
  
```bash
cd ~/Azure_SAP_Automated_Deployment/sap-hana/

git checkout sap-level-up
```

Copie los archivos de configuración de ejemplo del repositorio.

```bash
cd ~/Azure_SAP_Automated_Deployment/

cp -Rp ./sap-hana/deploy/samples/WORKSPACES ./
```

## <a name="deploy-the-workload-zone"></a>Implementación de la zona de cargas de trabajo


Use el script [install_workloadzone](bash/automation-install_workloadzone.md) para implementar la zona de cargas de trabajo de SAP. 

En la VM del implementador, vaya a la carpeta `Azure_SAP_Automated_Deployment`.
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-XXXX-SAP01-INFRASTRUCTURE
```

En la región de ejemplo "northeurope", la carpeta tendrá el siguiente aspecto:

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE
```
   
Abra el archivo de configuración de la zona de cargas de trabajo y, si es necesario, cambie el nombre lógico de red para que coincida con el nombre de red.

Aquí se necesitan los detalles recopilados anteriormente. Estos detalles son los siguientes:
  - Nombre del archivo tfstate del implementador (que se encuentra en el contenedor tfstate)
  - Nombre de la cuenta de almacenamiento de tfstate
  - Nombre del almacén de claves del implementador

Inicie la implementación de la zona de cargas de trabajo:

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret="<password>"
tenant_id=<tenant>
storageaccount=<storageaccountName>
statefile_subscription=<subscriptionID>
key_vault=<vaultID>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh              \
    --parameterfile ./DEV-NOEU-SAP01-INFRASTRUCTURE.tfvars                  \
    --deployer_environment MGMT                                             \
    --deployer_tfstate_key MGMT-NOEU-DEP00-INFRASTRUCTURE.terraform.tfstate \
    --keyvault $key_vault                                                   \
    --storageaccountname $storageaccount                                    \
    --state_subscription $statefile_subscription                            \
    --subscription $subscriptionID                                          \
    --spn_id $appID                                                         \
    --spn_secret $spn_secret                                                \
    --tenant_id $tenant_id
```

La implementación de la zona de cargas de trabajo debe iniciarse automáticamente.

Espere a que finalice la implementación. El nuevo grupo de recursos aparece en Azure Portal.

## <a name="deploy-sap-system-infrastructure"></a>Implementación de la infraestructura del sistema SAP

Una vez completada la zona de cargas de trabajo, puede implementar los recursos de infraestructura del sistema SAP. El sistema SAP crea las VM y los componentes auxiliares para la aplicación de SAP.
Use el script [installer.sh](bash/automation-installer.md) para implementar el sistema SAP. 

El sistema SAP implementa:

- El nivel de base de datos, que implementa las VM de base de datos y sus discos, y una instancia de Azure Standard Load Balancer. Puede ejecutar bases de datos de HANA o bases de datos de AnyDB en este nivel.
- El nivel SCS, que implementa un número de VM definido por el cliente y una instancia de Azure Standard Load Balancer.
- El nivel de aplicación, que implementa las VM y sus discos.
- El nivel de distribuidor web.

Implemente el sistema SAP.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-XXXX-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh \
  --parameterfile DEV-XXXX-SAP01-X00.tfvars         \
  --type sap_system                                 \
  --auto-approve
```
  
El comando de implementación del ejemplo `northeurope` tendrá el siguiente aspecto:

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh  \
  --parameter_file DEV-NOEU-SAP01-X00.tfvars         \
  --type sap_system                                  \
  --auto-approve
```

Compruebe que el grupo de recursos del sistema ahora esté en Azure Portal.

## <a name="sap-application-installation"></a>Instalación de la aplicación de SAP

La instalación de la aplicación de SAP se produce a través de guiones de procedimientos de Ansible. 

Vaya a la carpeta de implementación del sistema:

```bash
cd ~/Azure_ SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00/
```

Asegúrese de que tiene los siguientes archivos en la carpeta actual: `sap-parameters.yaml` y `SID_host.yaml`.

En el caso de un sistema SAP S/4HANA independiente, hay ocho guiones de procedimientos para ejecutarse en secuencia. Puede desencadenar los siguientes guiones de procedimientos mediante un sistema de menús. 

Desencadene los guiones de procedimientos que se ejecutarán.

### <a name="playbook-os-config"></a>Guion de procedimientos: OS Config

Al seleccionar este guion de procedimientos, se realiza la configuración genérica del sistema operativo en todas las máquinas, lo que incluye la configuración de repositorios de software, paquetes, servicios, etc.

### <a name="playbook-sap-specific-os-config"></a>Guion de procedimientos: SAP-Specific OS config

Al seleccionar este guion, se realiza la configuración del sistema operativo de SAP en todas las máquinas, lo que incluye la creación de grupos de volúmenes, sistemas de archivos, la configuración de repositorios de software, paquetes y servicios.

### <a name="playbook-bom-processing"></a>Guion de procedimientos: BOM Processing

Al seleccionar este guion, se descarga el software de SAP en la máquina virtual de SCS. 
    
### <a name="playbook-hana-db-install"></a>Guion de procedimientos: HANA DB Install

Este guion instalará las instancias de base de datos de HANA.

### <a name="playbook-scs-install"></a>Guion de procedimientos: SCS Install
  
Este guion instalará SAP Central Services. Si se configura la alta disponibilidad, el guion también instalará la instancia de ERS y configurará Pacemaker.

### <a name="playbook-db-load"></a>Guion de procedimientos: DB Load

Este guion invocará la tarea de carga de bases de datos desde el servidor de aplicaciones principal.

### <a name="playbook-pas-install"></a>Guion de procedimientos: PAS Install

Este guion instalará el servidor de aplicaciones principal.
  
### <a name="playbook-app-install"></a>Guion de procedimientos: APP Install

Este guion instalará los servidores de aplicaciones adicionales.

Ahora tiene implementado y configurado un sistema HANA independiente, si necesita configurar una base de datos de SAP HANA de alta disponibilidad, ejecute el guion HANA HA.

### <a name="playbook-hana-ha-playbook"></a>Guion de procedimientos: HANA HA

Este guion configurará la replicación del sistema HANA (HSR) y Pacemaker para la base de datos de HANA.

## <a name="clean-up-installation"></a>Limpieza de la instalación

> [!NOTE]
> Es importante limpiar la instalación de SAP de este tutorial una vez que haya terminado. De lo contrario, seguirá incurriendo en costos relacionados con los recursos.

Para quitar toda la infraestructura de SAP que ha implementado, debe:

> [!div class="checklist"]
> * Quitar los recursos de infraestructura del sistema SAP
> * Quitar todas las zonas de cargas de trabajo (Landscape)
> * Quitar el plano de control

Ejecute la eliminación de los recursos de la infraestructura de SAP y las zonas de cargas de trabajo desde la VM del implementador. Ejecute la eliminación del plano de control desde Cloud Shell.

Antes de empezar, inicie sesión en su cuenta de Azure. A continuación, compruebe que está en la suscripción correcta.

### <a name="remove-sap-infrastructure"></a>Eliminación de la infraestructura de SAP

Vaya a la subcarpeta `DEV-NOEU-SAP01-X00` dentro de la carpeta `SYSTEM`. Después, ejecute este comando:
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-NOEU-SAP01-X00

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remover.sh          \
      --parameter_file DEV-NOEU-SAP01-X00.tfvars           \
      --type sap_system
```

### <a name="remove-sap-workload-zone"></a>Eliminación de la zona de cargas de trabajo de SAP

Vaya a la subcarpeta `DEV-XXXX-SAP01-INFRASTRUCTURE` dentro de la carpeta `LANDSCAPE`. Luego, ejecute el comando siguiente.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-NOEU-SAP01-INFRASTRUCTURE

${DEPLOYMENT_REPO_PATH}/deploy/scripts/remover.sh          \
      --parameter_file DEV-NOEU-SAP01-INFRASTRUCTURE.tfvars           \
      --type sap_landscape
```

### <a name="remove-control-plane"></a>Eliminación del plano de control

Inicie sesión en [Cloud Shell](https://shell.azure.com).

Vaya a la carpeta `WORKSPACES`.
  
```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/
```

Exporte las dos variables de entorno siguientes.

```bash
export DEPLOYMENT_REPO_PATH=~/Azure_SAP_Automated_Deployment/sap-hana

export ARM_SUBSCRIPTION_ID=<subscriptionID>
```

Ejecute el siguiente comando.

```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/remove_region.sh                                                    \
  --deployer_parameter_file DEPLOYER/MGMT-NOEU-DEP00-INFRASTRUCTURE/MGMT-NOEU-DEP00-INFRASTRUCTURE.tfvars  \
  --library_parameter_file LIBRARY/MGMT-NOEU-SAP_LIBRARY/MGMT-NOEU-SAP_LIBRARY.tfvars
```

Compruebe que se hayan limpiado todos los recursos.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración del plano de control](automation-configure-control-plane.md)


