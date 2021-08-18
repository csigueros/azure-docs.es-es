---
title: 'Seguridad del firmware: seguridad de Azure'
description: Obtenga información sobre la forma en que Microsoft protege el hardware y el firmware de Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: 64c1a42147a55423336c9994246f08046f44adf5
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895659"
---
# <a name="firmware-security"></a>Seguridad de firmware
En este artículo se describe la forma en que Microsoft protege el ecosistema de hardware en la nube y las cadenas de suministro.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Protección del ecosistema de hardware en la nube
Microsoft se asocia activamente dentro del ecosistema de hardware en la nube para impulsar las mejoras de seguridad continuas. Para ello:

- Colabora con asociados de hardware y firmware de Azure (como fabricantes de componentes e integradores de sistemas) para cumplir los requisitos de seguridad de hardware y firmware de Azure.

- Permite a los asociados realizar una evaluación y mejora continua de la seguridad de sus productos mediante los requisitos definidos por Microsoft en áreas como:

  - Arranque seguro del firmware
  - Recuperación segura del firmware
  - Actualización segura del firmware
  - Criptografía del firmware
  - Hardware bloqueado
  - Telemetría de depuración granular
  - Compatibilidad del sistema con el hardware TPM 2.0 para habilitar el arranque medido

- Participa en el proyecto de seguridad [Open Compute Project (OCP)](https://www.opencompute.org/wiki/Security) y realiza contribuciones al mismo a través del desarrollo de especificaciones. Las especificaciones fomentan la coherencia y claridad del diseño y arquitectura seguros en el ecosistema.

   > [!NOTE]
   > Un ejemplo de la contribución al proyecto de seguridad OCP es la especificación de [arranque seguro de hardware](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0).

## <a name="securing-hardware-and-firmware-supply-chains"></a>Protección de cadenas de suministro de hardware y firmware
También es preciso que los proveedores de hardware de la nube de Azure sigan los requisitos y procesos de seguridad de la cadena de suministro desarrollados por Microsoft. Los procesos de desarrollo e implementación de hardware y firmware son necesarios para seguir los procesos del [ciclo de vida de desarrollo de la seguridad](https://www.microsoft.com/securityengineering/sdl) (SDL) de Microsoft, como:

- Modelado de amenazas
- Revisiones del diseño seguro
- Revisiones del firmware y pruebas de penetración
- Entornos de desarrollo y pruebas seguros
- Administración de vulnerabilidades de seguridad y respuesta a incidentes

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre lo que se hace para fomentar la seguridad y la integridad de la plataforma, vea:

- [Integridad del código de plataforma](code-integrity.md)
- [Arranque seguro](secure-boot.md)
- [Atestación de host y de arranque medido](measured-boot-host-attestation.md)
- [Proyecto Cerberus](project-cerberus.md)
- [Cifrado en reposo](encryption-atrest.md)
- [Seguridad de Hypervisor](hypervisor.md)