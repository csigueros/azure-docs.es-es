---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d5915d8628254f24343571c1adc254c548558415
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077715"
---
- Esta característica es compatible actualmente solo con SSD Premium.
- Debe desasignar la máquina virtual o desconectar el disco de una máquina virtual en ejecución antes de que pueda cambiar el nivel del disco.
- Los niveles de rendimiento P60, P70 y P80 solo pueden usarlos aquellos discos cuyo tamaño supere los 4096 GiB.
- El nivel de rendimiento de un disco solo se puede cambiar a un nivel inferior una vez cada 12 horas.

## <a name="change-performance-tier-without-downtime-preview"></a>Cambio del nivel de rendimiento sin que se produzca tiempo de inactividad (versión preliminar)

Normalmente, habría que desasignar la máquina virtual o desconectar el disco para cambiar el nivel de rendimiento. Pero si habilita esta característica de la versión preliminar, no hay que desasignar la máquina virtual ni desconectar el disco para cambiar el nivel.

La versión preliminar tiene las limitaciones siguientes:
- Solo está disponible en la región Centro-oeste de EE. UU.
- Actualmente no está disponible para discos compartidos.
- Debe usar uno de los métodos siguientes para cambiar el nivel sin tiempo de inactividad:
    - Plantillas de Azure Resource Manager con la API `2020-12-01` para cambiar los niveles de rendimiento sin que se produzca tiempo de inactividad.
    - Acceso a Azure Portal a través del vínculo siguiente: [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
    - La CLI de Azure más reciente.
- Actualmente no está disponible con el módulo de Azure PowerShell.