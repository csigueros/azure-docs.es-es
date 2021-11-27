---
title: Introducción a la implementación manual del marco de automatización
description: Implemente manualmente el marco de automatización de la implementación de SAP en Azure mediante una configuración de ejemplo y archivos de parámetros de ejemplo.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: cd8250d690a07fe8999274779ce00d3ac8fa04dc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725303"
---
# <a name="get-started-with-manual-deployment"></a>Introducción a la implementación manual

Junto con la [implementación automatizada](automation-get-started.md), también puede realizar la implementación manual del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md). Use esta configuración de ejemplo y los archivos de parámetros de ejemplo para empezar.

> [!TIP]
> En esta guía solo se explica cómo realizar una **implementación manual**. Si quiere empezar a trabajar rápidamente, consulte en su lugar la [guía de implementación **automatizada**](automation-get-started.md).

Estos pasos usan la [convención de nomenclatura predeterminada para](automation-naming.md) el marco de automatización y hacen referencia a ella. Los valores de ejemplo también se usan para asignar nombres a lo largo del código. Por ejemplo, el nombre del implementador es `DEMO-EUS2-DEP00-INFRASTRUCTURE`. En este ejemplo, el entorno es una demostración (`DEMO`), la región es **Este de EE. UU. 2** (`EUS2`) y la red virtual del implementador es `DEP00`. 

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de Azure con privilegios para crear una entidad de servicio. 
- Una [descarga del software de SAP](automation-software.md) en el entorno de Azure.

## <a name="deployer-setup"></a>Configuración del implementador

Antes de empezar, [compruebe que está en la suscripción de Azure correcta](#check-azure-subscription). A continuación, configure el implementador:

1. [Descargue e instale Terraform](#download-terraform). 
1. [Clone y configure el repositorio del marco de automatización](#set-up-repository) en el implementador. 
1. [Inicialización de Terraform](#initialize-terraform)
1. [Obtenga las claves SSH](#get-ssh-keys) para usarlas en el resto de la implementación.
### <a name="check-azure-subscription"></a>Comprobación de la suscripción de Azure

Compruebe que usa la suscripción de Azure adecuada:

1. [Inicie sesión en Azure Portal](https://portal.azure.com/).

1. [Abra Azure Cloud Shell](https://shell.azure.com/).

1. Compruebe que está en la suscripción que desea usar:

    ```azurecli-interactive
    az account list --output=table | grep -i true
    ```

1. Si es necesario, [cambie la suscripción activa](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) a la suscripción que desea usar. 

### <a name="download-terraform"></a>Descarga de Terraform

Descargue Terraform en su entorno:

1. Cree un nuevo directorio, `bin`, y vaya a él.

    ```azurecli-interactive
    mkdir -p ~/bin; cd $_
    ```

1. Recupere el binario de Terraform adecuado. Por ejemplo:

    ```azurecli-interactive
    wget  https://releases.hashicorp.com/terraform/0.14.7/terraform_0.14.7_linux_amd64.zip
    ```

1. Descomprima el binario de Terraform. Por ejemplo:

    ```azurecli-interactive
    unzip terraform_0.14.7_linux_amd64.zip
    ```

1. Compruebe la descarga de Terraform:

    ```azurecli-interactive
    hash terraform
    ```

1. Cree un directorio para la implementación automatizada de SAP.

    ```azurecli-interactive
    mkdir -p ~/Azure_SAP_Automated_Deployment; cd $_
    ```

## <a name="set-up-repository"></a>Configuración del repositorio

Clone y configure el [repositorio del marco de automatización](https://github.com/Azure/sap-hana).

1. Clone el repositorio de GitHub:

    ```azurecli-interactive
    git clone https://github.com/Azure/sap-hana.git
    ```

1. Vaya a la carpeta `sap-hana`.

    ```azurecli-interactive
    cd  ~/Azure_SAP_Automated_Deployment/sap-hana
    ```

1. Opcionalmente, consulte una rama diferente de la rama principal. La rama principal del repositorio es la predeterminada.
    
    1. Reemplace `<branch>` por el nombre de rama o el hash de confirmación que desea usar.

        ```azurecli
        git checkout <branch>
        ```

    1. Compruebe que la rama está en la revisión esperada.

        ```azurecli-interactive
        git rev-parse HEAD
        ```

### <a name="initialize-terraform"></a>Inicialización de Terraform

1. Cree un directorio de trabajo. El nombre del directorio debe observar la [convención de nomenclatura predeterminada](automation-naming.md). Por ejemplo:

    ```azurecli-interactive
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/DEMO-EUS2-DEP00-INFRASTRUCTURE; cd $_
    ```

1. Cree el archivo de parámetros JSON.

    ```azurecli-interactive
    cat <<EOF > DEMO-EUS2-DEP00-INFRASTRUCTURE.json
    {
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnets": {
          "management": {
            "name"                            : "DEP00",
            "address_space"                   : "10.0.0.0/25",
            "subnet_mgmt": {
              "prefix"                        : "10.0.0.64/28"
            },
            "subnet_fw": {
              "prefix"                        : "10.0.0.0/26"
            }
          }
        }
      },
      "options": {
        "enable_deployer_public_ip"           : true
      },
      "firewall_deployment"                   : true
    }
    EOF
    ```

1. Inicialice Terraform.

    ```azurecli-interactive
    terraform init  ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. Cree un plan de ejecución de Terraform que siga la convención de nomenclatura predeterminada.

    ```azurecli-interactive
    terraform plan                                                                    \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                    \
                    ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. Aplique el plan de ejecución de Terraform para implementar los recursos.

    ```azurecli-interactive
    terraform apply --auto-approve                                                    \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                    \
                    ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. Observe la salida.

### <a name="get-ssh-keys"></a>Obtención de las claves SSH

1. Con la salida de la [implementación de Terraform](#initialize-terraform), tenga en cuenta los valores de los campos siguientes.

    1. Dirección IP pública: `deployer_public_ip_address`.

    1. Nombre de usuario del almacén de claves: `deployer_kv_user_name`.

    1. Nombre del almacén de claves privado: `deployer_kv_prvt_name`.

    1. Nombre de la clave pública: `deployer_public_key_secret_name`.

    1. Nombre de la clave privada: `deployer_private_key_secret_name`.

1. Ejecute el script de procesamiento posterior.

    ```azurecli-interactive
    ./post_deployment.sh
    ```

1. Extraiga la clave SSH privada:
    
    ```azurecli-interactive
    az keyvault secret show               \
      --vault-name DEMOEUS2DEP00userE27   \
      --name DEMO-EUS2-DEP00-sshkey     | \
      jq -r .value > sshkey
    
    ```

1. Extraiga la clave SSH pública:
    
    ```azurecli-interactive
    az keyvault secret show               \
      --vault-name DEMOEUS2DEP00userF6A   \
      --name DEMO-EUS2-DEP00-sshkey-pub | \
      jq -r .value > sshkey.pub
    
    ```

1. Descargue el par de claves pública y privada. En el menú de Cloud Shell, seleccione **Cargar/Descargar archivos** &gt; **Descargar**.

## <a name="service-principal-configuration"></a>Configuración de la entidad de servicio

El implementador usa una entidad de servicio para implementar recursos en una suscripción. 

1. Inicie sesión en la CLI de Azure.

    ```azurecli-interactive
    az login
    ```

1. Crear una entidad de servicio. Asegúrese de reemplazar `<subscription-id>` por el id. de su suscripción de Azure. Reemplace también `<sp-name>` por un nombre para la entidad de servicio.

    ```azurecli-interactive
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --name="<sp-name>"
    ```

1. Observe la salida, que contiene información sobre la entidad de servicio. Copie los valores de los siguientes campos:

    1. Identificador de aplicación: `appId`.

    1. Contraseña: `password`.

    1. Identificador de inquilino: `tenant`.

1. Cree una asignación de roles para la entidad de servicio. Asegúrese de reemplazar `<appId>` por el identificador de aplicación que anotó en el paso anterior.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --role "User Access Administrator"
    ```

1. Agregue claves para la entidad de servicio al almacén de claves como se muestra a continuación. Asegúrese de reemplazar los valores de marcador de posición por la información que anotó en los pasos anteriores. Reemplace `<environment>` por el nombre del entorno, por ejemplo, `DEMO`. 

    ```azurecli-interactive
    az keyvault secret set --name "<environment>-subscription-id" --vault-name "<deployer_kv_user_name>" --value "<subscription-id>";
    az keyvault secret set --name "<environment>-tenant-id"       --vault-name "<deployer_kv_user_name>" --value "<tenant>";
    az keyvault secret set --name "<environment>-client-id"       --vault-name "<deployer_kv_user_name>" --value "<appId>";
    az keyvault secret set --name "<environment>-client-secret"   --vault-name "<deployer_kv_user_name>" --value "<password>";
    ```

## <a name="library-configuration"></a>Configuración de la biblioteca

1. Inicie sesión en el implementador mediante el cliente SSH y las claves SSH que recuperó durante la [instalación del implementador](#get-ssh-keys). Si usa PuTTY como cliente SSH, convierta las claves SSH al formato `.ppk` antes de su uso.

1. Vaya a donde ha clonado el repositorio del marco de automatización.

    ```bash
    cd  ~/Azure_SAP_Automated_Deployment/sap-hana
    ```

1. Opcionalmente, consulte una rama diferente de la rama principal. La rama principal del repositorio es la predeterminada.
    
    1. Reemplace `<branch>` por el nombre de rama o el hash de confirmación que desea usar.

        ```azurecli
        git checkout <branch>
        ```

    1. Compruebe que la rama está en la revisión esperada.

        ```azurecli-interactive
        git rev-parse HEAD
        ```

1. Cree un directorio de trabajo.

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/DEMO-EUS2-SAP_LIBRARY; cd $_
    ```

1. Cree el archivo de configuración JSON.

    ```bash
    cat <<EOF > DEMO-EUS2-SAP_LIBRARY.json
    {
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2"
      },
      "deployer": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnet"                                : "DEP00"
      }
    }
    EOF
    ```

1. Inicialice Terraform.

    ```bash
    terraform init  ../../../sap-hana/deploy/terraform/bootstrap/sap_library/
    ```

1. Cree un plan de ejecución de Terraform que siga la convención de nomenclatura predeterminada.

    ```bash
    terraform plan                                                                  \
                --var-file=DEMO-EUS2-SAP_LIBRARY.json                           \
                ../../../sap-hana/deploy/terraform/bootstrap/sap_library

    ```

1. Aplique el plan de ejecución de Terraform para implementar los recursos.

    ```bash
    terraform apply --auto-approve                                                  \
                --var-file=DEMO-EUS2-SAP_LIBRARY.json                           \
                ../../../sap-hana/deploy/terraform/bootstrap/sap_library/

    ```

## <a name="reinitialize-deployment"></a>Reinicialización de la implementación

Reinicialice el [implementador](#reinitialize-deployer) y la [biblioteca SAP](#reinitialize-sap-library).

### <a name="reinitialize-deployer"></a>Reinicialización del implementador

1. Mantenga la sesión iniciada en el implementador en el cliente SSH. O vuelva a iniciar sesión. 

1. Navegue al directorio de trabajo que creó.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LOCAL/DEMO-EUS2-DEP00-INFRASTRUCTURE
    ```

1. Cree otro archivo de parámetros denominado `backend`. De nuevo, siga las convenciones de nomenclatura predeterminadas. Para `resource_group_name`, use el nombre del grupo de recursos donde se encuentra la cuenta de almacenamiento con los archivos de estado de Terraform (`.tfstate`). Para `storage_account_name`, reemplace `<tfstate_storage_account_name>` por el nombre de la cuenta de almacenamiento de la implementación de la biblioteca SAP para archivos `.tfstate`. Para `key`, combine el nombre del grupo de recursos del implementador con la extensión `.terraform.tfstate`. Por ejemplo:

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-DEP00-INFRASTRUCTURE.terraform.tfstate"
    EOF
    ```

1. Vuelva a inicializar Terraform.

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```

1. Cuando se le solicite **¿Desea copiar el estado existente en el nuevo back-end?** , escriba `yes`.

1. Quite el archivo de estado local.

    ```bash
    rm terraform.tfstate*
    ```

1. Creación de un plan de ejecución de Terraform. De nuevo, siga las convenciones de nomenclatura predeterminadas. Por ejemplo:

    ```bash
    terraform plan                                                                  \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```
    
1. Aplique un plan de ejecución de Terraform. Por ejemplo:

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```

### <a name="reinitialize-sap-library"></a>Reinicialización de la biblioteca SAP

1. Mantenga la sesión iniciada en el implementador en el cliente SSH. O vuelva a iniciar sesión. 

1. Navegue al directorio de trabajo que creó.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/DEMO-EUS2-SAP_LIBRARY
    ```

1. Cree otro archivo de parámetros denominado `backend`. De nuevo, siga las convenciones de nomenclatura predeterminadas. Para `resource_group_name`, use el nombre del grupo de recursos donde se encuentra la cuenta de almacenamiento con los archivos de estado de Terraform (`.tfstate`). Para `storage_account_name`, reemplace `<tfstate_storage_account_name>` por el nombre de la cuenta de almacenamiento de la implementación de la biblioteca SAP para archivos `.tfstate`. Para `key`, combine el nombre del grupo de recursos del implementador con la extensión `.terraform.tfstate`. Por ejemplo:

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP_LIBRARY.terraform.tfstate"
    EOF
    ```

1. Agregue un nuevo par clave-valor inmediatamente después del corchete de apertura (`{`) del archivo de parámetros `backend`. Para `tfstate_resource_id`, use el identificador de recurso para la cuenta de almacenamiento de archivos de estado de Terraform. Para `deployer_tfstate_key`, use el nombre de clave para el archivo de estado del implementador. Por ejemplo:
    
    ```bash
    {
        "tfstate_resource_id"                   : "<identifier>",
        "deployer_tfstate_key"                  : "<key>",
        "infrastructure": {
            ...
    }
    ```

1. Vuelva a inicializar Terraform.

    ```bash
    terraform init  --backend-config backend                                          \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

1. Cuando se le solicite **¿Desea copiar el estado existente en el nuevo back-end?** , escriba `yes`.

1. Quite el archivo de estado local.

    ```bash
    rm terraform.tfstate*
    ```

1. Creación de un plan de ejecución de Terraform. De nuevo, siga las convenciones de nomenclatura predeterminadas. Por ejemplo:

    ```bash
    terraform plan                                                                    \
                    --var-file=DEMO-EUS2-SAP_LIBRARY.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

1. Aplique un plan de ejecución de Terraform. Por ejemplo:

    ```bash
    terraform apply --auto-approve                                                    \
                    --var-file=DEMO-EUS2-SAP_LIBRARY.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

## <a name="deploy-workload-virtual-network"></a>Implementación de la red virtual de carga de trabajo

A continuación, implemente la red virtual de carga de trabajo de SAP.

1. Mantenga la sesión iniciada en el implementador en el cliente SSH. O vuelva a iniciar sesión. 

1. Cree un directorio de trabajo. Siga las convenciones de nomenclatura predeterminadas.

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEMO-EUS2-SAP00-INFRASTRUCTURE; cd $_
    ```

1. Cree un archivo de parámetros denominado `backend`. Para `resource_group_name`, use el nombre del grupo de recursos donde se encuentra la cuenta de almacenamiento con los archivos de estado de Terraform (`.tfstate`). Para `storage_account_name`, reemplace `<tfstate_storage_account_name>` por el nombre de la cuenta de almacenamiento de la implementación de la biblioteca SAP para archivos `.tfstate`. Para `key`, combine el nombre del grupo de recursos del implementador con la extensión `.terraform.tfstate`. Por ejemplo:

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP00-INFRASTRUCTURE.terraform.tfstate"
    EOF
    ```

1. Vuelva a inicializar Terraform.

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

1. Creación de un plan de ejecución de Terraform. De nuevo, siga las convenciones de nomenclatura predeterminadas. Por ejemplo:

    ```bash
    terraform plan                                                                  \
                --var-file=DEMO-EUS2-SAP00-INFRASTRUCTURE.json                  \
                ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

1. Aplique un plan de ejecución de Terraform. Por ejemplo:

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-SAP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

## <a name="sap-deployment-unit"></a>Unidad de implementación de SAP

A continuación, configure la unidad de implementación de SAP.

1. Mantenga la sesión iniciada en el implementador en el cliente SSH. O vuelva a iniciar sesión.

1. Cree un directorio de trabajo. Siga las convenciones de nomenclatura predeterminadas.

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEMO-EUS2-SAP00-X00; cd $_
    ```

1. Cree otro archivo de parámetros denominado `backend`. Para `resource_group_name`, use el nombre del grupo de recursos donde se encuentra la cuenta de almacenamiento con los archivos de estado de Terraform (`.tfstate`). Para `storage_account_name`, reemplace `<tfstate_storage_account_name>` por el nombre de la cuenta de almacenamiento de la implementación de la biblioteca SAP para archivos `.tfstate`. Para `key`, combine el nombre del grupo de recursos del implementador con la extensión `.terraform.tfstate`. Por ejemplo:

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP00-X00.terraform.tfstate"
    EOF
    ```

1. Cree un archivo de parámetros JSON con parámetros de entrada como se muestra a continuación. Asegúrese de reemplazar los valores de ejemplo por sus propios valores.

    ```bash
    cat <<EOF > DEMO-EUS2-SAP00-X00.json
    {
      "tfstate_resource_id"                   : "<resource-id>",
      "deployer_tfstate_key"                  : "DEMO-EUS2-DEP00-INFRASTRUCTURE.terraform.tfstate",
      "landscape_tfstate_key"                 : "DEMO-EUS2-SAP00-INFRASTRUCTURE.terraform.tfstate",
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnets": {
          "sap": {
            "name"                            : "SAP00",
            "subnet_db": {
              "prefix"                        : "0.0.0.0/28"
            },
            "subnet_web": {
              "prefix"                        : "0.0.0.0/28"
            },
            "subnet_app": {
              "prefix"                        : "0.0.0.0/27"
            },
            "subnet_admin": {
              "prefix"                        : "0.0.0.0/27"
            }
          }
        }
      },
      "databases": [
        {
          "platform"                          : "HANA",
          "high_availability"                 : false,
          "size"                              : "S4Demo",
          "os": {
            "publisher"                       : "SUSE",
            "offer"                           : "sles-sap-12-sp5",
            "sku"                             : "gen2",
            "version"                         : "latest"
          }
        }
      ],
      "application": {
        "enable_deployment"                   : true,
        "sid"                                 : "X00",
        "scs_instance_number"                 : "00",
        "ers_instance_number"                 : "10",
        "scs_high_availability"               : false,
        "application_server_count"            : 3,
        "webdispatcher_count"                 : 1,
        "authentication": {
          "type"                              : "key",
          "username"                          : "azureadm"
        }
      }
    }
    EOF
    ```

1. Vuelva a inicializar Terraform.

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
  
    ```

1. Creación de un plan de ejecución de Terraform. De nuevo, siga las convenciones de nomenclatura predeterminadas. Por ejemplo:

    ```bash
    terraform plan                                                                  \
                    --var-file=DEMO-EUS2-SAP00-X00.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
    ```

1. Aplique un plan de ejecución de Terraform. Por ejemplo:

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-SAP00-X00.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
    ```

## <a name="ansible-configuration"></a>Configuración de Ansible

Establezca la configuración mediante la ejecución de cuadernos de estrategias de Ansible. Estos cuadernos de estrategias se encuentran en el repositorio del marco de automatización en `/sap-hana/deploy/ansible`.

| Nombre de archivo          | Descripción                        |
| ----------------- | ---------------------------------- |
| `playbook_01_os_base_config.yaml` | Configuración del sistema operativo (SO) base |
| `playbook_02_os_sap_specific_config.yaml ` | Configuración del sistema operativo específico de SAP |
| `playbook_03_bom_processing.yaml` | Descarga de software de procesamiento de listas de materiales (BOM) de SAP |
| `playbook_04a_sap_scs_install.yaml   ` | Instalación de los servicios centrales de SAP (SCS) |
| `playbook_05a_hana_db_install.yaml ` | Instalación de la base de datos de SAP HANA |
| `playbook_06a_sap_dbload.yaml` | Cargador de bases de datos |
| `playbook_06b_sap_pas_install.yaml` | Instalación del servidor de aplicaciones principal (PAS) de SAP |
| `playbook_06c_sap_app_install.yaml` | Instalación del servidor de aplicaciones de SAP |
| `playbook_06d_sap_web_install.yaml` | Instalación del distribuidor web de SAP |
| `playbook_06_00_00_pacemaker.yaml` | Configuración del clúster de Pacemaker |
| `playbook_06_00_01_pacemaker_scs.yaml` | Configuración de Pacemaker para SCS | 
| `playbook_06_00_03_pacemaker_hana.yaml` | Configuración de Pacemaker para la base de datos de SAP HANA |

Para ejecutar un cuaderno de estrategias, o varios, use el comando `ansible-playbook` como se muestra a continuación. Asegúrese de cambiar todos los valores de marcador de posición por su propia información:

- Cambie `<your-sapbits-path>` a la ruta de acceso a la cuenta de almacenamiento de la biblioteca SAP `sapbits`.
- Cambie `<azure-admin>` a su nombre de usuario de administrador de Azure.
- Cambie `<ssh-key`> a la clave SSH privada que desea usar.
- Cambie otros valores en `--extra-vars` según sea necesario para la configuración.

Si experimenta problemas, asegúrese de que ha [descargado el software de SAP](automation-software.md) en su entorno de Azure.

```azurecli-interactive
export           ANSIBLE_HOST_KEY_CHECKING=False
# export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=Yes
# export           ANSIBLE_KEEP_REMOTE_FILES=1


ansible-playbook                                                                                                      \
  --inventory   new-hosts.yaml                                                                                        \
  --user        <azure-admin>                                                                                              \
  --private-key <ssh-key>                                                                                                \
  --extra-vars="{                                                                                                     \
                  \"bom_base_name\":                \"HANA_2_00_053_v001\",                                           \
                  \"download_templates\":           \"false\",                                                        \
                  \"sapbits_location_base_path\":   \"<your-sapbits-path>",        \
                  \"target_media_location\":        \"/usr/sap/install\",                                             \
                  \"sap_sid\":                      \"X00\",                                                          \
                  \"hdb_sid\":                      \"HDB\"                                                           \
                }"                                                                                                    \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_00_transition_start_for_sap_install_refactor.yaml     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_01_os_base_config.yaml                       \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_02_os_sap_specific_config.yaml               \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_03_bom_processing.yaml                       \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_04a_sap_scs_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_05a_hana_db_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06a_sap_dbload.yaml                          \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06b_sap_pas_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06c_sap_app_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06d_sap_web_install.yaml

```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción rápida a una implementación automatizada](automation-get-started.md)
