---
title: 'Integridad del firmware: seguridad de Azure'
description: Conozca las medidas criptográficas para garantizar la integridad del firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: cda9aacfc3a207aa43c63cc26189da64fac87e85
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893931"
---
# <a name="project-cerberus"></a>Proyecto Cerberus

Cerberus es una raíz de confianza de hardware compatible con la normativa 800-193 del NIST con una identidad que no se puede clonar. Cerberus protege la integridad del software para aumentar la seguridad de la infraestructura de Azure.

## <a name="enabling-an-anchor-of-trust"></a>Habilitación de un anclaje de confianza
Cada chip de Cerberus tiene una identidad criptográfica única que se establece mediante una cadena de certificados firmada con raíz a una entidad de certificación (CA) de Microsoft. Las medidas obtenidas de Cerberus se pueden usar para validar la integridad de los componentes, como:

- administrador de flujos de trabajo
- Controlador de administración de placa base (BMC)
- Todos los periféricos, incluida la tarjeta de interfaz de red y el [sistema en chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC)

Este anclaje de confianza ayuda a defender el firmware de la plataforma de:

- Los binarios de firmware en peligro que se ejecutan en la plataforma
- El malware y los hackers que aprovechan los errores del sistema operativo, la aplicación o el hipervisor
- Ciertos tipos de ataques de la cadena de suministro (fabricación, ensamblado, tránsito)
- Intrusos malintencionados con privilegios administrativos o acceso al hardware

## <a name="cerberus-attestation"></a>Atestación de Cerberus
Cerberus autentica la integridad del firmware de los componentes de servidor mediante un manifiesto de firmware de la plataforma (PFM). PFM define una lista de versiones de firmware autorizadas y proporciona una medida de la plataforma al [servicio de atestación del host](measured-boot-host-attestation.md) de Azure. El servicio de atestación del host valida las medidas y toma una determinación para permitir que solo los hosts de confianza se unan a la flota de Azure y hospeden las cargas de trabajo de los clientes.

Junto con el servicio de atestación del host, las funcionalidades de Cerberus mejoran y promueven una infraestructura de producción de Azure de alta seguridad.

> [!NOTE]
> Para más información, consulte la información de [Proyecto Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) en GitHub.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre lo que se hace para fomentar la seguridad y la integridad de la plataforma, vea:

- [Seguridad de firmware](firmware.md)
- [Integridad del código de plataforma](code-integrity.md)
- [Arranque seguro](secure-boot.md)
- [Atestación del host y arranque medido](measured-boot-host-attestation.md)
- [Cifrado en reposo](encryption-atrest.md)
- [Seguridad de Hypervisor](hypervisor.md)