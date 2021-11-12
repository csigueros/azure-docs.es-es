---
title: Atestación para enclaves de SGX
description: Compruebe que el enclave de SGX de computación confidencial está protegido con atestación.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03a389facebbfcb242cf63afd0a8fcca6ad7b670
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347677"
---
# <a name="attestation-for-sgx-enclaves"></a>Atestación para enclaves de SGX

La computación confidencial en Azure ofrece máquinas virtuales basadas en Intel SGX que pueden aislar una parte del código o los datos. Al trabajar con estos [enclaves](confidential-computing-enclaves.md), puede comprobar y validar que el entorno de confianza es seguro. Esta comprobación es el proceso de atestación. 

## <a name="overview"></a>Información general 

La atestación permite a un usuario de confianza tener una mayor confianza de que su software está:

1. Ejecutándose en un enclave
1. Actualizado
1. Seguridad

Por ejemplo, un enclave puede pedir al hardware subyacente que genere una credencial. Esta credencial incluye la prueba de que el enclave existe en la plataforma. Un segundo enclave puede recibir y comprobar que la misma plataforma generó el informe.

![Diagrama del proceso de atestación, que muestra el intercambio seguro del cliente con el enclave que contiene los datos y el código de la aplicación.](media/attestation/attestation.png)

Implemente la atestación con un servicio de atestación seguro que sea compatible con el software del sistema y el silicio. Por ejemplo:

- [Microsoft Azure Attestation](../attestation/overview.md) 
- [Servicios de atestación y aprovisionamiento de Intel](https://software.intel.com/sgx/attestation-services),


Ambos servicios son compatibles con máquinas virtuales de la serie Intel SGX DCsv2 de computación confidencial de Azure. Las máquinas virtuales de las series DCsv3 y DCdsv3 no son compatibles con el servicio de atestación de Intel. 

## <a name="next-step"></a>Paso siguiente

> [!div class="nextstepaction"]
> [Muestras de Microsoft Azure Attestation para aplicaciones compatibles con enclaves](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)
