---
title: Residencia de datos
description: Residencia de datos e información sobre los servidores habilitados para Azure Arc.
ms.topic: reference
ms.date: 08/05/2021
ms.custom: references_regions
ms.openlocfilehash: 36aa97dc55879e61caf2b7c19aac21cae4091433
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832183"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servidores habilitados para Azure Arc: residencia de datos

En este artículo se explica el concepto de residencia de datos y cómo se aplica a los servidores habilitados para Azure Arc.

Los servidores habilitados para Azure Arc están **[disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** en **Estados Unidos, Europa, Reino Unido, Australia y Asia Pacífico**.

## <a name="data-residency"></a>Residencia de datos

Los servidores habilitados para Azure Arc almacenan los valores de configuración de la [extensión de VM de Azure](manage-vm-extensions.md) (es decir, los valores de propiedad). Es necesario especificar la extensión antes de intentar habilitarla en la máquina conectada. Por ejemplo, cuando se habilita la extensión de máquina virtual de Log Analytics, se solicita el **id. de área de trabajo** y la **clave principal** de Log Analytics.

También se recopila información de metadatos sobre la máquina conectada. Concretamente:

* Nombre, tipo y versión del sistema operativo
* Nombre del equipo
* Nombre de dominio completo (FQDN) del equipo
* Versión del agente Connected Machine
* Active Directory y nombre de dominio completo (FQDN) de DNS
* UUID (IDENTIFICADOR DE BIOS)
* Latido del agente de Connected Machine
* Versión del agente Connected Machine
* Clave pública para la identidad administrada
* Estado y detalles de cumplimiento de las directivas (si usa directivas de configuración de invitado)

Los servidores habilitados para Azure Arc permiten especificar la región en la que se almacenan los datos. Microsoft puede replicar en otras regiones para obtener resistencia de datos, pero no replica ni mueve datos fuera de la geografía. Estos datos se almacenan en la región donde se ha configurado el recurso de máquina de Azure Arc. Por ejemplo, si la máquina se registra en Arc en la región Este de EE. UU., estos datos se almacenan en la región de EE. UU.

> [!NOTE] 
> En el caso de Sudeste de Asia, los datos no se replican fuera de esta región. 

Para obtener más información sobre la resistencia regional y la compatibilidad con el cumplimiento, consulte [Geografía de Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el diseño de la [resistencia de Azure](/azure/architecture/reliability/architect).
