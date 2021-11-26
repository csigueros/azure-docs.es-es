---
title: Descarga de software de SAP para el marco de automatización
description: Descargue el software de SAP en su entorno de Azure mediante cuadernos de estrategias de Ansible para usar el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: d93f1d9daa433a6abcd6ad54426060795708c9ef
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725280"
---
# <a name="download-sap-software"></a>Descarga de software de SAP

Necesita una copia del software de SAP para poder usar [el marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md). [Prepare el entorno de Azure](#configure-key-vault) para que pueda colocar los medios de SAP en la cuenta de almacenamiento. Luego, [descargue el software de SAP mediante cuadernos de estrategias de Ansible](#download-sap-software).

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de usuario de SAP (cuenta de usuario de SAP o cuenta usuario de S) con privilegios de descarga de software.

## <a name="configure-key-vault"></a>Configuración del almacén de claves

En primer lugar, configure los secretos del almacén de claves del implementador. Para esta configuración de ejemplo, el grupo de recursos es `DEMO-EUS2-DEP00-INFRASTRUCTURE` o `DEMO-SCUS-DEP00-INFRASTRUCTURE`.

1. [Inicie sesión en el CLI de Azure](/cli/azure/authenticate-azure-cli) con la cuenta que desea usar.

    ```azurecli-interactive
    az login
    ```

1. Agregue un secreto con el nombre de usuario de la cuenta de usuario de SAP. Reemplace `<keyvault-name>` por el nombre del almacén de claves del implementador. Además, reemplace `<sap-username>` por el nombre de usuario de SAP.

    ```azurecli-interactive
     az keyvault secret set --name "S-Username" --vault-name "<keyvault-name>" --value "<sap-username>";
    ```

2. Agregue un secreto con la contraseña de la cuenta de usuario de SAP. Reemplace `<keyvault-name>` por el nombre del almacén de claves del implementador. Además, reemplace `<sap-password>` por la contraseña de SAP.

```azurecli-interactive
az keyvault secret set --name "S-Password" --vault-name "<keyvault-name>" --value "<sap-password>";
```

3. Hay otros dos secretos que se necesitan en este paso para la cuenta de almacenamiento `sapbits`, que el marco de automatización configura automáticamente. Sin embargo, siempre es bueno comprobar si existen o no en el almacén de claves del implementador.

    ```azurecli-interactive
    sapbits-access-key
    sapbits-location-base-path
    ```

## <a name="download-sap-software"></a>Descarga de software de SAP

A continuación, [configure el archivo de parámetros de SAP](#configure-parameters-file) para el proceso de descarga. Luego, [descargue el software de SAP mediante cuadernos de estrategias de Ansible](#download-sap-software). 

### <a name="configure-parameters-file"></a>Configuración del archivo de parámetros

Configure el archivo de parámetros de SAP:

1. Vaya al directorio del área de trabajo de implementación de SAP.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/
    ```

1. Crear un nuevo directorio denominado `BOMS`:

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/BOMS; cd $_
    ```

1. Cree el archivo YAML de parámetros de SAP.

    ```bash
    cat <<EOF > sap-parameters.yaml
    ---
    bom_base_name:               S41909SPS03_v0006ms
    sapbits_location_base_path:  https://<storage_account_FQDN>/sapbits
    kv_name: Name of your Management/Control Plane keyvault
    secret_prefix:
    ...
    EOF
    ```

1. Abra `sap-parameters.yaml` en un editor.

    ```bash
    vi sap-parameters.yaml
    ``` 

1. Actualice los siguientes parámetros:

    1. Cambie el valor de `bom_base_name` a `S41909SPS03_v0006ms`.

    2. Cambie el valor `sapbits_location_base_path` a la ruta de acceso a su cuenta de almacenamiento `sapbits`.

    3. Cambie el valor de `kv_name` por el nombre del almacén de claves del implementador.
   
   4. (Si es necesario) Cambie el valor de `secret_prefix` para que coincida con el prefijo de su entorno (por ejemplo, DEV-WEEU-SAP).
   
### <a name="execute-ansible-playbooks"></a>Ejecución de los cuadernos de estrategias de Ansible

A continuación, ejecute los cuadernos de estrategias de Ansible. Una manera de ejecutar los cuadernos de estrategias es usar el menú de prueba del validador:

1. Ejecute el script de menú de prueba del validador:

    ```bash
    ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/validator_test_menu.sh
    ```

1. Seleccione el cuaderno de estrategias que desea ejecutar. Por ejemplo:
    
    ```output
    1) BoM Downloader
    2) Quit
    Please select playbook: 
    ```


Otra opción es ejecutar los cuadernos de estrategias de Ansible mediante el comando `ansible-playbook`. 

```bash
ansible-playbook                                                                                   \
  --user        azureadm                                                                           \
  --extra-vars="@sap-parameters.yaml"                                                              \
  ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/playbook_bom_downloader.yaml
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción a la implementación del marco de automatización](automation-get-started.md)
