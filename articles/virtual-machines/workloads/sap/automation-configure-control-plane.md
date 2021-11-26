---
title: Configuración del plano de control para el marco de automatización
description: Configure el plano de control de implementación para el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: f44a3c7ec4d6f46dcfaf238200a9284e1f7d4efa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725309"
---
# <a name="configure-the-control-plane"></a>Configuración del plano de control

El plano de control del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) consta de los siguientes componentes:
 - Implementador
 - Biblioteca de SAP

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="Diagrama del plano de control.":::

## <a name="deployer"></a>Implementador

El [implementador](automation-deployment-framework.md#deployment-components) es el motor de ejecución del [marco de automatización de SAP](automation-deployment-framework.md). Es una máquina virtual preconfigurada que se usa para ejecutar comandos de Terraform y Ansible. 

La configuración del implementador se realiza en un archivo de variables tfvars de Terraform.

## <a name="terraform-parameters"></a>Parámetros de Terraform

La tabla siguiente contiene los parámetros de Terraform; estos parámetros se deben especificar manualmente si no se usan los scripts de implementación.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Descripción                           | Tipo       |
> | ----------------------- | ------------------------------------- | ---------- | 
> | `tfstate_resource_id`   | Identificador de recursos de Azure para la cuenta de Storage en la biblioteca SAP que contiene los archivos de estado de Terraform | Requerido   | 


### <a name="generic-parameters"></a>Parámetros genéricos

La tabla siguiente contiene los parámetros que definen el grupo de recursos y la nomenclatura de los recursos.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Descripción                           | Tipo       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | Identificador de cinco caracteres para la zona de cargas de trabajo. Por ejemplo, `PROD` para un entorno de producción y `NP` para un entorno que no sea de producción. | Mandatory |
> | `location`              | La región de Azure en la que se va a implementar.     | Requerido   | 
> | `resource_group_name`   | Nombre del grupo de recursos que se va a crear. | Opcionales   |
> | `resource_group_arm_id` | Identificador de recursos de Azure para un grupo de recursos existente | Opcionales   | 

### <a name="network-parameters"></a>Parámetros de red

El marco de automatización admite no solo la creación de la red virtual y las subredes (campo verde), sino también el uso de una red virtual existente y las subredes existentes (campo marrón), o una combinación del campo verde y el campo marrón.
 - En el escenario de campo verde, se deben especificar el espacio de direcciones de la red virtual y los prefijos de direcciones de la subred. 
 - En el escenario del campo marrón, se debe especificar el identificador de recursos de Azure para la red virtual y las subredes.

El CIDR recomendado del espacio de direcciones de red virtual es /27, lo que concede espacio para 32 direcciones IP. Un valor de CIDR de /28 solo permite 16 direcciones IP. Si desea incluir Azure Firewall, use un valor CIDR de /25, ya que Azure Firewall requiere un intervalo de /26. 

El valor de CIDR recomendado para la subred de administración es /28, que permite 16 direcciones IP.
El valor de CIDR recomendado para la subred de administración es /26, que permite 64 direcciones IP.

La tabla siguiente contiene los parámetros de red.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                                    | Descripción                                                      | Tipo       | Notas  |
> | ------------------------------------------  | ---------------------------------------------------------------- | ---------- | ------ |
> | `management_network_name`                   | El nombre lógico de la red (DEV-WEEU-MGMT01-INFRASTRUCTURE) | Requerido   | |
> | `management_network_arm_id`                 | Identificador de recursos de Azure para la red virtual            | Opcional   | Para implementaciones de entorno existentes. |
> | `management_network_address_space`          | El intervalo de direcciones de la red virtual.                        | Mandatory  | Para nuevas implementaciones del entorno. |
> | `management_subnet_name`                    | El nombre de la subred.                                           | Opcional   | |
> | `management_subnet_address_prefix`          | El intervalo de direcciones para la subred.                                 | Mandatory  | Para nuevas implementaciones del entorno. |
> | `management_subnet_arm_id`                  | Identificador de recursos de Azure para la subred.                     | Mandatory  | Para implementaciones de entorno existentes. |
> | `management_subnet_nsg_name`                | El nombre del grupo de seguridad de red.                      | Opcional   | |
> | `management_subnet_nsg_arm_id`              | Identificador de recursos de Azure para el grupo de seguridad de red.     | Mandatory  | Obligatorio para las implementaciones de entorno existentes. |
> | `management_subnet_nsg_allowed_ips`         | Intervalo de direcciones IP permitidas que se agregan a Azure Firewall.           | Opcional   | |  
> | `management_firewall_subnet_arm_id`         | Identificador de recursos de Azure para el grupo de seguridad de red.     | Mandatory  | Para implementaciones de entorno existentes. |
> | `management_firewall_subnet_address_prefix` | El intervalo de direcciones para la subred.                                 | Mandatory  | Para nuevas implementaciones del entorno. | 

### <a name="deployer-virtual-machine-parameters"></a>Parámetros de máquina virtual del implementador

La tabla siguiente contiene los parámetros relacionados con la máquina virtual del implementador. 

> [!div class="mx-tdCol2BreakAll "]
> | Variable                        | Descripción                                                                  | Tipo       |
> | ------------------------------- | ---------------------------------------------------------------------------- | ---------- |
> | `deployer_size`                 | Define la SKU de máquina virtual que se va a usar, por ejemplo, Standard_D4s_v3        | Opcional   |
> | `deployer_image`                | Define la imagen de máquina virtual que se va a usar, consulte a continuación.                          | Opcionales   |
> | `deployer_disk_type`            | Define el tipo de disco, por ejemplo, Premium_LRS                               | Opcional   |
> | `deployer_use_DHCP`             | Controla si se deben usar las direcciones IP proporcionadas por la subred de Azure (dinámicas). | Opcional   |
> | `deployer_private_ip_address`   | Define la dirección IP privada que se va a usar.                                        | Opcional   |
> | `deployer_enable_public_ip`     | Se define si el implementador tiene una IP pública.                                      | Opcional   |

La imagen de máquina virtual se define mediante la siguiente estructura: 
```python 
{ 
  os_type=""
  source_image_id=""
  publisher="Canonical"
  offer="0001-com-ubuntu-server-focal"
  sku="20_04-lts"
  version="latest"
}
```

### <a name="authentication-parameters"></a>Parámetros de autenticación

En la tabla siguiente se definen los parámetros usados para definir la autenticación de la máquina virtual.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                                         | Descripción                                         | Tipo      | 
> | ------------------------------------------------ | --------------------------------------------------- | --------- | 
> | `deployer_vm_authentication_type`                | Define la autenticación predeterminada para el implementador. | Opcional  |
> | `deployer_authentication_username`               | Nombre de cuenta de administrador.                          | Opcional  |
> | `deployer_authentication_password`               | Contraseña de administrador                              | Opcional  |
> | `deployer_authentication_path_to_public_key`     | Ruta de acceso a la clave pública usada para la autenticación.      | Opcional  |
> | `deployer_authentication_path_to_private_key`    | Ruta de acceso a la clave privada usada para la autenticación.     | Opcional  |

### <a name="key-vault-parameters"></a>Parámetros de Key Vault

En la tabla siguiente se definen los parámetros que se usan para definir la información de Key Vault.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                             | Descripción                                                                 | Tipo       | 
> | ------------------------------------ | --------------------------------------------------------------------------- | ---------- | 
> | `user_keyvault_id`                   | Identificador de recursos de Azure para el almacén de claves de usuario.                            | Opcional   |
> | `spn_keyvault_id`                    | Identificador de recursos de Azure para el almacén de claves de usuario que contiene los detalles del nombre de entidad de seguridad de servicio. | Opcional   |
> | `deployer_private_key_secret_name`   | Nombre del secreto de Azure Key Vault para la clave privada del implementador.                | Opcional   |
> | `deployer_public_key_secret_name`    | Nombre del secreto de Azure Key Vault para la clave pública del implementador.                 | Opcional   |
> | `deployer_username_secret_name`      | Nombre del secreto de Azure Key Vault para el nombre de usuario del implementador.                   | Opcional   |
> | `deployer_password_secret_name`      | Nombre del secreto de Azure Key Vault para la contraseña del implementador.                   | Opcionales   |

### <a name="other-parameters"></a>Otros parámetros

> [!div class="mx-tdCol2BreakAll "]
> | Variable                             | Descripción                           | Tipo        | 
> | ------------------------------------ | ------------------------------------- | ----------- | 
> | `firewall_deployment`                | Marca booleana que controla si se va a implementar un firewall de Azure. | Mandatory   | 
> | `enable_purge_control_for_keyvaults` | Marca booleana que controla si el control de purga está habilitado en el almacén de claves. Se usa solo para implementaciones de prueba. | Opcional    | 
> | `use_private_endpoint`               | Marca booleana que controla si se usan puntos de conexión privados. | Opcional    | 

### <a name="example-parameters-file-for-deployer-required-parameters-only"></a>Archivo de parámetros de ejemplo para el implementador (solo parámetros necesarios)

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
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


## <a name="sap-library"></a>Biblioteca de SAP

La [biblioteca de SAP](automation-deployment-framework.md#deployment-components) proporciona el almacenamiento persistente de los archivos de estado de Terraform y el soporte de instalación de SAP descargado para el plano de control. 

La configuración de la biblioteca de SAP se realiza en un archivo de variables tfvars de Terraform.

### <a name="terraform-parameters"></a>Parámetros de Terraform

La tabla siguiente contiene los parámetros de Terraform; estos parámetros se deben especificar manualmente cuando no se usan los scripts de implementación.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Descripción                           | Tipo       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `deployer_tfstate_key`  | Nombre del archivo de estado para el implementador  | Requerido   | 

### <a name="generic-parameters"></a>Parámetros genéricos

La tabla siguiente contiene los parámetros que definen el grupo de recursos y la nomenclatura de los recursos.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Descripción                           | Tipo       |
> | ----------------------- | ------------------------------------- | ---------- |
> | `environment`           | Identificador de cinco caracteres para la zona de cargas de trabajo. Por ejemplo, `PROD` para un entorno de producción y `NP` para un entorno que no sea de producción. | Mandatory |
> | `location`              | La región de Azure en la que se va a implementar.     | Requerido   | 
> | `resource_group_name`   | Nombre del grupo de recursos que se va a crear. | Opcionales   |
> | `resource_group_arm_id` | Identificador de recursos de Azure para un grupo de recursos existente | Opcionales   | 

### <a name="deployer-parameters"></a>Parámetros del implementador

La tabla siguiente contiene los parámetros que definen el grupo de recursos y la nomenclatura de los recursos.

> [!div class="mx-tdCol2BreakAll "]
> | Variable               | Descripción                           | Tipo |
> | ---------------------  | ------------------------------------- | ----- | 
> | `deployer_environment` | Identificador de cinco caracteres para la zona de cargas de trabajo. Por ejemplo, `PROD` para un entorno de producción y `NP` para un entorno que no sea de producción. | Mandatory |
> | `deployer_location`    | La región de Azure en la que se va a implementar.   | Mandatory |
> | `deployer_vnet`        | Nombre lógico de la red virtual del implementador. | Mandatory |


### <a name="sap-installation-media-storage-account"></a>Cuenta de almacenamiento del soporte de instalación de SAP.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                  | Descripción                 | Tipo       | 
> | ------------------------- | --------------------------- | ---------- | 
> | `library_sapmedia_arm_id` | Identificador de recurso de Azure.   | Opcional   |

### <a name="terraform-remote-state-storage-account"></a>Cuenta de almacenamiento de estado remota de Terraform.

> [!div class="mx-tdCol2BreakAll "]
> | Variable                         | Descripción                | Tipo       | 
> | -------------------------------- | -------------------------- | ---------- | 
> | `library_terraform_state_arm_id` | Identificador de recurso de Azure.  | Opcional   |

### <a name="extra-parameters"></a>Parámetros adicionales


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                      | Tipo     | 
> | ---------------------------------- | -------------------------------- | -------- | 
> | `dns_label`                        | Nombre DNS de la zona de DNS privado | Opcionales | 
> | `use_private_endpoint`             | Usar puntos de conexión privados            | Opcionales | 

### <a name="example-parameters-file-for-sap-library-required-parameters-only"></a>Archivo de parámetros de ejemplo para la biblioteca de SAP (solo parámetros necesarios)

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"
# The deployer_environment value is a mandatory field, it is used for identifying the deployer
deployer_environment="MGMT"
# The deployer_location value is a mandatory field, it is used for identifying the deployer
deployer_location="westeurope"
# The deployer_vnet value is a mandatory field, it is used for identifying the deployer
deployer_vnet="DEP00"

```


## <a name="next-step"></a>Paso siguiente

> [!div class="nextstepaction"]
> [Configuración del sistema SAP](automation-configure-system.md)
