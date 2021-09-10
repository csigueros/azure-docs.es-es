---
title: Archivo de inclusión
description: Archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/22/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 528b3243a7c7d8ca1f96d9b4a311393820e19685
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738995"
---
- No admite Ultra Disks.
- No se puede habilitar si Azure Disk Encryption (cifrado de máquina virtual de invitado con BitLocker/DM-Crypt) está habilitado en las máquinas virtuales o en los conjuntos de escalado de máquinas virtuales.
- No se puede habilitar Azure Disk Encryption en discos que tienen habilitado el cifrado en el host.
- El cifrado se puede habilitar en el conjunto de escalado de máquinas virtuales existente. Sin embargo, solo se cifrarán automáticamente las nuevas máquinas virtuales creadas después de habilitar el cifrado.
- Las máquinas virtuales existentes se deben desasignar y reasignar para su cifrado.
- Admite discos del sistema operativo efímeros, pero solo con claves administradas por la plataforma.
