---
title: Desarrollo de enclaves de aplicaciones con soluciones de código abierto en la Computación confidencial de Azure
description: Aprenda a usar herramientas para desarrollar aplicaciones de Intel SGX para la Computación confidencial de Azure.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71be688b3f69860468ce6c638e484caae0ba72f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290217"
---
# <a name="open-source-solutions-to-build-enclave-applications"></a>Soluciones de código abierto para compilar aplicaciones de enclave

En este artículo se indican las soluciones de código abierto para compilar aplicaciones que usan enclaves de aplicaciones. Antes de leerlo, asegúrese de leer la página conceptual de las [aplicaciones de enclave](application-development.md). 

## <a name="intel-sgx-compatible-tools"></a>Herramientas compatibles con Intel SGX
Azure ofrece enclaves de aplicaciones a través de [máquinas virtuales confidenciales con Intel Software Guard Extensions (SGX) habilitado](virtual-machine-solutions-sgx.md). Después de implementar una máquina virtual de Intel SGX, necesitará herramientas especializadas para que la aplicación sea "compatible con el enclave". De este modo, puede compilar aplicaciones que tengan partes de código de confianza y que no sean de confianza. 

Por ejemplo, puede usar estos marcos de código abierto: 

- [Kit de desarrollo de software (SDK) de Open Enclave (OE)](#oe-sdk)
- [SDK de EGo](#ego)
- [SDK de Intel SGX](#intel-sdk)
- [El marco de trabajo del Consorcio de Computación Confidencial (CCF)](#ccf)

Si no quiere escribir código de aplicación nuevo, puede encapsular una aplicación en contenedores mediante [habilitadores de contenedor confidenciales](confidential-containers.md).

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Use una biblioteca o un marco de trabajo compatible con el proveedor si quiere escribir código que se ejecute en un enclave. [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) es un SDK de código abierto que permite la abstracción sobre diferente hardware habilitado para la computación confidencial. 

OE SDK se compila como una única capa de abstracción en cualquier hardware de cualquier CSP. OE SDK se puede usar sobre las máquinas virtuales de computación confidencial de Azure para crear y ejecutar aplicaciones a partir de los enclaves. Microsoft mantiene el repositorio de Open Enclave.

### <a name="ego-software-development-kit"></a>Kit de desarrollo de software de EGo <a id="ego"></a>

[EGo](https://ego.dev/) es un SDK de código abierto que permite ejecutar aplicaciones escritas en el lenguaje de programación Go dentro de los enclaves. EGo se basa en el SDK de OE y viene con una biblioteca de Go en el enclave para la atestación y el sellado. Muchas aplicaciones de Go existentes se ejecutan en EGo sin modificaciones.  

### <a name="intel-sgx-software-development-kit"></a>Kit de desarrollo de software de Intel SGX <a id="intel-sdk"></a>
El [SDK de Intel SGX](https://01.org/intel-softwareguard-extensions) lo desarrolla y mantiene el equipo de SGX en Intel. El SDK es una herramienta de recopilación que permite a los desarrolladores de software crear y depurar aplicaciones habilitadas para Intel SGX en C y C++.

### <a name="confidential-consortium-framework-ccf"></a>Marco de trabajo del Consorcio de Computación Confidencial (CCF) <a id="ccf"></a>

El [Marco de consorcio confidencial](https://www.microsoft.com/research/project/confidential-consortium-framework/) ([CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/)) es un ejemplo de un marco de cadena de bloques distribuido. El CCF se basa en la computación confidencial de Azure. Con el impulso de Microsoft Research, este marco usa la eficacia de los entornos de ejecución de confianza (TEE) para crear una red de enclaves remotos para la atestación. Los nodos se pueden ejecutar sobre máquinas virtuales de Azure Intel SGX y aprovechar la infraestructura del enclave. Mediante los protocolos de atestación, los usuarios de la cadena de bloques pueden comprobar la integridad de un nodo de CCF y comprobar de manera eficaz toda la red.

En el Consorcio de Computación Confidencial, el libro de contabilidad descentralizado se compone de cambios registrados en un almacén de clave-valor que se replica en todos los nodos de la red. Cada uno de estos nodos ejecuta un motor de transacciones que pueden desencadenar los usuarios de la cadena de bloques sobre TLS. Cuando se desencadena un punto de conexión, se muta el almacén de clave-valor. Antes de que el cambio cifrado se registre en el libro de contabilidad descentralizado, se debe acordar en más de un nodo para llegar a un consenso.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de una máquina virtual de computación confidencial de Intel SGX](quick-create-portal.md)
