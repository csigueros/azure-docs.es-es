---
title: Ejecución de Ansible para configurar el sistema SAP
description: Configure el entorno e instale SAP mediante cuadernos de estrategias de Ansible con el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: a54870554cc1ab1a0c45d62115bbc54f11b41dac
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725339"
---
# <a name="get-started-ansible-configuration"></a>Introducción a la configuración de Ansible

Cuando se usa el [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md), tiene la opción de realizar una [implementación de infraestructura automatizada](automation-get-started.md). Sin embargo, también puede realizar las configuraciones necesarias en el sistema operativo e instalar SAP mediante los cuadernos de estrategias de Ansible que encontrará en el repositorio. Estos cuadernos de estrategias se encuentran en el repositorio del marco de automatización de la carpeta `/sap-hana/deploy/ansible`.

| Nombre de archivo                                   | Descripción                                       |
| ------------------------------------------ | ------------------------------------------------- |
| `playbook_01_os_base_config.yaml`          | Configuración del sistema operativo (SO) base          |
| `playbook_02_os_sap_specific_config.yaml`  | Configuración del sistema operativo específico de SAP                     |
| `playbook_03_bom_processing.yaml`          | Procesamiento de la lista de materiales de SAP (SAP BoM)        |
| `playbook_04_00_00_hana_db_install`        | Instalación de la base de datos de SAP HANA                    |
| `playbook_05_00_00_sap_scs_install.yaml`   | Instalación de los servicios centrales de SAP (SCS)           |
| `playbook_05_01_sap_dbload.yaml`           | Cargador de bases de datos                                   |
| `playbook_05_02_sap_pas_install.yaml`      | Instalación del servidor de aplicaciones principal (PAS) de SAP |
| `playbook_05_03_sap_app_install.yaml`      | Instalación del servidor de aplicaciones de SAP               |
| `playbook_05_04_sap_web_install.yaml`      | Instalación del distribuidor web de SAP                   |
| `playbook_04_00_01_hana_hsr.yaml`          | Configuración de alta disponibilidad de SAP HANA                         |

## <a name="prerequisites"></a>Requisitos previos

Los cuadernos de estrategias de Ansible requieren los siguientes archivos, `sap-parameters.yaml` y `SID_host.yaml`, en el directorio actual.

### <a name="configuration-files"></a>Archivos de configuración

**sap-parameters.yaml** contiene información que Ansible usa para la configuración de la infraestructura de SAP.

```yaml
---

# bom_base_name is the name of the SAP Application Bill of Materials file
bom_base_name:                 S41909SPS03_v0006ms
# Set to true to instruct Ansible to update all the packages on the virtual machines
upgrade_packages:              false 

# TERRAFORM CREATED
sap_fqdn:                      sap.contoso.net                      
# kv_name is the name of the key vault containing the system credentials
kv_name:                       DEVWEEUSAP01user###
# secret_prefix is the prefix for the name of the secret stored in key vault
secret_prefix:                 DEV-WEEU-SAP01

# sap_sid is the application SID
sap_sid:                       X01
# scs_high_availability is a boolean flag indicating 
# if the SAP Central Services are deployed using high availability 
scs_high_availability:         false
# SCS Instance Number
scs_instance_number:           "00"
# scs_lb_ip is the SCS IP address of the load balancer in 
# from of the SAP Central Services virtual machines
scs_lb_ip:                     10.110.32.26
# ERS Instance Number
ers_instance_number:           "02"
# ecs_lb_ip is the ERS IP address of the load balancer in
# from of the SAP Central Services virtual machines
ers_lb_ip:                     

# sap_sid is the database SID
db_sid:                        XDB
# platform
platform:                      HANA

# db_high_availability is a boolean flag indicating if the 
# SAP database servers are deployed using high availability
db_high_availability:          false
# db_lb_ip is the IP address of the load balancer in from of the database virtual machines
db_lb_ip:                      10.110.96.13

disks:
  - { host: 'x01dxdb00l0538', LUN: 0, type: 'sap' }
  - { host: 'x01dxdb00l0538', LUN: 10, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 11, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 12, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 13, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 20, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 21, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 22, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 2, type: 'backup' }
  - { host: 'x01app00l538', LUN: 0, type: 'sap' }
  - { host: 'x01app01l538', LUN: 0, type: 'sap' }
  - { host: 'x01scs00l538', LUN: 0, type: 'sap' }

...
```

**`X01_hosts.yaml`** es el archivo de inventario que Ansible usa para la configuración de la infraestructura de SAP. "X01" puede diferir en las implementaciones.

```yaml
X01_DB:
  hosts:
    x01dxdb00l0538:
      ansible_host        : 10.110.96.12
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key
  vars:
    node_tier             : hana

X01_SCS:
  hosts:
    x01scs00l538:
      ansible_host        : 10.110.32.25
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key
  vars:
    node_tier             : scs

X01_ERS:
  hosts:
  vars:
    node_tier             : ers

X01_PAS:
  hosts:
    x01app00l538:
      ansible_host        : 10.110.32.24
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key 

  vars:
    node_tier             : pas

X01_APP:
  hosts:
    x01app01l538:
      ansible_host        : 10.110.32.15
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key 

  vars:
    node_tier             : app

X01_WEB:
  hosts:
  vars:
    node_tier             : web

```

## <a name="run-a-playbook"></a>Ejecutar un cuaderno de estrategias

Asegúrese de que ha [descargado el software de SAP](automation-software.md) en su entorno de Azure antes de ejecutar este paso.

Para ejecutar un cuaderno de estrategias, o varios, use el comando `ansible-playbook` como se muestra a continuación. En el ejemplo siguiente se ejecuta el cuaderno de estrategias de configuración del sistema operativo.


```bash

sap_params_file=sap-parameters.yaml

if [[ ! -e "${sap_params_file}" ]]; then
        echo "Error: '${sap_params_file}' file not found!"
        exit 1
fi

# Extract the sap_sid from the sap_params_file, so that we can determine
# the inventory file name to use.
sap_sid="$(awk '$1 == "sap_sid:" {print $2}' ${sap_params_file})"

kv_name="$(awk '$1 == "kv_name:" {print $2}' ${sap_params_file})"

prefix="$(awk '$1 == "secret_prefix:" {print $2}' ${sap_params_file})"
password_secret_name=$prefix-sid-password

password_secret=$(az keyvault secret show --vault-name ${kv_name} --name ${password_secret_name} | jq -r .value)

export           ANSIBLE_PASSWORD=$password_secret
export           ANSIBLE_INVENTORY="${sap_sid}_hosts.yaml"
export           ANSIBLE_PRIVATE_KEY_FILE=sshkey
export           ANSIBLE_COLLECTIONS_PATHS=/opt/ansible/collections${ANSIBLE_COLLECTIONS_PATHS:+${ANSIBLE_COLLECTIONS_PATHS}}
export           ANSIBLE_REMOTE_USER=azureadm

# Ref: https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html
# Silence warnings about Python interpreter discovery
export           ANSIBLE_PYTHON_INTERPRETER=auto_silent

# Set of options that will be passed to the ansible-playbook command
playbook_options=(
        --inventory-file="${sap_sid}_hosts.yaml"
        --private-key=${ANSIBLE_PRIVATE_KEY_FILE}
        --extra-vars="_workspace_directory=`pwd`"
        --extra-vars="@${sap_params_file}"
        -e ansible_ssh_pass='{{ lookup("env", "ANSIBLE_PASSWORD") }}'
        "${@}"
)

ansible-playbook "${playbook_options[@]}" ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/playbook_01_os_base_config.yaml

```

