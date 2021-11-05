---
title: Creación de soluciones confidenciales de Azure
description: Obtener información sobre cómo crear soluciones de computación confidencial de Azure
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: f86c56fbc779af0bc1a2a002d79d41842c1ac0a7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093192"
---
# <a name="building-confidential-computing-solutions"></a>Creación de soluciones de computación confidencial

La computación confidencial de Azure ofrece varias opciones para crear soluciones confidenciales. El espectro de opciones abarca desde la habilitación de escenarios de "elevación y desplazamiento" de las aplicaciones existentes hasta un control total de diversas características de seguridad. Estas características incluyen el control sobre el nivel de acceso. Establezca los niveles de acceso del proveedor de host o del operador invitado en datos y código. También puede controlar otro acceso de rootkits o malware que puede poner en peligro la integridad de las cargas de trabajo que se ejecutan en la nube.

## <a name="solutions"></a>Soluciones

Tecnologías como los enclaves seguros o las máquinas virtuales confidenciales permiten a los clientes elegir el enfoque que quieren seguir para crear soluciones confidenciales.

- Las aplicaciones existentes sin acceso al código fuente pueden beneficiarse de máquinas virtuales confidenciales basadas en la tecnología AMD SEV-SNP para facilitar la incorporación a la plataforma de computación confidencial de Azure.
- Las cargas de trabajo sofisticadas que incluyen código propietario para protegerse de cualquier vector de confianza pueden beneficiarse de la tecnología de enclave de aplicaciones segura. Azure ofrece enclaves de aplicaciones en máquinas virtuales basadas en Intel SGX. Intel SGX proporciona protección de los datos y el código que se ejecutan en un espacio de memoria cifrado por hardware. Estas aplicaciones normalmente requieren comunicación con un enclave seguro atestiguado, que se obtiene mediante marcos de código abierto.
- Las soluciones en contenedores que se ejecutan en [contenedores confidenciales](confidential-containers.md) habilitados en Azure Kubernetes Service pueden ajustarse a los clientes que buscan un enfoque equilibrado de confidencialidad. En estos escenarios, las aplicaciones existentes se pueden empaquetar e implementar en contenedores con cambios limitados, pero ofrecen aislamiento de seguridad total del proveedor de servicios en la nube y los administradores.

![Captura de pantalla del espectro de computación confidencial, en la que se muestran opciones más sencillas para las opciones con mayor control de seguridad.](media/confidential-computing-solutions/spectrum.png)

## <a name="learn-more"></a>Más información

Para usar la eficacia de los enclaves y los entornos aislados, deberá usar herramientas que admitan la computación confidencial. Hay varias herramientas que admiten el desarrollo de aplicaciones de enclave. Obtenga información sobre el [desarrollo de aplicaciones de enclave](application-development.md) para más información. 

Obtenga información sobre las [herramientas de código abierto](enclave-development-oss.md) para crear soluciones para aplicaciones de enclave de Intel SGX para máquinas virtuales.

Use [asociados y herramientas de código abierto para contenedores confidenciales](confidential-containers.md). También puede usar algunas de estas herramientas para cargas de trabajo de Azure Kubernetes.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre el desarrollo de enclave de aplicaciones](application-development.md)
