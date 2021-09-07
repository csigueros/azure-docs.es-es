---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/16/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a2009a1898abd8390b4a747efb10db01964f55a7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252581"
---
Los discos compartidos solo se pueden habilitar para un subconjunto de tipos de disco. Actualmente, solo los discos Ultra, los discos SSD prémium y los discos SSD estándar pueden permitir discos compartidos. Cada disco administrado que tiene discos compartidos habilitados está sujeto a las siguientes limitaciones, organizadas según el tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Los discos Ultra Disks tienen su propia lista independiente de limitaciones, que no están relacionadas con los discos compartidos. Para conocer las limitaciones de Ultra Disks, vea [Uso de Azure Ultra Disks](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Al compartir discos Ultra Disks, estos tienen las siguientes limitaciones adicionales:

- Solo se pueden usar discos básicos con algunas versiones del clúster de conmutación por error de Windows Server. Para más información, consulte [Requisitos de hardware y opciones de almacenamiento de clústeres de conmutación por error](/windows-server/failover-clustering/clustering-requirements).
- Solo se admite el [cifrado del lado servidor](../articles/virtual-machines/disk-encryption.md); [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) no se admite actualmente.

Los discos Ultra Disks compartidos están disponibles en todas las regiones que admiten este tipo de disco de forma predeterminada y no requieren que se registren para poder usarlos.

### <a name="premium-ssds"></a>SSD Premium

- Solo se puede habilitar en discos de datos, no en discos de sistema operativo.
- El almacenamiento en caché del host de **solo lectura** no está disponible para los discos SSD premium con `maxShares>1`.
- La expansión de disco no está disponible para los discos SSD premium con `maxShares>1`.
- Al usar conjuntos de disponibilidad y conjuntos de escalado de máquinas virtuales con discos compartidos de Azure, la [alineación del dominio de error del almacenamiento](../articles/virtual-machines/availability.md) con el dominio de error de la máquina virtual no se exige para el disco de datos compartido.
- Al usar [grupos con ubicación por proximidad (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), todas las máquinas virtuales que comparten un disco deben ser parte del mismo grupo con ubicación por proximidad.
- Solo se pueden usar discos básicos con algunas versiones del clúster de conmutación por error de Windows Server. Para más información, consulte [Requisitos de hardware y opciones de almacenamiento de clústeres de conmutación por error](/windows-server/failover-clustering/clustering-requirements).
- La compatibilidad con Azure Site Recovery todavía no está disponible.
- Azure Backup está disponible a través de [Azure Disk Backup](../articles/backup/disk-backup-overview.md).
- Solo se admite el [cifrado del lado servidor](../articles/virtual-machines/disk-encryption.md); [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) no se admite actualmente.

#### <a name="premium-ssd-regional-availability"></a>Disponibilidad regional de los discos SSD prémium

En todas las regiones en las que hay disponibles discos administrados, también hay disponibles discos compartidos de todos los tamaños de SDD prémium.


### <a name="standard-ssds"></a>Discos SSD estándar

- Solo se puede habilitar en discos de datos, no en discos de sistema operativo.
- Al usar conjuntos de disponibilidad y conjuntos de escalado de máquinas virtuales con discos compartidos de Azure, la [alineación del dominio de error del almacenamiento](../articles/virtual-machines/availability.md) con el dominio de error de la máquina virtual no se exige para el disco de datos compartido.
- Al usar [grupos con ubicación por proximidad (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), todas las máquinas virtuales que comparten un disco deben ser parte del mismo grupo con ubicación por proximidad.
- Solo se pueden usar discos básicos con algunas versiones del clúster de conmutación por error de Windows Server. Para más información, consulte [Requisitos de hardware y opciones de almacenamiento de clústeres de conmutación por error](/windows-server/failover-clustering/clustering-requirements).
- La compatibilidad con Azure Site Recovery todavía no está disponible.
- Azure Backup está disponible a través de [Azure Disk Backup](../articles/backup/disk-backup-overview.md).
- Solo se admite el [cifrado del lado servidor](../articles/virtual-machines/disk-encryption.md); [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) no se admite actualmente.

#### <a name="standard-ssd-regional-availability"></a>Disponibilidad regional de los discos SSD estándar

En todas las regiones en las que hay disponibles discos administrados, también hay disponibles discos compartidos de todos los tamaños de SDD estándar.
