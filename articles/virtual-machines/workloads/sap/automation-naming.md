---
title: Estándares de nomenclatura para el marco de automatización
description: Explicación de las convenciones de nomenclatura para el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: reference
ms.service: virtual-machines-sap
ms.openlocfilehash: 628eb0dc4cef93b8782e38660709ee93230cc616
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725302"
---
# <a name="naming-conventions-for-sap-automation-framework"></a>Convenciones de nomenclatura para el marco de automatización de SAP

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) usa una convención de nomenclatura estándar. La nomenclatura coherente ayuda a que el marco de automatización se ejecute correctamente con Terraform. La nomenclatura estándar le ayuda a implementar el marco de automatización sin problemas. Por ejemplo, la nomenclatura coherente le ayuda a:

- Implementar la infraestructura de red virtual de SAP en cualquier región de Azure compatible.

- Realizar varias implementaciones con redes virtuales con particiones. 

- Implementar el sistema SAP en cualquier zona de carga de trabajo de SAP. 

- Ejecutar instancias normales y de alta disponibilidad (HA).

- Realizar la recuperación ante desastres y el comportamiento de reenvío.

Revise los términos estándar, las rutas de acceso de área y los nombres de variable antes de comenzar la implementación. Si es necesario, también puede [configurar nomenclatura personalizada](automation-naming-module.md).

## <a name="placeholder-values"></a>Valores del marcador de posición

Los formatos de ejemplo de la convención de nomenclatura usan los siguientes valores de marcador de posición.

| Marcador de posición | Concepto | Límite de caracteres | Ejemplo | 
| ----------- | ------- | --------------- | ------- |
| `{ENVIRONMENT}` | Entorno | 5 | `DEV`, `PROTO`, `NP`, `PROD` |
| `{REGION_MAP}` | Mapa de la [región](#azure-region-names) | 4 | `weus` para `westus` |
| `{SAP_VNET}` | Red virtual (VNet) de SAP | 7 |  `SAP0` |
| `{SID}` | Identificador del sistema SAP | 3 | `X01` |
| `{PREFIX}` | Prefijo de recurso de SAP | | `DEV-WEEU-SAP01-X01` |
| `{DEPLOY_VNET}` | Red virtual del implementador | 7 |  |
| `{REMOTE_VNET}` | Red virtual remota | 7 |  |
| `{LOCAL_VNET}` |Red virtual local | 7 |  |
| `{CODENAME}` | Nombre lógico de la versión |  | `version1`, `beta` |
| `{VM_NAME}` | Nombre de la máquina virtual |  |  |
| `{SUBNET}` | Subnet |  |  |
| `{DBSID}` | Identificador del sistema de base de datos |  |  |
| `{DIAG}` | | 5 |  |
| `{RND}` | | 3 |  |
| `{USER}` | | 12 |  |
| `{COMPUTER_NAME}` | | 14 |  |

### <a name="deployer-names"></a>Nombres del implementador

Para obtener una explicación de la columna **Formato**, vea las [definiciones de los valores de marcador de posición](#placeholder-values).

| Concepto | Límite de caracteres | Formato | Ejemplo |
| ------- | --------------- | ------ | ------- |
| Grupo de recursos | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}-INFRASTRUCTURE` |  `MGMT-WEEU-DEP00-INFRASTRUCTURE` |
| Virtual network | 38 (64)  | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}-vnet` | `MGMT-WEEU-DEP00-vnet` |
| Subnet | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deployment-subnet` | `MGMT-WEEU-DEP00_deployment-subnet` |
| Cuenta de almacenamiento | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}{DIAG}{RND}` | `mgmtweeudep00diagxxx` |
| Grupo de seguridad de red (NSG) | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deployment-nsg` | `MGMT-WEEU-DEP00_deployment-nsg` |
| Tabla de rutas | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_routeTable` | `MGMT-WEEU-DEP00_route-table` |
| Componente de interfaz de red | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_{COMPUTER_NAME}-nic` | `-ipconfig1` (No se requiere ninguno para el bloque`ip_configuration`.) |
| Disco | |`{vm.name}-deploy00` | `PROTO-WUS2-DEPLOY_deploy00-disk00` |
| máquina virtual | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_deploy##` | `MGMT-WEEU-DEP00_permweeudep00deploy00` |
| Disco del sistema operativo (SO) | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deploy##-OsDisk` | `PERM-WEEU-DEP00_permweeudep00deploy00-OsDisk` |
| Nombre del equipo | | `{environment[_map]}{DEPLOY_VNET}{region_map}deploy##` | `MGMT-WEEU-DEP00_permweeudep00deploy00` |
| Almacén de claves | 24 | `{ENVIRONMENT}{REGION_MAP}{DEPLOY_VNET}{USER}{RND}` (credenciales de implementación) | `MGMTWEEUDEP00userxxx` |
| Dirección IP pública | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_{COMPUTER_NAME}-pip` | `MGMT-WEEU-DEP00_permweeudep00deploy00-pip` |

### <a name="sap-library-names"></a>Nombres de la biblioteca SAP

Para obtener una explicación de la columna **Formato**, vea las [definiciones de los valores de marcador de posición](#placeholder-values).

| Concepto | Límite de caracteres | Formato | Ejemplo |
| ------- | --------------- | ------ | ------- |
| Resource group | 80 | `{ENVIRONMENT}-{REGION_MAP}-SAP_LIBRARY` | `MGMT-WEEU-SAP_LIBRARY` |
| Cuenta de almacenamiento | 24 | `{ENVIRONMENT}{REGION_MAP}saplib(12CHAR){RND}` | `mgmtweeusaplibxxx` |
| Cuenta de almacenamiento | 24 | `{ENVIRONMENT}{REGION_MAP}tfstate(12CHAR){RND}` | `mgmtweeutfstatexxx` |

### <a name="sap-workload-zone-names"></a>Nombres de la zona de cargas de trabajo de SAP

Para obtener una explicación de la columna **Formato**, vea las [definiciones de los valores de marcador de posición](#placeholder-values).

| Concepto         | Límite de caracteres | Formato | Ejemplo |
| --------------- | --------------- | ------ | ------- |
| Resource group  | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-INFRASTRUCTURE` | `DEV-WEEU-SAP01-INFRASTRUCTURE` |
| Virtual network | 38 (64)         | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-vnet` | `DEV-WEEU-SAP01-vnet` |
| Emparejamiento         | 80              | `{LOCAL_VNET}_to_{REMOTE_VNET}` | `DEV-WEEU-SAP01-vnet_to_MGMT-WEEU-DEP00-vnet` |
| Subnet          | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_utility-subnet` | `DEV-WEEU-SAP01_db-subnet` |
| Grupo de seguridad de red | 80 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_utility-nsg` | `DEV-WEEU-SAP01_dbSubnet-nsg` |
| Tabla de rutas | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_routeTable` | `DEV-WEEU-SAP01_route-table` |
| Cuenta de almacenamiento | 80              | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}diag(5CHAR){RND}` | `devweeusap01diagxxx` |
| Ruta definida por el usuario | | `{remote_vnet}_Hub-udr` | |
| Ruta definida por el usuario (firewall)| | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_firewall-route` |`DEV-WEEU-SAP01_firewall-route` |
| Conjunto de disponibilidad | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi-avset` | |
| Componente de interfaz de red | 80 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##-nic` | |
| Disco | | `{vm.name}-iscsi00` o `${azurerm_virtual_machine.iscsi.*.name}-iscsi00` (código) | `DEV-WEEU-SAP01_iscsi00-iscsi00` |
| máquina virtual | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##` | |
| Disco del sistema operativo | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##-OsDisk` | |
| Nombre del equipo | | `{ENVIRONMENT}_{REGION_MAP}{SAP_VNET}{region_map}iscsi##` | |
| Almacén de claves | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}{USER}{RND}` | `DEVWEEUSAP01userxxx` |
| Cuenta de NetApp |  | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}_netapp_account` | `DEV-WEEU-SAP01_netapp_account` |
| Grupo de capacidad de NetApp | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}_netapp_pool` | `DEV-WEEU-SAP01_netapp_pool` |

### <a name="sap-system-names"></a>Nombres de sistema SAP

Para obtener una explicación de la columna **Formato**, vea las [definiciones de los valores de marcador de posición](#placeholder-values).

| Concepto         | Límite de caracteres | Formato | Ejemplo |
| --------------- | --------------- | ------ | ------- |
| Prefijo de recurso. | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP-VNET}-{SID}` o `{ENVIRONMENT}-{REGION_MAP}-{SAP-VNET}_{CODENAME}-{SID}` | `DEV-WEEU-SAP01-X01` |
| Resource group  | 80              | `{PREFIX}` | `DEV-WEEU-SAP01-X01` |
| Grupo con ubicación por proximidad de Azure (PPG) | | `{PREFIX}_ppg` | |
| Conjunto de disponibilidad | | `{PREFIX}_app-avset` | `DEV-WEEU-SAP01-X01_app-avset` |
| Subnet          | 80              | `{PREFIX}_utility-subnet` | `DEV-WEEU-SAP01_X01_db-subnet` |
| Grupo de seguridad de red | 80 | `{PREFIX}_utility-nsg` | `DEV-WEEU-SAP01_X01_dbSubnet-nsg` |
| Componente de interfaz de red | | `{PREFIX}_{VM_NAME}-{SUBNET}-nic` | `-app-nic`, `-web-nic`, `-admin-nic`, `-db-nic` |
| Nombre de equipo (base de datos) | 14 | `{SID}d{DBSID}##{OS flag l/w}{primary/secondary 0/1}{RND}` | `DEV-WEEU-SAP01-X01_x01dxdb00l0xxx` |
| Nombre del equipo (que no es de base de datos) | 14 | `{SID}{ROLE}##{OS flag l/w}{RND}` | `DEV-WEEU-SAP01-X01_x01app01l538`, `DEV-WEEU-SAP01-X01_x01scs01l538` |
| máquina virtual | | `{PREFIX}_{COMPUTER-NAME}` | |
| Disco | | `{PREFIX}_{VM_NAME}-{disk_type}{counter}` | `{VM-NAME}-sap00`, `{VM-NAME}-data00`, `{VM-NAME}-log00`, `{VM-NAME}-backup00` |
| Disco del sistema operativo | | `{PREFIX}_{VM_NAME}-osDisk` | `DEV-WEEU-SAP01-X01_x01scs00lxxx-OsDisk` |
| Azure Load Balancer (utilidad)| 80 | `{PREFIX}_db-alb` | `DEV-WEEU-SAP01-X01_db-alb` |
| Dirección IP de front-end del equilibrador de carga (utilidad)| | `{PREFIX}_dbAlb-feip` | `DEV-WEEU-SAP01-X01_dbAlb-feip` |
| Grupo de back-end del equilibrador de carga (utilidad)| | `{PREFIX}_dbAlb-bePool` | `DEV-WEEU-SAP01-X01_dbAlb-bePool` |
| Sondeo de estado de Load Balancer (utilidad)| | `{PREFIX}_dbAlb-hp` | `DEV-WEEU-SAP01-X01_dbAlb-hp`|
| Almacén de claves (usuario) | 24 | `{SHORTPREFIX}u{RND}` | `DEVWEEUSAP01uX01xxx` |
| Volumen de NetApp (utilidad) | 24 | `{PREFIX}-utility` | `DEV-WEEU-SAP01-X01_sapmnt` |



> [!NOTE]
> La numeración de discos comienza en cero. La convención de nomenclatura usa un formato de dos caracteres; por ejemplo, `00`.

## <a name="azure-region-names"></a>Nombres de regiones de Azure

El marco de automatización usa formas cortas de nombres de regiones de Azure. Los nombres cortos de las regiones de Azure se asignan a los nombres de región normales.

Puede establecer la asignación en la variable `_region_mapping` en el archivo de configuración del generador de nombres, `../../../deploy/terraform/terraform-units/modules/sap_namegenerator/variables_local.tf`.

A continuación, puede usar la variable `_region_mapping` en otro lugar, como una ruta de acceso de área. El formato de una ruta de acceso de área es `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-{ARTIFACT}`, donde:

- `{ENVIRONMENT}` es el nombre del entorno o la zona de carga de trabajo.
- `{REGION_MAP}` es la forma corta del nombre de la región de Azure.
- `{SAP_VNET}` es la red virtual de SAP dentro del entorno.
- `{ARTIFACT}` es el artefacto de implementación dentro de la red virtual, como `INFRASTRUCTURE`.

Puede usar la variable `_region_mapping` de la siguiente manera:

```
"${upper(var.__environment)}-${upper(element(split(",", lookup(var.__region_mapping, var.__region, "-,unknown")),1))}-${upper(var.__SAP_VNET)}-INFRASTRUCTURE"
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre cómo configurar el módulo de nomenclatura personalizado](automation-naming-module.md)
