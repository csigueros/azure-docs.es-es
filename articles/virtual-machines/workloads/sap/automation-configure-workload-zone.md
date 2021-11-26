---
title: Acerca de la configuración de la zona de cargas de trabajo en el marco de automatización
description: Introducción al proceso de configuración de la zona de cargas de trabajo de SAP dentro del marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 4fd67edf5f6668efa26fd3c6c6f4adf7a3707c24
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725352"
---
# <a name="workload-zone-configuration-in-sap-automation-framework"></a>Configuración de la zona de cargas de trabajo en el marco de automatización de SAP

Normalmente, las [aplicaciones de SAP](automation-deployment-framework.md#sap-concepts) tienen varios niveles de desarrollo. Por ejemplo, puede tener los niveles de desarrollo, control de calidad y producción. El [marco de automatización de implementaciones de SAP en Azure](automation-deployment-framework.md) hace referencia a estos niveles como [zonas de cargas de trabajo](automation-deployment-framework.md#deployment-components).

## <a name="workload-zone-deployment-configuration"></a>Configuración de la implementación de la zona de cargas de trabajo

La configuración de la zona de cargas de trabajo de SAP se realiza mediante un archivo de variables tfvars de Terraform.

## <a name="terraform-parameters"></a>Parámetros de Terraform

La tabla siguiente contiene los parámetros de Terraform; estos parámetros se deben especificar manualmente si no se usan los scripts de implementación.


| Variable                | Tipo       | Descripción                           | 
| ----------------------- | ---------- | ------------------------------------- | 
| `tfstate_resource_id`   | Se requiere * | Identificador de recursos de Azure para la cuenta de Storage en la biblioteca SAP que contendrá los archivos de estado de Terraform  |
| `deployer_tfstate_key`  | Se requiere * | El nombre del archivo de estado del implementador.  |

## <a name="generic-parameters"></a>Parámetros genéricos

La tabla siguiente contiene los parámetros que definen el grupo de recursos y la nomenclatura de los recursos.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Descripción                           | Tipo  |
> | ----------------------- | ------------------------------------- | ----- |
> | `environment`           | Identificador de cinco caracteres para la zona de cargas de trabajo. Por ejemplo, `PROD` para un entorno de producción y `NP` para un entorno que no sea de producción.| Requerido |
> | `location`              | La región de Azure en la que se va a implementar.     | Requerido |
> | `resource_group_name`   | Nombre del grupo de recursos que se va a crear. | Opcionales |
> | `resource_group_arm_id` | Identificador de recursos de Azure para un grupo de recursos existente | Opcionales |


## <a name="network-parameters"></a>Parámetros de red

El marco de automatización admite tanto la creación de la red virtual y las subredes para nuevas implementaciones del entorno (campo verde) como el uso de una red virtual existente y las subredes existentes para implementaciones de entorno existentes (campo marrón) o una combinación de las nuevas implementaciones del entorno y de las implementaciones del entorno existentes.
 - En el escenario de campo verde, se deben especificar el espacio de direcciones de la red virtual y los prefijos de direcciones de la subred. 
 - En el escenario del campo marrón, se debe especificar el identificador de recursos de Azure para la red virtual y las subredes.

Asegúrese de que el espacio de direcciones de la red virtual sea lo suficientemente grande como para hospedar todos los recursos.

La tabla siguiente contiene los parámetros de red.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                         | Descripción                                                          | Tipo      | Notas  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_name`                   | El nombre lógico de la red.                                      | Requerido  |        |       
> | `network_arm_id`                 | Identificador de recursos de Azure para la red virtual                | Opcional  | Para implementaciones de entorno existentes. |
> | `network_address_space`          | El intervalo de direcciones de la red virtual.                            | Mandatory | Para nuevas implementaciones del entorno.   |
> | `admin_subnet_name`              | El nombre de la subred `admin`.                                       | Opcional  |         |
> | `admin_subnet_address_prefix`    | El intervalo de direcciones de la subred `admin`.                             | Mandatory | Para nuevas implementaciones del entorno.  |
> | `admin_subnet_arm_id`            | Identificador de recursos de Azure para la subred `admin`.                 | Mandatory | Para implementaciones de entorno existentes. |
> | `admin_subnet_nsg_name`          | Nombre del grupo de seguridad de red `admin`.                  | Opcional   |         |
> | `admin_subnet_nsg_arm_id`        | Identificador de recursos de Azure para el grupo de seguridad de red `admin`. | Mandatory | Para implementaciones de entorno existentes. |
> | `db_subnet_name`                 | El nombre de la subred `db`.                                          | Opcional  |         |
> | `db_subnet_address_prefix`       | El intervalo de direcciones para la subred `db`.                                | Mandatory | Para nuevas implementaciones del entorno.  |
> | `db_subnet_arm_id`               | Identificador de recursos de Azure para la subred `db`.                    | Mandatory | Para implementaciones de entorno existentes. |
> | `db_subnet_nsg_name`             | Nombre del grupo de seguridad de red `db`.                     | Opcional  |          |
> | `db_subnet_nsg_arm_id`           | Identificador de recursos de Azure para el grupo de seguridad de red `db`.    | Mandatory | Para implementaciones de entorno existentes. |
> | `app_subnet_name`                | El nombre de la subred `app`.                                         | Opcional  |          |
> | `app_subnet_address_prefix`      | El intervalo de direcciones para la subred `app`.                               | Mandatory | Para nuevas implementaciones del entorno.  |
> | `app_subnet_arm_id`              | Identificador de recursos de Azure para la subred `app`.                   | Mandatory | Para implementaciones de entorno existentes. |
> | `app_subnet_nsg_name`            | Nombre del grupo de seguridad de red `app`.                    | Opcional  |          |
> | `app_subnet_nsg_arm_id`          | Identificador de recursos de Azure para el grupo de seguridad de red `app`.   | Mandatory | Para implementaciones de entorno existentes. |
> | `web_subnet_name`                | El nombre de la subred `web`.                                         | Opcional  |          |
> | `web_subnet_address_prefix`      | El intervalo de direcciones para la subred `web`.                               | Mandatory | Para nuevas implementaciones del entorno.  |
> | `web_subnet_arm_id`              | Identificador de recursos de Azure para la subred `web`.                   | Mandatory | Para implementaciones de entorno existentes. |
> | `web_subnet_nsg_name`            | Nombre del grupo de seguridad de red `web`.                    | Opcional  |          |
> | `web_subnet_nsg_arm_id`          | Identificador de recursos de Azure para el grupo de seguridad de red `web`.   | Mandatory | Para implementaciones de entorno existentes. |

## <a name="iscsi-parameters"></a>Parámetros de ISCSI


> [!div class="mx-tdCol2BreakAll "]
> | Variable                         | Descripción                                                               | Tipo      | Notas                                  |
> | -------------------------------- | ------------------------------------------------------------------------- | --------- | -------------------------------------- |
> | `iscsi_subnet_name`              | El nombre de la subred `iscsi`.                                            | Opcional  |                                        |
> | `iscsi_subnet_address_prefix`    | El intervalo de direcciones para la subred `iscsi`.                                  | Mandatory | Para nuevas implementaciones del entorno.        |
> | `iscsi_subnet_arm_id`            | Identificador de recursos de Azure para la subred `iscsi`.                      | Mandatory | Para implementaciones de entorno existentes.   |
> | `iscsi_subnet_nsg_name`          |  Nombre del grupo de seguridad de red `iscsi`.                      | Opcional  |                                        |
> | `iscsi_subnet_nsg_arm_id`        | Identificador de recursos de Azure para el grupo de seguridad de red `iscsi`.      | Mandatory | Para implementaciones de entorno existentes.   |
> | `iscsi_count`                    | Escriba el número de máquinas virtuales con iSCSI.                                      | Opcional  |                                        |   
> | `iscsi_use_DHCP`                 | Controla si se usan las direcciones IP dinámicas que proporciona la subred de Azure. | Opcional  |                                        |
> | `iscsi_image`                    | Define la imagen de máquina virtual que se va a usar, consulte a continuación.                       | Opcional  |                                        |
> | `iscsi_authentication_type`      | Define la autenticación predeterminada para las máquinas virtuales con iSCSI.         | Opcional  |                                        |
> | `iscsi__authentication_username` | Nombre de cuenta de administrador.                                                | Opcional  |                                        |
> | `iscsi_nic_ips`                  | Direcciones IP para las máquinas virtuales con iSCSI                               | Opcional  | Se ignora si se define `iscsi_use_DHCP`. |
 


```python 
{ 
os_type=""
source_image_id=""
publisher="SUSE"
offer="sles-sap-12-sp5"
sku="gen1"
version="latest"
}
```

### <a name="authentication-parameters"></a>Parámetros de autenticación


En la tabla siguiente se definen las credenciales usadas para definir la autenticación de la máquina virtual.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                          | Tipo        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | Nombre de cuenta de administrador.           | Opcional    |
> | `automation_password`              | Contraseña de administrador               | Opcional    |
> | `automation_path_to_public_key`    | Ruta de acceso a clave pública existente          | Opcional    |
> | `automation_path_to_private_key`   | Ruta de acceso a clave privada existente         | Opcional    |


## <a name="key-vault-parameters"></a>Parámetros de Key Vault

En la tabla siguiente se definen los parámetros que se usan para definir la información de Key Vault.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                                                               | Tipo          | 
> | ---------------------------------- | ------------------------------------------------------------------------- | ------------- | 
> | `user_keyvault_id`                 | Identificador de recursos de Azure para el almacén de claves de credenciales del sistema.            | Opcional      | 
> | `spn_keyvault_id`                  | Identificador de recursos de Azure para el almacén de claves de credenciales de implementación (SPN). | Opcional      | 


## <a name="dns"></a>DNS


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                                                    | Tipo        | 
> | ---------------------------------- | -------------------------------------------------------------- | ----------- | 
> | `dns_label`                        | Si se especifica, es el nombre DNS de la zona de DNS privado.          | Opcional    | 
> | `dns_resource_group_name`          | Nombre del grupo de recursos que contiene la zona de DNS privado | Opcional    | 


## <a name="azure-netapp-support"></a>Compatibilidad con Azure NetApp


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                                                            | Tipo         | Notas  |
> | ---------------------------------- | -----------------------------------------------------------------------| -----------  | ------ |
> | `use_ANF`                          | Si se especifica, implementa la cuenta de Azure NetApp Files y el grupo de capacidad. | Opcional     | |
> | `ANF_account_arm_id`               | Identificador de recursos de Azure para la cuenta de Azure NetApp Files.           | Opcional     | Para implementaciones de entorno existentes. |
> | `ANF_account_name`                 | Nombre de la cuenta de Azure NetApp Files.                                | Opcional     | |
> | `ANF_service_level`                | Nivel de servicio del grupo de capacidad de Azure NetApp Files                 | Opcional     | |
> | `ANF_pool_size`                    | El tamaño (en GB) del grupo de capacidad de Azure NetApp Files               | Opcional     | |
> | `anf_subnet_name`                  | El nombre de la subred ANF.                                             | Opcional     | |
> | `anf_subnet_arm_id`                | Identificador de recursos de Azure para la subred `ANF`.                     | Requerido     | Para implementaciones de entorno existentes. |
> | `anf_subnet_address_prefix`        | El intervalo de direcciones para la subred `ANF`.                                 | Requerido     | Para nuevas implementaciones del entorno.  |


## <a name="other-parameters"></a>Otros parámetros


> [!div class="mx-tdCol2BreakAll "]
> | Variable                             | Descripción                                                            | Tipo     | Notas                                 |
> | ------------------------------------ | ---------------------------------------------------------------------- | -------- | ------------------------------------- |
> | `enable_purge_control_for_keyvaults` | Marca booleana que controla si el control de purga está habilitado en el almacén de claves. | Opcional | Se usa solo para implementaciones de prueba.         |
> | `use_private_endpoint`               | Marca booleana que controla si se usan puntos de conexión privados.                 | Opcional |                                       |
> | `diagnostics_storage_account_arm_id` | Identificador de recursos de Azure para la cuenta de almacenamiento de diagnósticos      | Requerido | Para implementaciones de entorno existentes.  |
> | `witness_storage_account_arm_id`     | Identificador de recursos de Azure para la cuenta de almacenamiento de testigos          | Requerido | Para implementaciones de entorno existentes.  |


## <a name="next-step"></a>siguiente paso

> [!div class="nextstepaction"]
> [Acerca de la implementación del sistema SAP en el marco de automatización](automation-deploy-workload-zone.md)
