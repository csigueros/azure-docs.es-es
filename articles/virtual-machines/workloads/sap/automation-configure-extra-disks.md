---
title: Adición de más discos a la configuración de automatización de la implementación de SAP
description: Configure más discos para el sistema en el marco de automatización de la implementación de SAP en Azure. Agregue discos adicionales a un nuevo sistema o a un sistema existente.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 5b3f900de7c9e03ddf5dda8001ce5932cc7fbaac
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725353"
---
# <a name="change-the-disk-configuration-for-the-sap-deployment-automation"></a>Cambio de la configuración del disco para la automatización de la implementación de SAP

De forma predeterminada, el [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) define la configuración del disco para los sistemas SAP. Cuando sea necesario, puede cambiar la configuración predeterminada, para lo que debe proporcionar un archivo JSON de configuración de disco personalizado.

> [!TIP]
> Cuando sea posible, se recomienda aumentar el tamaño del disco, en lugar de agregar más discos.


### <a name="hana-databases"></a>Bases de datos de HANA

En la tabla siguiente se muestra la configuración de disco predeterminada de los sistemas HANA. 

| Tamaño      | SKU de la máquina virtual              | Disco del sistema operativo       | Discos de datos       | Discos de registro        | Compartido en Hana    | SAP de usuario     | Backup          |
|-----------|---------------------|---------------|------------------|------------------|----------------|--------------|-----------------|
| Valor predeterminado   | Standard_D8s_v3     | E6 (64 GB)    | P20 (512 GB)     | P20 (512 GB)     | E20 (512 GB)   | E6 (64 GB)   | E20 (512 GB)    |  
| S4DEMO    | Standard_E32ds_v4   | P10 (128 GB)  | 4 P10 (128 GB)   | 3 P10 (128 GB)   |                | P20 (512 GB) | P20 (512 GB)    |
| M32ts     | Standard_M32ts      | P6 (64 GB)    | 4 P6 (64 GB)     | 3 P10 (128 GB)   | P20 (512 GB)   | P6 (64 GB)   | P20 (512 GB)    |
| M32ls     | Standard_M32ls      | P6 (64 GB)    | 4 P6 (64 GB)     | 3 P10 (128 GB)   | P20 (512 GB)   | P6 (64 GB)   | P20 (512 GB)    |
| M64ls     | Standard_M64ls      | P6 (64 GB)    | 4 P10 (128 GB)   | 3 P10 (128 GB)   | P20 (512 GB)   | P6 (64 GB)   | P30 (1024 GB)   |
| M64s      | Standard_M64s       | P10 (128 GB)  | 4 P15 (256 GB)   | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | P30 (1024 GB)   |
| M64ms     | Standard_M64ms      | P6 (64 GB)    | 4 P20 (512 GB)   | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 2 P30 (1024 GB) |
| M128s     | Standard_M128s      | P10 (128 GB)  | 4 P20 (512 GB)   | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 2 P30 (1024 GB) |
| M128ms    | Standard_M128m      | P10 (128 GB)  | 4 P30 (1024 GB)  | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 4 P30 (1024 GB) |
| M208s_v2  | Standard_M208s_v2   | P10 (128 GB)  | 4 P30 (1024 GB)  | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 3 P40 (2048 GB) |
| M208ms_v2 | Standard_M208ms_v2  | P10 (128 GB)  | 4 P40 (2048 GB)  | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 3 P40 (2048 GB) |
| M416s_v2  | Standard_M416s_v2   | P10 (128 GB)  | 4 P40 (2048 GB)  | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 3 P40 (2048 GB) |
| M416ms_v2 | Standard_M416m_v2   | P10 (128 GB)  | 4 P50 (4096 GB)  | 3 P15 (256 GB)   | P30 (1024 GB)  | P6 (64 GB)   | 4 P50 (4096 GB) |

### <a name="anydb-databases"></a>Bases de datos de AnyDB

En la tabla siguiente se muestra la configuración de disco predeterminada de los sistemas AnyDB. 

| Size    | SKU de la máquina virtual           | Disco del sistema operativo     | Discos de datos.       | Discos de registro       |
|---------|------------------|-------------|------------------|-----------------|
| Valor predeterminado | Standard_E4s_v3  | P6 (64 GB)  | 256 P15 (256 GB)     | P10 (128 GB)    |
| 200 GB  | Standard_E4s_v3  | P6 (64 GB)  | 256 P15 (256 GB)     | P10 (128 GB)    |
| 500 GB  | Standard_E8s_v3  | P6 (64 GB)  | P20 (512 GB)     | 256 P15 (256 GB)    |
| 1 TB  | Standard_E16s_v3 | P10 (128 GB) | 2 P20 (512 GB)   | 2 P15 (256 GB)  |
| 2 TB  | Standard_E32s_v3 | P10 (128 GB) | 2 P30 (1024 GB)  | 2 P20 (512 GB)  |
| 5 TB  | Standard_M64ls   | P10 (128 GB) | 5 P30 (1024 GB)  | 2 P20 (512 GB)  |
| 10 TB  | Standard_M64s    | P10 (128 GB) | 5 P40 (2048 GB)  | 2 P20 (512 GB)  |
| 15 TB  | Standard_M64s    | P10 (128 GB) | 4 P50 (4096 GB)  | 2 P20 (512 GB)  |
| 20 TB  | Standard_M64s    | P10 (128 GB) | 5 P50 (4096 GB)  | 2 P20 (512 GB)  |
| 30 TB  | Standard_M128s   | P10 (128 GB) | 8 P50 (4096 GB)  | 2 P40 (2048 GB) |
| 40 TB  | Standard_M128s   | P10 (128 GB) | 10 P50 (4096 GB) | 2 P40 (2048 GB) |
| 50 TB  | Standard_M128s   | P10 (128 GB) | 13 P50 (4096 GB) | 2 P40 (2048 GB) |


## <a name="custom-sizing-file"></a>Archivo de dimensionamiento personalizado

El dimensionamiento de discos para un sistema SAP se puede definir mediante un archivo de dimensionamiento personalizado. 

Cree un archivo con la estructura que se muestra a continuación y guárdelo en la misma carpeta que el archivo de parámetros del sistema, por ejemplo, "XO1_db_sizes.json".  Luego, defina el parámetro `db_disk_sizes_filename` en el archivo de parámetros para el nivel de base de datos. Por ejemplo, `db_disk_sizes_filename = "XO1_db_sizes.json"`.

El siguiente código de ejemplo es una configuración de ejemplo para el nivel de base de datos. Define tres discos de datos (LUN 0, 1 y 2), un disco de registro (LUN 9, con la SKU Ultra) y un disco de copia de seguridad (LUN 13, mediante la SKU SSDN estándar).

```json
{
  "db" : {
    "Default": {
      "compute": {
        "vm_size"                 : "Standard_D4s_v3",
        "swap_size_gb"            : 2
      },
      "storage": [
        {
          "name"                  : "os",
          "count"                 : 1,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 128,
          "caching"               : "ReadWrite"
        },
        {
          "name"                  : "data",
          "count"                 : 3,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 0
        },
        {
          "name"                  : "log",
          "count"                 : 1,
          "disk_type"             : "UltraSSD_LRS",
          "size_gb": 512,
          "disk-iops-read-write"  : 2048,
          "disk-mbps-read-write"  : 8,
          "caching"               : "None",
          "write_accelerator"     : false,
          "start_lun"             : 9
        },
        {
          "name"                  : "backup",
          "count"                 : 1,
          "disk_type"             : "StandardSSD_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 13
        }

      ]
    }
  }
}
```

## <a name="add-extra-disks-to-existing-system"></a>Adición de discos adicionales a un sistema existente

Si necesita incorporar discos a un sistema que ya está implementado, puede agregar un nuevo bloque a la estructura de JSON. Incluya el atributo `append` en este bloque y establezca el valor en `true`. Por ejemplo, en el código de ejemplo siguiente, el último bloque contiene el atributo `"append" : true,`. El último bloque agrega un nuevo disco al nivel de base de datos, que ya está configurado en el primer bloque `"data"` del código.

```json
{
  "db" : {
    "Default": {
      "compute": {
        "vm_size"                 : "Standard_D4s_v3",
        "swap_size_gb"            : 2
      },
      "storage": [
        {
          "name"                  : "os",
          "count"                 : 1,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 128,
          "caching"               : "ReadWrite"
        },
        {
          "name"                  : "data",
          "count"                 : 3,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 0
        },
        {
          "name"                  : "log",
          "count"                 : 1,
          "disk_type"             : "UltraSSD_LRS",
          "size_gb": 512,
          "disk-iops-read-write"  : 2048,
          "disk-mbps-read-write"  : 8,
          "caching"               : "None",
          "write_accelerator"     : false,
          "start_lun"             : 9
        },
        {
          "name"                  : "backup",
          "count"                 : 1,
          "disk_type"             : "StandardSSD_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "start_lun"             : 13
        }
        ,
        {
          "name"                  : "data",
          "count"                 : 1,
          "disk_type"             : "Premium_LRS",
          "size_gb"               : 256,
          "caching"               : "ReadWrite",
          "write_accelerator"     : false,
          "append"                : true,
          "start_lun"             : 4
        }

      ]
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de un módulo de nomenclatura personalizado](automation-naming-module.md)

