---
title: 'Seguridad del hipervisor en la flota de Azure: seguridad de Azure'
description: Información técnica sobre la seguridad del hipervisor en la flota de Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e8da9ae3d8f8c13bf52c5c0a2ea61b38f316a8b2
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895641"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Seguridad del hipervisor en la flota de Azure

El sistema de hipervisor de Azure se basa en Windows Hyper-V. El sistema de hipervisor permite al administrador del equipo especificar las particiones invitadas que tienen espacios de direcciones independientes. Los espacios de direcciones independientes permiten cargar un sistema operativo y las aplicaciones que funcionan en paralelo del sistema operativo (host) que se ejecuta en la partición raíz del equipo. El sistema operativo host (también conocido como partición raíz con privilegios) tiene acceso directo a todos los dispositivos físicos y periféricos del sistema (controladores de almacenamiento, adaptaciones de redes). El sistema operativo host permite a las particiones invitadas compartir el uso de estos dispositivos físicos mediante la exposición de "dispositivos virtuales" a cada partición invitada. Así, un sistema operativo que se ejecuta en una partición invitada tiene acceso a los dispositivos periféricos virtualizados que proporcionan los servicios de virtualización que se ejecutan en la partición raíz.

El hipervisor de Azure se ha compilado teniendo en cuenta los siguientes objetivos de seguridad:

| Objetivos | Source |
|--|--|
| Aislamiento | Una directiva de seguridad dicta que no haya ninguna transferencia de información entre máquinas virtuales. Esta restricción requiere capacidades de Virtual Machine Manager (VMM) y hardware para aislamiento de memoria, dispositivos, la red y recursos administrados como los datos guardados. |
| Integridad de VMM | Para lograr la integridad global del sistema, se establece y mantiene la integridad de los componentes individuales del hipervisor. |
| Integridad de plataforma | La integridad del hipervisor depende de la integridad del hardware y el software en los que se basa. Aunque el hipervisor no tiene control directo sobre la integridad de la plataforma, Azure se basa en mecanismos de hardware y firmware como el chip [Cerberus](project-cerberus.md) para proteger y detectar la integridad de la plataforma subyacente. Se evita que los invitados y VMM se ejecuten si la integridad de la plataforma está en peligro. |
| Acceso restringido | Las funciones de administración solo las ejercen los administradores autorizados que se conectan a través de conexiones seguras. Los mecanismos de control de acceso basado en rol de Azure (RBAC de Azure) aplican un principio de privilegios mínimos. |
| Auditoría | Azure habilita la capacidad de auditoría para capturar y proteger los datos sobre lo que ocurre en un sistema para que se puedan inspeccionar más adelante. |

El enfoque de Microsoft de proteger el hipervisor de Azure y el subsistema de virtualización se puede desglosar en las tres categorías siguientes.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Límites de seguridad fuertemente definidos aplicados por el hipervisor

El hipervisor de Azure aplica varios límites de seguridad entre:

- Particiones "invitadas" virtualizadas y la partición con privilegios ("host")
- Varios invitados
- Él mismo y el host
- Él mismo y todos los invitados

La confidencialidad, la integridad y la disponibilidad de los límites de seguridad del hipervisor están garantizadas. Los límites defienden frente a una serie de ataques, como las pérdidas de información de canal lateral, la denegación de servicio y la elevación de privilegios.

El límite de seguridad del hipervisor también proporciona segmentación entre inquilinos para el tráfico de red, los dispositivos virtuales, el almacenamiento, los recursos de proceso y todos los demás recursos de máquina virtual.

## <a name="defense-in-depth-exploit-mitigations"></a>Mitigaciones de vulnerabilidades de seguridad de defensa en profundidad

En el improbable caso de que un límite de seguridad tenga una vulnerabilidad, el hipervisor de Azure incluye varias capas de mitigaciones, que incluyen:

- Aislamiento del proceso basado en host que hospeda componentes entre máquinas virtuales
- Seguridad basada en la virtualización (VBS) para garantizar la integridad de los componentes de modo kernel y usuario desde un entorno seguro
- Varios niveles de mitigaciones de vulnerabilidades de seguridad. Las mitigaciones incluyen la selección aleatoria del diseño del espacio de direcciones (ASLR), la Prevención de ejecución de datos (DEP), la protección de código arbitraria, la integridad del flujo de control y la prevención de datos dañados
- Inicialización automática de variables de pila en el nivel de compilador
- API de kernel que inicializan en cero automáticamente las asignaciones del montón de kernel realizadas por Hyper-V

Estas mitigaciones están diseñadas para que el desarrollo de una vulnerabilidad de seguridad para una vulnerabilidad entre máquinas virtuales sea inviable.

## <a name="strong-security-assurance-processes"></a>Sólidos procesos de garantía de seguridad

La superficie expuesta a ataques relacionada con el hipervisor incluye redes de software, dispositivos virtuales y todas las superficies entre máquinas virtuales. Se realiza un seguimiento de la superficie expuesta a ataques por medio de la integración de compilación automatizada, que desencadena revisiones de seguridad periódicas.

Todas las superficies expuestas a ataques de las máquinas virtuales son modeladas frente a amenazas, revisadas, analizadas y probadas en busca de infracciones de los límites de seguridad por parte del equipo RED. Microsoft tiene un [Programa de recompensas](https://www.microsoft.com/msrc/bounty-hyper-v) que concede un premio por las vulnerabilidades relevantes detectadas en las versiones de producto válidas de Microsoft Hyper-V.

> [!NOTE]
> Obtenga más información sobre los [sólidos procesos de garantía de seguridad](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) de Hyper-V.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre lo que se hace para fomentar la seguridad y la integridad de la plataforma, vea:

- [Seguridad de firmware](firmware.md)
- [Integridad del código de plataforma](code-integrity.md)
- [Arranque seguro](secure-boot.md)
- [Atestación de host y de arranque medido](measured-boot-host-attestation.md)
- [Proyecto Cerberus](project-cerberus.md)
- [Cifrado en reposo](encryption-atrest.md)