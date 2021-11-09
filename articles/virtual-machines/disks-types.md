---
title: 'Selección de un tipo de disco para máquinas virtuales IaaS de Azure: discos administrados'
description: Obtenga información sobre los tipos de disco de Azure disponibles para las máquinas virtuales, incluidos los discos ultra, SSD Premium, SSD estándar y HDD estándar.
author: roygara
ms.author: rogarana
ms.date: 11/03/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5e4eb581f8cf9b95e9a8ba4dffd442efc6c055ef
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505448"
---
# <a name="azure-managed-disk-types"></a>Tipos de discos administrados de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Actualmente, Azure Managed Disks ofrece cuatro tipos de disco, cada uno diseñado para abordar un escenario de cliente específico:

- Discos Ultra
- Discos SSD prémium (unidades de estado sólido)
- Discos SSD estándar
- Discos HDD estándar (unidades de disco duro)

## <a name="disk-type-comparison"></a>Comparación de tipos de disco

En la tabla siguiente se proporciona una comparación de los cuatro tipos de disco para ayudarle a decidir cuál usar.

|         | Disco Ultra | SSD Premium | SSD estándar | <nobr>HDD estándar</nobr> |
| ------- | ---------- | ----------- | ------------ | ------------ |
| **Tipo de disco** | SSD | SSD | SSD | HDD |
| **Escenario**  | Cargas de trabajo de uso intensivo de E/S, como [SAP HANA](workloads/sap/hana-vm-operations-storage.md), bases de datos de nivel superior (por ejemplo, SQL y Oracle) y otras cargas de trabajo con muchas transacciones. | Cargas de trabajo confidenciales de producción y rendimiento | Servidores web, aplicaciones empresariales poco utilizadas y desarrollo y pruebas | Copia de seguridad, no crítico, acceso poco frecuente |
| **Tamaño máximo del disco** | 65 536 gibibyte (GiB) | 32 767 GiB | 32 767 GiB | 32 767 GiB |
| **Rendimiento máx.** | 4000 MB/s | 900 MB/s | 750 MB/s | 500 MB/s |
| **IOPS máx.** | 160 000 | 20 000 | 6,000 | 2\.000 |

## <a name="ultra-disks"></a>Discos Ultra

Los discos Ultra de Azure son la opción de almacenamiento con mayor rendimiento para máquinas virtuales (VM) de Azure. Puede cambiar los parámetros de rendimiento de un disco Ultra sin tener que reiniciar las máquinas virtuales. Además, los discos Ultra son adecuados para cargas de trabajo con grandes cantidades de datos, como SAP HANA, bases de datos de nivel superior y cargas de trabajo que admitan muchas transacciones.

Los discos Ultra deben usarse como discos de datos y solo se pueden crear como discos vacíos. Microsoft recomienda usar unidades de estado sólido (SSD) prémium como discos de sistema operativo.

### <a name="ultra-disk-size"></a>Tamaño de discos Ultra

De manera predeterminada, los Discos Ultra de Azure ofrecen hasta 32 TiB por región y suscripción, pero los Discos Ultra admiten una mayor capacidad por solicitud. Para solicitar un aumento de la capacidad, solicite un aumento de la cuota o póngase en contacto con el Soporte técnico de Azure.

En la tabla siguiente se proporciona una comparación del tamaño y la capacidad de rendimiento de los discos para ayudarle a decidir cuál usar.

|Tamaño de disco (GiB)  |Capacidad de IOPS  |Capacidad de rendimiento (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9 600         |2,400         |
|64     |19 200         |4\.000         |
|128     |38 400         |4\.000         |
|256     |76 800         |4\.000         |
|512     |153.600         |4\.000         |
|1024 - 65 536 (los tamaños de este intervalo aumentan en incrementos de 1 TiB)     |160 000         |4\.000         |

Los discos Ultra están diseñados para proporcionar latencias de submilisegundos y las IOPS y el rendimiento que se describen en la tabla anterior el 99,99 % del tiempo.

### <a name="ultra-disk-performance"></a>Rendimiento de los discos Ultra

Los discos Ultra cuentan con un modelo de configuración de rendimiento flexible que le permite configurar de forma independiente las IOPS y el rendimiento antes y después de aprovisionar el disco. Los discos Ultra vienen en varios tamaños fijos, que van desde los 4 GiB hasta los 64 TiB.

### <a name="ultra-disk-iops"></a>IOPS de los discos Ultra

Los discos Ultra admiten límites de IOPS de 300 IOPS/GiB, hasta un máximo de 160 000 IOPS por disco. Para conseguir las IOPS objetivo en el disco, asegúrese de que la cantidad de IOPS de disco seleccionada sea menor que el límite de IOPS de la máquina virtual.

El límite máximo actual de IOPS para una sola máquina virtual en tamaños disponibles con carácter general es de 80 000. Los discos Ultra con mayor IOPS se pueden usar como discos compartidos para admitir varias máquinas virtuales.

El valor mínimo garantizado de IOPS por disco es 1 IOPS/GiB, con una línea de base general mínima de 100 IOPS. Por ejemplo, si aprovisionó un disco Ultra de 4 GiB, el valor mínimo de IOPS para ese disco es 100, en lugar de ocho.

Para más información sobre IOPS, consulte [Rendimiento de la máquina virtual y del disco](disks-performance.md).

### <a name="ultra-disk-throughput"></a>Rendimiento de discos Ultra

El límite de rendimiento de un solo disco Ultra es de 256 KiB/s por cada IOPS aprovisionada, hasta 4000 MBps como máximo por disco (donde MBps = 10^6 bytes por segundo). El rendimiento mínimo garantizado por disco es 4 KiB/s por cada IOPS aprovisionada, con una base de referencia total como mínima de 1 MBps.

Puede ajustar el rendimiento y las IOPS de los discos Ultra en tiempo de ejecución sin desasociar el disco de la máquina virtual. Después de enviar una operación de cambio de tamaño del rendimiento de un disco, este cambio puede tardar hasta una hora en surtir efecto. Se permiten hasta cuatro operaciones de cambio de tamaño de rendimiento durante un período de 24 horas.

Se puede producir un error en la operación de cambio de tamaño del rendimiento debido a la falta de capacidad de ancho de banda de rendimiento.

### <a name="ultra-disk-limitations"></a>Limitaciones de discos Ultra

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

Si quiere empezar a usar discos Ultra, consulte el artículo sobre el [uso de discos Ultra de Azure](disks-enable-ultra-ssd.md).

## <a name="premium-ssds"></a>SSD Premium

Los discos SSD Premium de Azure ofrecen soporte de disco de alto rendimiento y latencia baja para máquinas virtuales (VM) con cargas de trabajo con uso intensivo de entrada/salida (E/S). Para aprovechar la ventaja de la velocidad y el rendimiento de los discos SSD prémium, puede migrar los discos de VM existentes a este tipo. Los discos SSD prémium son adecuados para aplicaciones de producción críticas, pero solo se pueden usar con series de máquinas virtuales compatibles.

Para más información sobre los tipos y tamaños de máquinas virtuales individuales de Azure para Windows o Linux, incluida la compatibilidad de tamaños para el almacenamiento prémium, consulte [Tamaños de las máquinas virtuales Linux en Azure](sizes.md). Debe comprobar cada artículo de tamaño de VM individual para determinar si es compatible con el almacenamiento prémium.

### <a name="premium-ssd-size"></a>Tamaño de discos SSD prémium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

La capacidad, las IOPS y el rendimiento se garantizan cuando se aprovisiona un disco de almacenamiento prémium. Por ejemplo, si crea un disco P50, Azure aprovisiona una capacidad de almacenamiento de 4095 GB, 7500 E/S por segundo y un rendimiento de 250 MB/s para él. La aplicación puede usar toda la capacidad y el rendimiento o parte de ellos. Los discos SSD prémium están diseñados para proporcionar latencias inferiores a 10 milisegundos y los valores de IOPS y rendimiento que se describen en la tabla anterior el 99,9 % del tiempo.

### <a name="premium-ssd-bursting"></a>Seguridad de SSD Premium

Los discos SSD prémium ofrecen expansión de disco, lo que proporciona una mejor tolerancia a los cambios imprevisibles de los patrones de E/S. La expansión de disco es especialmente útil durante el arranque del disco del sistema operativo y para las aplicaciones con tráfico de picos. Para más información sobre el funcionamiento de la expansión de disco de discos de Azure, consulte [Expansión a nivel de disco](disk-bursting.md#disk-level-bursting).

### <a name="premium-ssd-transactions"></a>Transacciones de discos SSD prémium

Para los discos SSD Premium, cada operación de E/S menor o igual a 256 KiB de rendimiento se considera una sola operación de E/S. Las operaciones de E/S mayores de 256 KiB de rendimiento se consideran varias operaciones de E/S con un tamaño de 256 KiB.

## <a name="standard-ssds"></a>Discos SSD estándar

Los discos SSD estándar están optimizados para cargas de trabajo que necesitan un rendimiento coherente a niveles inferiores de IOPS. Son una opción especialmente buena para los clientes con cargas de trabajo variadas compatibles con las soluciones de unidad de disco duro (HDD) locales. En comparación con los discos HDD estándar, los discos SSD estándar ofrecen mayor disponibilidad, coherencia, confiabilidad y latencia. Los discos SSD estándar son adecuados para servidores web, servidores de aplicaciones con IOPS bajas, aplicaciones empresariales ligeramente usadas y cargas de trabajo que no son de producción. Al igual que los discos HDD estándar, los SSD están disponibles en todas las máquinas virtuales de Azure.

### <a name="standard-ssd-size"></a>Tamaño de discos SSD estándar

[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Los discos SSD estándar están diseñados para proporcionar latencias de menos de 10 milisegundos y un nivel de IOPS y de rendimiento hasta los límites descritos en la tabla anterior durante el 99 % del tiempo. Las IOPS y el rendimiento reales pueden variar a veces, según los patrones de tráfico. Los discos SSD estándar proporcionarán un rendimiento más coherente que los discos HDD, con una latencia menor.

### <a name="standard-ssd-transactions"></a>Transacciones de discos SSD estándar

Para los discos SSD estándar, cada operación de E/S menor o igual a 256 KiB de rendimiento se considera una sola operación de E/S. Las operaciones de E/S mayores de 256 KiB de rendimiento se consideran varias operaciones de E/S con un tamaño de 256 KiB. Estas transacciones generan costos facturables.

### <a name="standard-ssd-bursting"></a>Expansión de discos SSD estándar

Los discos SSD estándar ofrecen expansión de disco, lo que proporciona una mejor tolerancia a los cambios imprevisibles de los patrones de E/S. Tanto los discos de arranque del sistema operativo como las aplicaciones propensas a picos de tráfico se beneficiarán de la expansión de disco. Para más información sobre el funcionamiento de la expansión de disco de discos de Azure, consulte [Expansión a nivel de disco](disk-bursting.md#disk-level-bursting).

## <a name="standard-hdds"></a>Discos HDD estándar

Los discos HDD estándar de Azure ofrecen compatibilidad de discos confiable y de bajo costo para las máquinas virtuales que ejecutan cargas de trabajo tolerantes a la latencia. Con el almacenamiento estándar, los datos se almacenan en unidades HDD y el rendimiento puede variar más que el de los discos basados en SSD. Los discos HDD estándar están diseñados para proporcionar latencias de escritura de menos de 10 ms y latencias de lectura de menos de 20 ms en la mayoría de las operaciones de E/S. Sin embargo, el rendimiento real puede variar según el tamaño de E/S y el patrón de carga de trabajo. Cuando se trabaja con máquinas virtuales, se pueden usar discos HDD estándar para escenarios de desarrollo/pruebas y para cargas de trabajo menos críticas. Los discos HDD estándar están disponibles en todas las regiones de Azure y se pueden utilizar con todas las máquinas virtuales de Azure.

### <a name="standard-hdd-size"></a>Tamaño de discos HDD estándar
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="standard-hdd-transactions"></a>Transacciones de discos HDD estándar

En el caso de los discos HDD estándar, cada operación de E/S se considera como una única transacción, independientemente del tamaño de esta. Estas transacciones tienen un impacto en la facturación.

## <a name="billing"></a>Facturación

Al usar Managed Disks, se aplican las siguientes consideraciones de facturación:

- Tipo de disco
- Tamaño del disco administrado
- Instantáneas
- Transferencias de datos de salida
- Número de transacciones

**Tamaño del disco administrado**: los discos administrados se facturan según el tamaño aprovisionado. Azure asigna el tamaño aprovisionado (redondeado al alza) a la oferta de tamaño de disco más cercana. Para obtener detalles sobre los tamaños de disco ofrecidos, consulte las tablas anteriores. Cada disco se asigna a una oferta de tamaño de disco aprovisionado compatible y se factura en consecuencia. Por ejemplo, si ha aprovisionado un disco SSD estándar de 200 GiB, se asigna a la oferta de tamaño de disco E15 (256 GiB). La facturación de cualquier disco aprovisionado se prorratea cada hora con el precio mensual de la oferta de almacenamiento. Por ejemplo, supongamos que aprovisiona un disco E10 y lo elimina después de 20 horas de uso. En este caso, se le facturará por la oferta E10 prorrateada a 20 horas, independientemente de la cantidad de datos escritos en el disco.

**Instantáneas**: Las instantáneas se facturan según el tamaño utilizado. Por ejemplo, supongamos que crea una instantánea de un disco administrado con capacidad aprovisionada de 64 GiB y el tamaño de datos usado real es de 10 GiB. En este caso, solo se le facturará por el tamaño de datos usado de 10 GiB.

Para más información acerca de las instantáneas, consulte la sección sobre las instantáneas en la [introducción a los discos administrados](managed-disks-overview.md#managed-disk-snapshots).

**Transferencias de datos de salida**: las [transferencias de datos de salida](https://azure.microsoft.com/pricing/details/bandwidth/) (datos que salen de los centros de datos de Azure) se facturan en función del uso de ancho de banda.

**Transacciones**: se le factura por el número de transacciones que realiza en un disco administrado estándar. Para los discos SSD estándar, cada operación de E/S menor o igual a 256 KiB de rendimiento se considera una sola operación de E/S. Las operaciones de E/S mayores de 256 KiB de rendimiento se consideran varias operaciones de E/S con un tamaño de 256 KiB. En el caso de los discos HDD estándar, cada operación de E/S se considera como una única transacción, independientemente del tamaño de esta.

Para obtener información detallada sobre los precios de los discos administrados, incluidos los costos de las transacciones, consulte [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disks-vm-reservation-fee"></a>Precio de reserva de máquina virtual para discos Ultra

Las máquinas virtuales de Azure tienen la funcionalidad para indicar si son compatibles con discos Ultra. Una máquina virtual compatible con un disco Ultra asigna capacidad dedicada de ancho de banda entre la instancia de la máquina virtual de proceso y la unidad de escalado de almacenamiento en bloque, para así poder optimizar el rendimiento y reducir la latencia. Cuando agrega esta funcionalidad en la máquina virtual, se produce un cargo por reserva. Este cargo por reserva solo se aplica si habilita la funcionalidad de disco Ultra en la máquina virtual sin un disco Ultra asociado. Cuando se asocia un disco Ultra a la máquina virtual compatible con estos discos, este cargo por reserva no se aplica. Este cargo se calcula según la vCPU aprovisionada en la máquina virtual.

> [!Note]
> En el caso de los [tamaños principales de máquina virtual restringidos](constrained-vcpu.md), la tarifa de la reserva se basa en el número real de vCPU y no en los núcleos restringidos. En el caso de Standard_E32-8s_v3, la tarifa de la reserva se basará en 32 núcleos.

Para información sobre los precios de los discos Ultra, consulte la [página de precios de discos de Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="azure-disk-reservation"></a>Reserva de discos de Azure

La reserva de discos proporciona un descuento en la compra anticipada de un año de almacenamiento en disco, lo que reduce el costo total. Al comprar una reserva de discos, se selecciona una SKU de disco específica en una región de destino. Por ejemplo, puede elegir cinco discos SSD prémium P30 (1 TiB) en la región Centro de EE. UU. durante un período de un año. La experiencia de reserva de discos es similar a las instancias reservadas de máquina virtual de Azure. Puede agrupar las reservas de discos y máquinas virtuales para maximizar el ahorro. Por ahora, la reserva de discos de Azure ofrece un plan de compromiso de un año para las SKU de discos SSD prémium de P30 (1 TiB) a P80 (32 TiB) en todas las regiones de producción. Para más información sobre los precios de los discos reservados, consulte la [página de precios de los discos de Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Pasos siguientes

Para comenzar, consulte [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
