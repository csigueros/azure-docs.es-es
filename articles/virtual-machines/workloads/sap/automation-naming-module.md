---
title: Configuración del módulo de nomenclatura personalizado para el marco de automatización
description: Explicación de cómo implementar convenciones de nomenclatura personalizadas para el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 8e055971ea587e81ded1cb6317579a6a9bdcc8cb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725342"
---
# <a name="configure-custom-naming-module"></a>Configuración del módulo de nomenclatura personalizado

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) usa una convención de nomenclatura estándar para la [nomenclatura de recursos](automation-naming.md) de Azure. 

El módulo `sap_namegenerator` de Terraform define los nombres de todos los recursos que implementa el marco de automatización. En el repositorio, el módulo se encuentra en `/deploy/terraform/terraform-units/modules/sap_namegenerator/`. 

El marco también le permite proporcionar sus propios nombres para algunos de los recursos mediante los [archivos de parámetros](automation-configure-system.md). Si estas funcionalidades no son suficientes, también puede usar la lógica de la nomenclatura personalizada modificando el módulo de nomenclatura que utiliza la automatización.

Hay varios archivos en el módulo para asignar nombres a los recursos:

- Los nombres de máquina virtual y equipo se definen en (`vm.tf`).
- La nomenclatura del grupo de recursos se define en (`resourcegroup.tf`)
- Los almacenes de claves en (`keyvault.tf`)
- Los sufijos de recursos (`variables_local.tf`)

Los distintos nombres de recursos se identifican mediante prefijos en el código de Terraform.
- Las implementaciones del implementador de SAP usan nombres de recursos con el prefijo `deployer_`
- Las implementaciones de la biblioteca de SAP usan nombres de recursos con el prefijo `library`
- Las implementaciones del entorno de SAP usan nombres de recursos con el prefijo `vnet_`
- Las implementaciones del sistema de SAP usan nombres de recursos con el prefijo `sdu_`

Los nombres calculados se devuelven en un diccionario de datos que usan todos los módulos de Terraform. 

## <a name="using-custom-names"></a>Uso de nombres personalizados

Algunos de los nombres de recursos se pueden cambiar proporcionando parámetros en el archivo de parámetros tfvars.

| Recurso               | Parámetro               | Notas                                                              |
| ---------------------- | ----------------------- | ------------------------------------------------------------------ |
| `Prefix`               | `custom_prefix`         | Esto se usa como prefijo para todos los recursos del grupo de recursos. |
| `Resource group`       | `resourcegroup_name`    |                                                                    |
| `admin subnet name`    | `admin_subnet_name`     |                                                                    |
| `admin nsg name`       | `admin_subnet_nsg_name` |                                                                    |
| `db subnet name`       | `db_subnet_name`        |                                                                    |
| `db nsg name`          | `db_subnet_nsg_name`    |                                                                    |
| `app subnet name`      | `app_subnet_name`       |                                                                    |
| `app nsg name`         | `app_subnet_nsg_name`   |                                                                    |
| `web subnet name`      | `web_subnet_name`       |                                                                    |
| `web nsg name`         | `web_subnet_nsg_name`   |                                                                    |
| `admin nsg name`       | `admin_subnet_nsg_name` |                                                                    |

## <a name="changing-the-naming-module"></a>Cambio del módulo de nomenclatura

Para preparar el entorno de Terraform para la nomenclatura personalizada, primero debe crear un módulo de nomenclatura personalizado. La manera más fácil es copiar el módulo existente y realizar los cambios necesarios en el módulo copiado.

1. Cree una carpeta de nivel raíz en el entorno de Terraform. Por ejemplo, `Azure_SAP_Automated_Deployment`.
1. Vaya a la nueva carpeta de nivel raíz.
1. Clone el [repositorio del marco de automatización](https://github.com/Azure/sap-hana). Este paso crea una nueva carpeta `sap-hana`.
1. Cree una carpeta dentro de la carpeta de nivel raíz denominada `Contoso_naming`.
1. Vaya a la carpeta `sap-hana`.
1. Consulte la rama adecuada en Git.
1. Vaya a `\deploy\terraform\terraform-units\modules` en la carpeta `sap-hana`.
1. Copie la carpeta `sap_namegenerator` en la carpeta `Contoso_naming`.

Se llama al módulo de nomenclatura desde las carpetas raíz de Terraform:

```terraform
module "sap_namegenerator" {
  source           = "../../terraform-units/modules/sap_namegenerator"
  environment      = local.infrastructure.environment
  location         = local.infrastructure.region
  codename         = lower(try(local.infrastructure.codename, ""))
  random_id        = module.common_infrastructure.random_id
  sap_vnet_name    = local.vnet_logical_name
  sap_sid          = local.sap_sid
  db_sid           = local.db_sid
  app_ostype       = try(local.application.os.os_type, "LINUX")
  anchor_ostype    = upper(try(local.anchor_vms.os.os_type, "LINUX"))
  db_ostype        = try(local.databases[0].os.os_type, "LINUX")
  db_server_count  = var.database_server_count
  app_server_count = try(local.application.application_server_count, 0)
  web_server_count = try(local.application.webdispatcher_count, 0)
  scs_server_count = local.application.scs_high_availability ? 2 * local.application.scs_server_count : local.application.scs_server_count  app_zones        = local.app_zones
  scs_zones        = local.scs_zones
  web_zones        = local.web_zones
  db_zones         = local.db_zones
  resource_offset  = try(var.options.resource_offset, 0)
  custom_prefix    = var.custom_prefix
}
```

A continuación, debe apuntar los demás archivos del módulo de Terraform al módulo de nomenclatura personalizado. Estos archivos del módulo incluyen:

- `deploy\terraform\run\sap_system\module.tf`
- `deploy\terraform\bootstrap\sap_deployer\module.tf`
- `deploy\terraform\bootstrap\sap_library\module.tf`
- `deploy\terraform\run\sap_library\module.tf`
- `deploy\terraform\run\sap_deployer\module.tf`

Para cada archivo, cambie el origen del módulo `sap_namegenerator` para que apunte a la ubicación del nuevo módulo de nomenclatura. Por ejemplo, `module "sap_namegenerator" { source = "../../terraform-units/modules/sap_namegenerator"` se convierte en `module "sap_namegenerator" { source = "../../../../Contoso_naming"`.

## <a name="change-resource-group-naming-logic"></a>Cambio de la lógica de nomenclatura del grupo de recursos

Para cambiar la lógica de nomenclatura del grupo de recursos, vaya a la carpeta del módulo de nomenclatura personalizado (por ejemplo, `Workspaces\Contoso_naming`). Después, edite el archivo `resourcegroup.tf`. Modifique el código siguiente con su propia lógica de nomenclatura.

```json
locals {

  // Resource group naming
  sdu_name = length(var.codename) > 0 ? (
    upper(format("%s-%s-%s_%s-%s", local.env_verified, local.location_short, local.sap_vnet_verified, var.codename, var.sap_sid))) : (
    upper(format("%s-%s-%s-%s", local.env_verified, local.location_short, local.sap_vnet_verified, var.sap_sid))
  )

  deployer_name  = upper(format("%s-%s-%s", local.deployer_env_verified, local.deployer_location_short, local.dep_vnet_verified))
  landscape_name = upper(format("%s-%s-%s", local.landscape_env_verified, local.location_short, local.sap_vnet_verified))
  library_name   = upper(format("%s-%s", local.library_env_verified, local.location_short))

  // Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only. The name must be unique.
  deployer_storageaccount_name       = substr(replace(lower(format("%s%s%sdiag%s", local.deployer_env_verified, local.deployer_location_short, local.dep_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  landscape_storageaccount_name      = substr(replace(lower(format("%s%s%sdiag%s", local.landscape_env_verified, local.location_short, local.sap_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  library_storageaccount_name        = substr(replace(lower(format("%s%ssaplib%s", local.library_env_verified, local.location_short, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  sdu_storageaccount_name            = substr(replace(lower(format("%s%s%sdiag%s", local.env_verified, local.location_short, local.sap_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  terraformstate_storageaccount_name = substr(replace(lower(format("%s%stfstate%s", local.library_env_verified, local.location_short, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)

}
```

## <a name="change-resource-suffixes"></a>Cambio de sufijos de recursos

Para cambiar los sufijos de recursos, vaya a la carpeta del módulo de nomenclatura personalizado (por ejemplo, `Workspaces\Contoso_naming`). Después, edite el archivo `variables_local.tf`. Modifique la siguiente asignación con sus propios sufijos de recursos.

> [!NOTE]
> Cambie solo los **valores** de la asignación. No cambie la clave de **asignación** que usa el código de Terraform.
> Por ejemplo, si desea cambiar el nombre del componente de la interfaz de red del administrador, cambie `"admin-nic"           = "-admin-nic"` a `"admin-nic"           = "yourNICname"`.

```json
variable resource_suffixes {
  type        = map(string)
  description = "Extension of resource name"

  default = {
    "admin_nic"           = "-admin-nic"
    "admin_subnet"        = "admin-subnet"
    "admin_subnet_nsg"    = "adminSubnet-nsg"
    "app_alb"             = "app-alb"
    "app_avset"           = "app-avset"
    "app_subnet"          = "app-subnet"
    "app_subnet_nsg"      = "appSubnet-nsg"
    "db_alb"              = "db-alb"
    "db_alb_bepool"       = "dbAlb-bePool"
    "db_alb_feip"         = "dbAlb-feip"
    "db_alb_hp"           = "dbAlb-hp"
    "db_alb_rule"         = "dbAlb-rule_"
    "db_avset"            = "db-avset"
    "db_nic"              = "-db-nic"
    "db_subnet"           = "db-subnet"
    "db_subnet_nsg"       = "dbSubnet-nsg"
    "deployer_rg"         = "-INFRASTRUCTURE"
    "deployer_state"      = "_DEPLOYER.terraform.tfstate"
    "deployer_subnet"     = "_deployment-subnet"
    "deployer_subnet_nsg" = "_deployment-nsg"
    "iscsi_subnet"        = "iscsi-subnet"
    "iscsi_subnet_nsg"    = "iscsiSubnet-nsg"
    "library_rg"          = "-SAP_LIBRARY"
    "library_state"       = "_SAP-LIBRARY.terraform.tfstate"
    "kv"                  = ""
    "msi"                 = "-msi"
    "nic"                 = "-nic"
    "osdisk"              = "-OsDisk"
    "pip"                 = "-pip"
    "ppg"                 = "-ppg"
    "sapbits"             = "sapbits"
    "storage_nic"         = "-storage-nic"
    "storage_subnet"      = "_storage-subnet"
    "storage_subnet_nsg"  = "_storageSubnet-nsg"
    "scs_alb"             = "scs-alb"
    "scs_alb_bepool"      = "scsAlb-bePool"
    "scs_alb_feip"        = "scsAlb-feip"
    "scs_alb_hp"          = "scsAlb-hp"
    "scs_alb_rule"        = "scsAlb-rule_"
    "scs_avset"           = "scs-avset"
    "scs_ers_feip"        = "scsErs-feip"
    "scs_ers_hp"          = "scsErs-hp"
    "scs_ers_rule"        = "scsErs-rule_"
    "scs_scs_rule"        = "scsScs-rule_"
    "sdu_rg"              = ""
    "tfstate"             = "tfstate"
    "vm"                  = ""
    "vnet"                = "-vnet"
    "vnet_rg"             = "-INFRASTRUCTURE"
    "web_alb"             = "web-alb"
    "web_alb_bepool"      = "webAlb-bePool"
    "web_alb_feip"        = "webAlb-feip"
    "web_alb_hp"          = "webAlb-hp"
    "web_alb_inrule"      = "webAlb-inRule"
    "web_avset"           = "web-avset"
    "web_subnet"          = "web-subnet"
    "web_subnet_nsg"      = "webSubnet-nsg"

  }
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre convenciones de nomenclatura](automation-naming.md)
