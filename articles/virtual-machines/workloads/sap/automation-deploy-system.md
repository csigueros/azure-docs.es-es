---
title: Acerca de la implementación del sistema SAP para el marco de automatización
description: Información general sobre el proceso de implementación del sistema SAP en el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: a1f79dcd7f68f426a9c9d515d96048c7e7bc0123
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725351"
---
# <a name="sap-system-deployment-for-the-automation-framework"></a>Implementación del sistema SAP para el marco de automatización

La creación del [sistema SAP](automation-deployment-framework.md#sap-concepts) forma parte del proceso del [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md). El sistema SAP crea las máquinas virtuales y los componentes auxiliares de la [aplicación SAP](automation-deployment-framework.md#sap-concepts). 

El sistema SAP implementa:

- El [nivel de base de datos](#database-tier), que implementa máquinas virtuales de base de datos, sus discos y una instancia de Azure Standard Load Balancer. En este nivel puede ejecutar [bases de datos de HANA](automation-configure-extra-disks.md#hana-databases) o [bases de datos de AnyDB](automation-configure-extra-disks.md#anydb-databases).
- El [nivel de servicios centrales de SAP](#central-services-tier), que implementa un número de máquinas virtuales definido por el cliente y una instancia de Azure Standard Load Balancer.
- La [capa de aplicación](#application-tier), que implementa las máquinas virtuales y sus discos.
- El [nivel de Web Dispatcher](#web-dispatcher-tier).

## <a name="application-tier"></a>Nivel de aplicación

La capa de aplicación implementa un número de máquinas virtuales definido por el cliente. Estas máquinas virtuales son del tamaño **Standard_D4s_v3** y tienen un disco de sistema operativo (SO) de 30 GB y un disco de datos de 512 GB.

Para establecer el número de servidores de aplicaciones, defina el parámetro `application_server_count` para este nivel en el archivo de parámetros. Por ejemplo, `application_server_count= 3`.


## <a name="central-services-tier"></a>Nivel de servicios centrales

El nivel de servicios centrales de SAP (SCS) implementa un número de máquinas virtuales definido por el cliente. Estas máquinas virtuales son del tamaño **Standard_D4s_v3** y tienen un disco de sistema operativo de 30 GB y un disco de datos de 512 GB. Este nivel también implementa una instancia de [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md).

Para establecer el número de servidores de SCS, defina el parámetro `scs_server_count` para este nivel en el archivo de parámetros. Por ejemplo, `scs_server_count=1`.


## <a name="web-dispatcher-tier"></a>El nivel de Web Dispatcher

El nivel de Web Dispatcher implementa un número de máquinas virtuales definido por el cliente.  Este nivel también implementa una instancia de [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md).

Para establecer el número de servidores web, defina el parámetro `web_server_count` para este nivel en el archivo de parámetros. Por ejemplo, `web_server_count = 2`.

## <a name="database-tier"></a>Nivel de base de datos

El nivel de base de datos implementa las máquinas virtuales de base de datos y sus discos, y una instancia de [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md). En este nivel se pueden usar [bases de datos de HANA](automation-configure-extra-disks.md#hana-databases) o [bases de datos de AnyDB](automation-configure-extra-disks.md#anydb-databases).

Puede establecer el tamaño de las máquinas virtuales de base de datos con el parámetro `size` para este nivel. Por ejemplo, `"size": "S4Demo"` para bases de datos de HANA o `"size": "1 TB"` para bases de datos de AnyDB. Consulte el parámetro **Size** en las tablas de [opciones de máquina virtual de base de datos de HANA](automation-configure-extra-disks.md#hana-databases) y [opciones de máquina virtual de base de datos de AnyDB](automation-configure-extra-disks.md#anydb-databases) para ver los valores posibles.

De forma predeterminada, el marco de automatización implementa la configuración de disco correcta para las implementaciones de base de datos de HANA. Para las implementaciones de base de datos de HANA, el marco calcula la configuración de disco predeterminada en función del tamaño de la máquina virtual. Sin embargo, en el caso de las implementaciones de base de datos de HANA, el marco calcula la configuración de disco predeterminada en función del tamaño de la base de datos. Puede establecer el tamaño de disco necesario mediante la creación de un archivo JSON personalizado en la implementación. Para ver un ejemplo, [consulte el siguiente ejemplo de código JSON y reemplace los valores según sea necesario para la configuración](automation-configure-extra-disks.md#custom-sizing-file). Luego, defina el parámetro `db_disk_sizes_filename` en el archivo de parámetros para el nivel de base de datos. Por ejemplo, `db_disk_sizes_filename = "path/to/JSON/file"`.

También puede [agregar discos adicionales a un nuevo sistema](automation-configure-extra-disks.md#custom-sizing-file) o [agregar discos adicionales a un sistema existente](automation-configure-extra-disks.md#add-extra-disks-to-existing-system).

## <a name="core-configuration"></a>Configuración básica

En el siguiente archivo de parámetros de ejemplo solo se muestran los parámetros necesarios.

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="DEV"

# The location value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# The network logical name is mandatory - it is used in the naming convention and should map to the workload virtual network logical name 
network_name="SAP01"

# sid is a mandatory field that defines the SAP Application SID
sid="RH7"

app_tier_vm_sizing="Production"
app_tier_use_DHCP=true

database_platform="HANA"

database_size="S4Demo"
database_sid="XDB"

database_vm_use_DHCP=true

database_vm_image={
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="latest"
}

# application_server_count defines how many application servers to deploy
application_server_count=2

application_server_image= {
  os_type=""
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="latest"
}

scs_server_count=1

# scs_instance_number
scs_instance_number="01"

# ers_instance_number
ers_instance_number="02"

# webdispatcher_server_count defines how many web dispatchers to deploy
webdispatcher_server_count=0


```

## <a name="deploying-the-sap-system"></a>Implementación del sistema SAP
   
El archivo de configuración del sistema SAP de ejemplo `DEV-WEEU-SAP01-X01.tfvars` se encuentra en carpeta `~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X01`.

Al ejecutar el siguiente comando se implementará el sistema SAP.

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> Realice esta tarea desde el implementador.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X01

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh          \
        --parameterfile DEV-WEEU-SAP01-X01.tfvars           \
        --type sap_system
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\DEV-WEEU-SAP01-X01

New-SAPSystem -Parameterfile DEV-WEEU-SAP01-X01.tfvars 
-Type sap_system
```

---


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Acerca de la implementación de la zona de cargas de trabajo con el marco de automatización](automation-software.md)
