---
title: Selección de ofertas de contenedor para la computación confidencial
description: Cómo elegir las ofertas de contenedor confidencial adecuadas para cumplir las necesidades de seguridad, de aislamiento y de los desarrolladores.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 158e3e5a8191bc8e193ff9dab803feed7da2cc8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017609"
---
# <a name="choosing-confidential-container-offerings"></a>Selección de ofertas de contenedor confidencial

La computación confidencial de Azure ofrece varios tipos de contenedores confidenciales. Puede usar estos contenedores para mantener la integridad y la confidencialidad de los datos, así como la integridad del código.

Los contenedores confidenciales también contribuyen a la protección del código mediante el cifrado. Puede crear garantías basadas en hardware y raíz de confianza de hardware. También puede reducir el área expuesta a ataques gracias a los contenedores confidenciales.

## <a name="enclaves-confidential-containers"></a>Contenedores confidenciales de enclaves

Puede implementar contenedores confidenciales con enclaves. Este método de implementación de contenedores ofrece la máxima seguridad y aislamiento de proceso, con una base de computación de confianza (TCB) menor. Están disponibles contenedores confidenciales basados en Intel Software Guard Extensions Intel (SGX) que se ejecutan en el entorno de ejecución de confianza (TEE) basado en hardware. Estos contenedores admiten la migración mediante lift-and-shif de las aplicaciones de contenedor existentes. Otra opción es permitir la creación de aplicaciones personalizadas compatibles con enclaves.

Hay dos modelos de programación e implementación en Azure Kubernetes Service (AKS). 

Los **contenedores sin modificar** admiten lenguajes de programación superiores en Intel SGX mediante el ecosistema de asociados de Azure de proyectos de OSS. Para obtener más información, consulte el [flujo de implementación de contenedores sin modificar y los ejemplos relacionados](./confidential-containers.md).

Los **contenedores compatibles con enclaves** usan un modelo de programación de Intel SGX personalizado. Para obtener más información, consulte el [flujo de implementación de contenedores compatibles con enclaves y los ejemplos relacionados](./enclave-aware-containers.md). 

![Diagrama de contenedores confidenciales de enclave con Intel SGX, en el que se muestran los límites de aislamiento y seguridad.](./media/confidential-containers/confidential-container-intel-sgx.png)

## <a name="learn-more"></a>Más información

- [Máquinas virtuales confidenciales de Intel SGX en Azure](./virtual-machine-solutions-sgx.md)
- [Contenedores confidenciales en Azure](./confidential-containers.md)
