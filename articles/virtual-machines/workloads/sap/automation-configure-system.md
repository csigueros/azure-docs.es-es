---
title: Configuración de parámetros del sistema SAP para la automatización
description: Defina las propiedades del sistema SAP para el marco de automatización de la implementación de SAP en Azure mediante un archivo JSON de parámetros.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 6b18d33ce3b05809a6dfeb61b3f8337c4d0701cb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725308"
---
# <a name="configure-sap-system-parameters-for-automation"></a>Configuración de parámetros del sistema SAP para la automatización

La configuración del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md)] se produce mediante archivos de parámetros. La información sobre las propiedades del sistema SAP se proporciona en un archivo tfvars, que el marco de automatización usa para la implementación. 

La configuración de la zona de cargas de trabajo de SAP se realiza mediante un archivo de variables tfvars de Terraform.

## <a name="terraform-parameters"></a>Parámetros de Terraform

La tabla siguiente contiene los parámetros de Terraform; estos parámetros se deben especificar manualmente si no se usan los scripts de implementación.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                  | Descripción                                                                                                      | Tipo       |
> | ------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------- | 
> | `tfstate_resource_id`     | Identificador de recursos de Azure para la cuenta de Storage en la biblioteca SAP que contendrá los archivos de estado de Terraform | Se requiere * |
> | `deployer_tfstate_key`    | El nombre del archivo de estado del implementador.                                                                      | Se requiere * |
> | `landscaper_tfstate_key`  | El nombre del archivo de estado de la zona de cargas de trabajo.                                                                 | Se requiere * |

\* = se requiere para las implementaciones manuales
## <a name="generic-parameters"></a>Parámetros genéricos

La tabla siguiente contiene los parámetros que definen el grupo de recursos y la nomenclatura de los recursos.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                | Descripción                           | Tipo       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | Identificador de cinco caracteres para la zona de cargas de trabajo. Por ejemplo, `PROD` para un entorno de producción y `NP` para un entorno que no sea de producción. | Mandatory |
> | `location`              | La región de Azure en la que se va a implementar.     | Requerido   | 
> | `resource_group_name`   | Nombre del grupo de recursos que se va a crear. | Opcional   |
> | `resource_group_arm_id` | Identificador de recursos de Azure para un grupo de recursos existente | Opcional   | 

## <a name="network-parameters"></a>Parámetros de red

Si las subredes no se implementan mediante la implementación de la zona de cargas de trabajo, se pueden agregar en el archivo tfvars del sistema.

El marco de automatización admite tanto la creación de la red virtual y las subredes para nuevas implementaciones del entorno (campo verde) como el uso de una red virtual existente y las subredes existentes para implementaciones de entorno existentes (campo marrón) o una combinación de las nuevas implementaciones del entorno y de las implementaciones del entorno existentes.
 - En el escenario de campo verde, se deben especificar el espacio de direcciones de la red virtual y los prefijos de direcciones de la subred. 
 - En el escenario del campo marrón, se debe especificar el identificador de recursos de Azure para la red virtual y las subredes.

Asegúrese de que el espacio de direcciones de la red virtual sea lo suficientemente grande como para hospedar todos los recursos.

La tabla siguiente contiene los parámetros de red.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                         | Descripción                                                          | Tipo      | Notas  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_logical_name`           | El nombre lógico de la red.                                      | Requerido  |        |       
> | `network_address_space`          | El intervalo de direcciones de la red virtual.                            | Mandatory | Para nuevas implementaciones del entorno.   |
> | `admin_subnet_name`              | El nombre de la subred "admin".                                       | Opcional  |         |
> | `admin_subnet_address_prefix`    | El intervalo de direcciones de la subred "admin".                             | Mandatory | Para nuevas implementaciones del entorno.  |
> | `admin_subnet_arm_id`            | El identificador de recursos de Azure para la subred "admin".                 | Mandatory | Para implementaciones de entorno existentes. |
> | `admin_subnet_nsg_name`          | El nombre del grupo de seguridad de red "admin".                  | Opcional  |         |
> | `admin_subnet_nsg_arm_id`        | El identificador de recursos de Azure para el grupo de seguridad de red. | Mandatory | Para implementaciones de entorno existentes. |
> | `db_subnet_name`                 | El nombre de la subred "db".                                          | Opcional  |         |
> | `db_subnet_address_prefix`       | El intervalo de direcciones para la subred "db".                                | Mandatory | Para nuevas implementaciones del entorno.  |
> | `db_subnet_arm_id`               | El identificador de recursos de Azure para la subred "db".                    | Mandatory | Para implementaciones de entorno existentes. |
> | `db_subnet_nsg_name`             | El nombre del grupo de seguridad de red "db".                     | Opcional  |          |
> | `db_subnet_nsg_arm_id`           | El identificador de recursos de Azure para el grupo de seguridad de red "db".    | Mandatory | Para implementaciones de entorno existentes. |
> | `app_subnet_name`                | El nombre de la subred "app".                                         | Opcional  |          |
> | `app_subnet_address_prefix`      | El intervalo de direcciones para la subred "app".                               | Mandatory | Para nuevas implementaciones del entorno.  |
> | `app_subnet_arm_id`              | El identificador de recursos de Azure para la subred "app".                   | Mandatory | Para implementaciones de entorno existentes. |
> | `app_subnet_nsg_name`            | El nombre del grupo de seguridad de red "app".                    | Opcional  |          |
> | `app_subnet_nsg_arm_id`          | El identificador de recursos de Azure para el grupo de seguridad de red "app".   | Mandatory | Para implementaciones de entorno existentes. |
> | `web_subnet_name`                | El nombre de la subred "web".                                         | Opcional  |          |
> | `web_subnet_address_prefix`      | El intervalo de direcciones para la subred "web".                               | Mandatory | Para nuevas implementaciones del entorno.  |
> | `web_subnet_arm_id`              | El identificador de recursos de Azure para la subred "web".                   | Mandatory | Para implementaciones de entorno existentes. |
> | `web_subnet_nsg_name`            | El nombre del grupo de seguridad de red "web".                    | Opcional  |          |
> | `web_subnet_nsg_arm_id`          | El identificador de recursos de Azure para el grupo de seguridad de red "web".   | Mandatory | Para implementaciones de entorno existentes. |

\* = Se requiere para las implementaciones del entorno existentes

### <a name="database-tier-parameters"></a>Parámetros de nivel de base de datos

El nivel de base de datos define la infraestructura del nivel de base de datos; estos son los back-ends de base de datos que se admiten:

- `HANA`
- `DB2`
- `ORACLE`
- `ORACLE-ASM`
- `ASE`
- `SQLSERVER`
- `NONE` (en este caso, no se implementa ningún nivel de base de datos)


> [!div class="mx-tdCol2BreakAll "]
> | Variable                          | Descripción                                                                              | Tipo         | Notas              |
> | --------------------------------  | -----------------------------------------------------------------------------------------| -----------  | ------------------ |
> | `database_platform`               | Define el back-end de la base de datos.                                                             | Requerido     |                    |
> | `database_high_availability`      | Define si el nivel de base de datos que está implementado tiene alta disponibilidad.                                | Opcional     | Consulte [Configuración de alta disponibilidad](automation-configure-system.md#high-availability-configuration). |
> | `database_server_count`           | Define el número de servidores de bases de datos.                                                   | Opcional     | El valor predeterminado es 1. |
> | `database_vm_names`               | Define los nombres de las máquinas virtuales de los servidores de bases de datos si no se puede aceptar la nomenclatura predeterminada. | Opcional    |                    |
> | `database_size`                   | Define la información de dimensionamiento de la base de datos.                                                  | Requerido     | Consulte [Dimensionamiento personalizado](automation-configure-extra-disks.md). |
> | `database_sid`                    | Define el identificador de seguridad de la base de datos.                                                                 | Requerido     |                    |
> | `db_disk_sizes_filename`          | Define el dimensionamiento de la base de datos personalizada.                                                       | Opcional     | Consulte [Dimensionamiento personalizado](automation-configure-extra-disks.md). |
> | `database_sid`                    | Define el identificador de seguridad de la base de datos.                                                                 | Requerido     |                    |
> | `database_vm_image`               | Define la imagen de máquina virtual que se va a usar, consulte a continuación.                                      | Opcional     |                    |
> | `database_vm_use_DHCP`            | Controla si se deben usar las direcciones IP proporcionadas por la subred de Azure (dinámicas).             | Opcional     |                    |
> | `database_vm_db_nic_ips`          | Define las direcciones IP estáticas para los servidores de base de datos (subred de base de datos).               | Opcional     |                    |
> | `database_vm_admin_nic_ips`       | Define las direcciones IP estáticas para los servidores de base de datos (subred de administrador).                  | Opcional     |                    |
> | `database_vm_authentication_type` | Define el tipo de autenticación para las máquinas virtuales de base de datos (clave/contraseña).         | Opcional     |                    | 
> | `database_vm_zones`               | Define las zonas de disponibilidad.                                                           | Opcional     |                    |
> | `database_vm_avset_arm_ids`       | Define los identificadores de recursos de Azure de los conjuntos de disponibilidad existentes.                                | Opcional     | Se usa principalmente junto con el anclado de ANF.| 
> | `database_no_avset`               | Controla si las máquinas virtuales de base de datos se implementan sin conjuntos de disponibilidad         | Opcional     | El valor predeterminado es "false".   |
> | `database_no_ppg`                 | Controla si los servidores de bases de datos no se colocarán en un grupo con ubicación por proximidad.       | Opcional     | El valor predeterminado es "false".   |

La máquina virtual y la imagen del sistema operativo se definen mediante la siguiente estructura: 

```python 
{
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="8.2.2021040902"
}
```

### <a name="common-application-tier-parameters"></a>Parámetros comunes de la capa de aplicación

La capa de aplicación define la infraestructura de la capa de aplicación, que puede constar de servidores de aplicaciones, servidores de servicios centrales y servidores de distribución web.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                                                                 | Tipo       | Notas  |
> | ---------------------------------- | --------------------------------------------------------------------------- | -----------| ------ |
> | `enable_app_tier_deployment`         | Define si se implementa la capa de aplicación.                                 | Opcional     |        |
> | `sid`                                |  Define el identificador de seguridad de la aplicación SAP.                                            | Requerido     |        | 
> | `app_disk_sizes_filename`            | Define el archivo de tamaño de disco personalizado para los servidores de la capa de aplicación.          | Opcional   | Consulte [Dimensionamiento personalizado](automation-configure-extra-disks.md). |
> | `app_tier_authentication_type`     | Define el tipo de autenticación para las máquinas virtuales de la capa de aplicación. | Opcional   |       |
> | `app_tier_use_DHCP`                | Controla si se deben usar las direcciones IP proporcionadas por la subred de Azure (dinámicas).     | Opcional   |       |
> | `app_tier_dual_nics`                 | Define si el servidor de la capa de aplicación va a tener dos interfaces de red.     | Opcional     |       |
> | `app_tier_vm_sizing`                 | Valor de búsqueda que define la SKU de máquina virtual y el diseño del disco para los servidores de la capa de aplicación. | Opcional |

### <a name="application-server-parameters"></a>Parámetros del servidor de aplicaciones.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                               | Descripción                                                                  | Tipo       | Notas  |
> | -------------------------------------- | ---------------------------------------------------------------------------- | -----------| ------ |
> | `application_server_count`             | Define el número de servidores de aplicaciones.                                    | Requerido     | |
> | `application_server_sku`                 | Define la SKU de máquina virtual que se usará.                                       | Opcional   | | 
> | `application_server_image`             | Define la imagen de máquina virtual que se usará.                                     | Requerido   | | 
> | `application_server_zones`             | Define las zonas de disponibilidad en las que se implementan los servidores de aplicaciones. | Opcional   | | 
> | `application_server_app_nic_ips[]`     | Lista de direcciones IP para el servidor de aplicaciones (subred de aplicación)                 | Opcional   | Se ignora si se usa `app_tier_use_DHCP`. |
> | `application_server_app_admin_nic_ips` | Lista de direcciones IP para el servidor de aplicaciones (subred de administrador)               | Opcional   | Se ignora si se usa `app_tier_use_DHCP`. |
> | `application_server_no_ppg`            | Controla el grupo con ubicación por proximidad del servidor de aplicaciones.                        | Opcional   | |
> | `application_server_no_avset`            | Controla la selección de ubicación del conjunto de disponibilidad del servidor de aplicaciones.                       | Opcional   | |
> | `application_server_tags`                | Define la lista de etiquetas que se van a aplicar a los servidores de aplicaciones.              | Opcional   | |

### <a name="sap-central-services-parameters"></a>Parámetros de SAP Central Services.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                               | Descripción                                                          | Tipo      | Notas  |
> | -------------------------------------- | -------------------------------------------------------------------- | ----------| ------ |
> | `scs_high_availability`                | Define si Central Services tiene alta disponibilidad.                  | Opcionales    | Consulte [Configuración de alta disponibilidad](automation-configure-system.md#high-availability-configuration). |
> | `scs_instance_number`                    | El número de instancia de SCS.                                           | Opcionales  |        |
> | `ers_instance_number`                    | El número de instancia de ERS.                                           | Opcionales  |        |
> | `scs_server_count`                     | Define el número de servidores SCS.                                    | Requerido    |        |
> | `scs_server_sku`                         | Define la SKU de máquina virtual que se usará.                               | Opcionales  |        | 
> | `scs_server_image`                     | Define la imagen de máquina virtual que se usará.                             | Requerido  |        | 
> | `scs_server_zones`                     | Define las zonas de disponibilidad en las que se implementan los servidores SCS. | Opcionales  |        | 
> | `scs_server_app_nic_ips[]`             | Lista de direcciones IP para el servidor SCS (subred de aplicación).                 | Opcional  | Se ignora si se usa `app_tier_use_DHCP`. |
> | `scs_server_app_admin_nic_ips`         | Lista de direcciones IP para el servidor SCS (subred de administrador).               | Opcional  | Se ignora si se usa `app_tier_use_DHCP`. |
> | `scs_server_no_ppg`                    | Controla el grupo con ubicación por proximidad del servidor SCS.                        | Opcional  |         |
> | `scs_server_no_avset`                    | Controla la selección de ubicación del conjunto de disponibilidad del servidor SCS                       | Opcional  |         |
> | `scs_server_tags`                        | Define la lista de etiquetas que se van a aplicar a los servidores SCS.              | Opcional  |         |


### <a name="web-dispatcher-parameters"></a>Parámetros de Web Dispatcher.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                                | Descripción                                                              | Tipo      | Notas  |
> | --------------------------------------- | ------------------------------------------------------------------------ | --------- | ------ |
> | `webdispatcher_server_count`              | Define el número de servidores de Web Dispatcher.                             | Requerido  |        |
> | `webdispatcher_server_sku`              | Define la SKU de máquina virtual que se usará.                                   | Opcional  |        | 
> | `webdispatcher_server_image`              | Define la imagen de máquina virtual que se usará.                                 | Opcional  |        | 
> | `webdispatcher_server_zones`              | Define las zonas de disponibilidad en las que se implementan las instancias de Web Dispatcher. | Opcional  |        |
> | `webdispatcher_server_app_nic_ips[]`    | Lista de direcciones IP para Web Dispatcher (subred de aplicación).          | Opcional  | Se ignora si se usa `app_tier_use_DHCP`. |
> | `webdispatcher_server_app_admin_nic_ips`| Lista de direcciones IP para el servidor de distribuidor web (subred de administrador).        | Opcional  | Se ignora si se usa `app_tier_use_DHCP`. |
> | `webdispatcher_server_no_ppg`           | Controla la selección de ubicación del grupo con ubicación por proximidad web.                         | Opcional  | |
> | `webdispatcher_server_no_avset`         | Define la selección de ubicación del conjunto de disponibilidad de Web Dispatcher.                        | Opcional  | |
> | `webdispatcher_server_tags`             | Define la lista de etiquetas que se van a aplicar a los servidores de Web Dispatcher.       | Opcional  | |

### <a name="anchor-virtual-machine-parameters"></a>Parámetros de máquina virtual delimitadora.

El marco de automatización de la implementación de SAP admite tener una máquina virtual delimitadora. La máquina virtual delimitadora será la primera máquina virtual que se implementará y se usará para delimitar el grupo con ubicación por proximidad.

La tabla siguiente contiene los parámetros relacionados con la máquina virtual delimitadora. 


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                                                                       | Tipo        | 
> | ---------------------------------- | --------------------------------------------------------------------------------- | ----------- | 
> | `deploy_anchor_vm`                 | Define si se usa la máquina virtual delimitadora.                                     | Opcional      | 
> | `anchor_vm_sku`                    | Define la SKU de máquina virtual que se va a usar. Por ejemplo, Standard_D4s_v3.              | Opcional    | 
> | `anchor_vm_image`                    | Define la imagen de máquina virtual que se va a usar. Consulte el siguiente código de ejemplo.                              | Opcional     | 
> | `anchor_vm_use_DHCP`               | Controla si se usan las direcciones IP dinámicas que proporciona la subred de Azure.           | Opcional    | 
> | `anchor_vm_accelerated_networking` | Define si la máquina virtual delimitadora está configurada para usar redes aceleradas. | Opcional    | 
> | `anchor_vm_authentication_type`    | Define el tipo de autenticación para la clave y contraseña de la máquina virtual delimitadora.       | Opcional       | 

La máquina virtual y la imagen del sistema operativo se definen mediante la siguiente estructura: 
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

De forma predeterminada, la implementación del sistema SAP usa las credenciales de la zona de cargas de trabajo de SAP. Si el sistema SAP necesita credenciales únicas, se pueden proporcionar mediante estos parámetros.


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                          | Tipo        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | Nombre de cuenta de administrador           | Opcional    |
> | `automation_password`              | Contraseña de administrador               | Opcional    |
> | `automation_path_to_public_key`    | Ruta de acceso a clave pública existente          | Opcional    |
> | `automation_path_to_private_key`   | Ruta de acceso a clave privada existente         | Opcional    |


## <a name="other-parameters"></a>Otros parámetros


> [!div class="mx-tdCol2BreakAll "]
> | Variable                                       | Descripción                           | Tipo        | 
> | ---------------------------------------------- | ------------------------------------- | ----------- | 
> | `resource_offset`                              | Proporciona un desplazamiento para la nomenclatura de recursos. El número de desplazamiento para la nomenclatura de recursos cuando se crean varios recursos. El valor predeterminado es 0, que crea un patrón de nomenclatura de disk0, disk1, y así sucesivamente. Un desplazamiento de 1 crea un patrón de nomenclatura de disk1, disk2, y así sucesivamente. | Opcional    |
> | `disk_encryption_set_id`                       | La clave de cifrado de disco que se usará para cifrar discos administrados mediante las claves proporcionadas por el cliente | Opcional   |
> | `use_loadbalancers_for_standalone_deployments` | Controla si se implementan equilibradores de carga para instalaciones independientes. | Opcional |
> | `bom_name`                                     | Nombre de la lista de materiales de SAP que se va a usar.                                           | Opcional |


## <a name="azure-netapp-support"></a>Compatibilidad con Azure NetApp


> [!div class="mx-tdCol2BreakAll "]
> | Variable                           | Descripción                                                             | Tipo        |
> | ---------------------------------- | ----------------------------------------------------------------------- | ----------- |
> | `use_ANF`                          | Si se especifica, implementa la cuenta de Azure NetApp Files y el grupo de capacidad.  | Opcional    |
> | `anf_sapmnt_volume_size`           | Define el tamaño (en GB) del volumen "sapmnt".                        | Opcional    |
> | `anf_transport_volume_size`        | Define el tamaño (en GB) del volumen "saptransport".                  | Opcional    |


## <a name="high-availability-configuration"></a>Configuración de alta disponibilidad

La configuración de alta disponibilidad para el nivel de base de datos y el nivel de SCS se configura mediante las marcas `database_high_availability` y `scs_high_availability`. 

Las configuraciones de alta disponibilidad usan Pacemaker con agentes de limitación de Azure. Los agentes de limitación deben configurarse para usar una entidad de servicio única con permisos para detener e iniciar máquinas virtuales. Para más información, consulte la sección sobre la [creación de agentes de limitación](high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device).

```azurecli-interactive
az ad sp create-for-rbac --role="Linux Fence Agent Role" --scopes="/subscriptions/<subscriptionID>" --name="<prefix>-Fencing-Agent"
```

Reemplace `<prefix>` por el prefijo de nombre de su entorno, como `DEV-WEEU-SAP01` y `<subscriptionID>` por el identificador de suscripción de la zona de cargas de trabajo.
  
> [!IMPORTANT]
> El nombre de la entidad de servicio del agente de limitación debe ser único en el inquilino. El script asume que ya se ha creado un rol, "Rol del agente de barrera de Linux".
>
> Registre los valores del SPN del agente de limitación.
   > - appId
   > - password
   > - tenant

Los detalles del agente de limitación deben almacenarse en el almacén de claves de la zona de cargas de trabajo mediante una convención de nomenclatura predefinida. Reemplace `<prefix>` por el prefijo de nombre de su entorno, como `DEV-WEEU-SAP01`, y `<workload_kv_name>` por el nombre del almacén de claves del grupo de recursos de la zona de cargas de trabajo y, para los restantes valores, use los valores registrados en el paso anterior y ejecute el script.


```azurecli

    ```azurecli-interactive
    az keyvault secret set --name "<prefix>-fencing-spn-id" --vault-name "<workload_kv_name>" --value "<appId>";
    az keyvault secret set --name "<prefix>-fencing-spn-pwd" --vault-name "<workload_kv_name>" --value "<password>";
    az keyvault secret set --name "<prefix>-fencing-spn-tenant" --vault-name "<workload_kv_name>" --value "<tenant>";
    ```
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación del sistema SAP](automation-deploy-system.md)

