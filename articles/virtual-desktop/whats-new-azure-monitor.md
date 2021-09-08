---
title: Novedades de Azure Monitor para Azure Virtual Desktop
description: Nuevas características y actualizaciones de productos para el agente de Azure Virtual Desktop.
author: Heidilohr
ms.topic: reference
ms.date: 07/09/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 000fb5a26c34a8fbb9013ad873aa167aa759ae7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450884"
---
# <a name="whats-new-in-azure-monitor-for-azure-virtual-desktop"></a>Novedades de Azure Monitor para Azure Virtual Desktop

En este artículo se describen los cambios realizados en cada nueva versión de Azure Monitor para Azure Virtual Desktop.

Si no está seguro de qué versión de Azure Monitor está usando actualmente, puede encontrarla en la esquina inferior derecha de la página Conclusiones o en el libro de configuración. Para acceder al libro, vaya a [https://aka.ms/azmonwvdi](https://aka.ms/azmonwvdi).

## <a name="how-to-read-version-numbers"></a>Lectura de los números de versión

Hay tres números en cada versión de Azure Monitor para Azure Virtual Desktop. Esto es lo que significa cada número:

- El primer número es la versión principal y normalmente se usa para las versiones principales.

- El segundo número es la versión secundaria. Las versiones secundarias son para cambios compatibles con versiones anteriores, como nuevas características y avisos de desuso.

- El tercer número es la versión de revisión, que se usa para pequeños cambios que corrigen errores o comportamientos incorrectos.

Por ejemplo, una versión con el número de versión 1.2.31 se encuentra en la primera versión principal, la segunda versión secundaria y el número de revisión 31.

Cuando se aumenta uno de los números, también deben cambiar todos los números que le siguen. Una versión tiene un número de versión, pero no todos los números de versión realizan un seguimiento de las versiones. Los números de revisión pueden ser algo arbitrarios, por ejemplo.

## <a name="version-100"></a>Versión 1.0.0

Fecha de lanzamiento: 21 de marzo de 2021.

En esta versión, hemos realizado los siguientes cambios:

- Se ha introducido un nuevo indicador visual para errores y advertencias de alto impacto desde el registro de eventos del agente de Azure Virtual Desktop en la página de diagnóstico del host.

- Hemos quitado cinco contadores de rendimiento de proceso costosos de la configuración predeterminada. Para obtener más información, consulte la entrada de blog en [Guía actualizada de Azure Monitor para Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/updated-guidance-on-azure-monitor-for-wvd/m-p/2236173).

- El proceso de instalación del registro de eventos de Windows para el libro de configuración ahora está automatizado.

- El libro de configuración ahora admite la implementación automatizada de registros de eventos de Windows recomendados.

- El libro de configuración ahora puede instalar el agente de Log Analytics y establecer el área de trabajo preferida para los hosts de sesión fuera de la región del grupo de recursos.

- El libro de configuración ahora tiene un diseño con pestañas para el proceso de instalación.

- Hemos introducido el control de versiones con esta actualización.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre las novedades, consulte [Novedades de Azure Virtual Desktop](whats-new.md).

Para obtener más información sobre Azure Monitor para Azure Virtual Desktop, consulte [Uso de Azure Monitor para Azure Virtual Desktop para supervisar implementaciones](azure-monitor.md).
