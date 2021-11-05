---
title: 'Compilación con enclaves SGX: Azure Virtual Machines'
description: Obtenga información sobre el hardware de Intel SGX para habilitar las cargas de trabajo de computación confidencial.
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0870c0cc762078ec38e978c8dab4815ff37741a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067610"
---
# <a name="build-with-sgx-enclaves"></a>Compilación con enclaves SGX 

La computación confidencial de Azure ofrece máquinas virtuales (VM) de la serie [DCsv2](../virtual-machines/dcv2-series.md) y de las series [DCsv3 y DCdsv3*](../virtual-machines/dcv3-series.md). Estas VM tienen [Intel® Software Guard Extensions (SGX)](https://intel.com/sgx). 

La tecnología Intel SGX permite a los clientes crear enclaves que protegen los datos y los mantienen cifrados mientras la CPU los procesa. El sistema operativo (SO) y el hipervisor no pueden acceder a los datos. Los administradores de centros de datos con acceso físico tampoco pueden acceder a los datos.

## <a name="enclaves-concept"></a>Concepto de enclaves

Los enclaves son partes protegidas del procesador y la memoria del hardware. No se pueden ver los datos ni el código dentro del enclave, ni siquiera con un depurador. Si un código que no es de confianza intenta cambiar el contenido de la memoria del enclave, SGX deshabilita el entorno y deniega las operaciones. Estas funcionalidades únicas le ayudan a proteger sus secretos para que no sean accesibles sin cifrar.  

![Diagrama del modelo de VM en el que se muestran los datos protegidos en los enclaves.](media/overview/hardware-backed-enclave.png)

Imagínese un enclave como una caja de seguridad protegida. Introduce el código y los datos cifrados dentro de la caja de seguridad. Desde fuera, no puede ver nada. Le proporciona al enclave una clave para descifrar los datos. El enclave procesa y vuelve a cifrar los datos antes de devolverlos.

Cada enclave tiene una caché de página cifrada (EPC) con un tamaño establecido. La EPC determina la cantidad de memoria que puede contener un enclave. Las VM de la [serie DCsv2](../virtual-machines/dcv2-series.md) pueden contener hasta 168 MiB. Las VM de las [series DCsv3 y DCdsv3](../virtual-machines/dcv3-series.md)* pueden contener hasta 256 GB para cargas de trabajo que consumen más memoria.

> [!NOTE]
> *DCsv3 y DCdsv3 están en **versión preliminar pública** desde el 1 de noviembre de 2021.

Para obtener más información, consulte [cómo implementar las VM de Intel SGX con enclaves de confianza basados en hardware](virtual-machine-solutions-sgx.md).

## <a name="developing-for-enclaves"></a>Desarrollo para enclaves

Puede usar varias [herramientas de software para desarrollar aplicaciones que se ejecutan en enclaves](application-development.md). Estas herramientas le ayudan a blindar partes del código y los datos dentro del enclave. Asegúrese de que nadie externo a su entorno de confianza pueda ver o modificar los datos con estas herramientas.

## <a name="next-steps"></a>Pasos siguientes
- [Implementación de una máquina virtual de la serie DCsv2 o de las series DCsv3 y DCdsv3](quick-create-portal.md)
- [Desarrollo de una aplicación compatible con enclave](application-development.md) mediante el SDK de OE
