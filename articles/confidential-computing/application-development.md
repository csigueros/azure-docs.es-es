---
title: Herramientas de desarrollo de computación confidencial de Azure
description: Uso de herramientas y bibliotecas a fin de desarrollar aplicaciones para computación confidencial en Intel SGX
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6300e0cee2659f767c2d765de5a24591fc962e6f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081322"
---
# <a name="application-enclave-development"></a>Desarrollo de enclave de aplicaciones 

Con la computación confidencial de Azure, puede crear enclaves de aplicación para máquinas virtuales (VM) que ejecutan Software Guard Extensions (SGX) de Intel. Es importante comprender las herramientas y el software relacionados antes de comenzar el desarrollo.

> [!NOTE]
> Si todavía no ha leído la introducción a las [máquinas virtuales y enclaves de Intel SGX](confidential-computing-enclaves.md), debe hacerlo antes de continuar.

## <a name="application-enclaves"></a>Enclaves de aplicación

Los enclaves de aplicación son entornos aislados que protegen datos y código específicos. Al crear enclaves, debe determinar qué parte de la aplicación se ejecuta dentro del enclave. Al crear o administrar enclaves, asegúrese de usar SDK y marcos compatibles para la pila de implementación elegida. 

En la actualidad, la computación confidencial ofrece enclaves de aplicación. En concreto, puede implementar y desarrollar con enclaves de aplicación mediante [máquinas virtuales confidenciales con Intel SGX habilitado](virtual-machine-solutions-sgx.md). 

## <a name="intel-sgx"></a>Intel SGX

Con la tecnología Intel SGX, puede cifrar enclaves de aplicación o entornos de ejecución de confianza con una clave inaccesible almacenada dentro de la CPU. El descifrado del código y los datos dentro del enclave se produce dentro del procesador. Solo tiene acceso la CPU. Este nivel de aislamiento protege los datos en uso y los ataques de hardware y software. Para más información, vea el [sitio web de Intel SGX](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html). 

Azure ofrece Intel SGX en un entorno de virtualización mediante de varios tamaños de máquina virtual de la serie DC. Varios tamaños de máquina virtual permiten varios tamaños de caché de páginas de enclave (EPC). EPC es la cantidad máxima de área de memoria para un enclave en esa máquina virtual. Actualmente, las máquinas virtuales Intel SGX están disponibles en máquinas virtuales de la [serie DCsv2](../virtual-machines/dcv2-series.md) y de la [serie DCsv3/DCdsv3](../virtual-machines/dcv3-series.md).


### <a name="developing-applications"></a>Desarrollo de aplicaciones

En una aplicación creada con enclaves hay dos particiones. 

El **host** es el componente que "no es de confianza". La aplicación de enclave se ejecuta sobre el host. El host es un entorno que no es de confianza. Al implementar código de enclave en el host, el host no puede acceder a ese código.

El **enclave** es el componente "de confianza". El código de aplicación y sus datos y memoria en caché se ejecutan en el enclave. El entorno de enclave protege los secretos y la información confidencial. Asegúrese de que en un enclave se realicen cálculos seguros.

![Diagrama de una aplicación, en el que se muestran las particiones de host y enclave. Dentro del enclave se encuentran los componentes de datos y código de aplicación.](media/application-development/oe-sdk.png)

Para usar la eficacia de los enclaves y los entornos aislados, elija herramientas que admitan la computación confidencial. Hay varias herramientas que admiten el desarrollo de aplicaciones de enclave. Por ejemplo, puede usar estos marcos de código abierto: 

- [Open Enclave Software Development Kit (OE SDK)](enclave-development-oss.md#oe-sdk)
- [SDK de Intel SGX](enclave-development-oss.md#intel-sdk)
- [Kit de desarrollo de software de EGo](enclave-development-oss.md#ego)
- [El marco de trabajo del Consorcio de Computación Confidencial (CCF)](enclave-development-oss.md#ccf)

Al diseñar una aplicación, identifique y determine qué parte debe ejecutarse en enclaves. El código del componente de confianza está aislado del resto de la aplicación. Una vez que el enclave se inicializa y el código se carga en la memoria, los componentes que no son de confianza no pueden leer ni cambiar ese código.

## <a name="next-steps"></a>Pasos siguientes 

- [Implementación de una máquina virtual de computación confidencial de Intel SGX](quick-create-portal.md)
- [Inicio del desarrollo de aplicaciones con software de código abierto](enclave-development-oss.md)
