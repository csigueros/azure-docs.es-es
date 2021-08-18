---
title: 'Integridad y seguridad de la plataforma Azure: seguridad de Azure'
description: Información general técnica sobre la integridad y seguridad de la plataforma Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e0522d6dcb02571a1ed197d60734906ee8131ab4
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893967"
---
# <a name="platform-integrity-and-security-overview"></a>Información general sobre la integridad y seguridad de la plataforma
La flota de Azure se compone de millones de servidores (hosts) y a diario se siguen agregando miles de ellos. Miles de hosts también se someten a mantenimiento a diario mediante reinicios, actualizaciones del sistema operativo o reparaciones. Antes de que un host pueda unirse a la flota y comenzar a aceptar cargas de trabajo de los clientes, Microsoft verifica que el host se encuentre en un estado seguro y confiable. Esta comprobación garantiza que no se han producido cambios malintencionados o involuntarios en los componentes de la secuencia de arranque durante los flujos de trabajo de la cadena de suministro o el mantenimiento.

## <a name="securing-azure-hardware-and-firmware"></a>Protección de hardware y firmware de Azure
En esta serie de artículos se describe cómo Microsoft garantiza la integridad y la seguridad de los hosts a través de varias fases de su ciclo de vida, desde la fabricación hasta la retirada. Los artículos abordan lo siguiente:
 
- [Seguridad de firmware](firmware.md)
- [Integridad del código de plataforma](code-integrity.md)
- [Arranque seguro UEFI](secure-boot.md)
- [Atestación de host y de arranque medido](measured-boot-host-attestation.md)
- [Proyecto Cerberus](project-cerberus.md)
- [Cifrado en reposo](encryption-atrest.md)
- [Seguridad de Hypervisor](hypervisor.md)
 
## <a name="next-steps"></a>Pasos siguientes

- Aprenda de qué manera Microsoft se asocia activamente dentro del ecosistema de hardware en la nube para impulsar las [mejoras de seguridad de firmware](firmware.md) continuas.

- Comprenda la [responsabilidad compartida en la nube](shared-responsibility.md).