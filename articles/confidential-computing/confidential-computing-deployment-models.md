---
title: Elegir entre modelos de implementación
description: Elegir entre modelos de implementación
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: f0d28b9f6e9894ec8a5506a428b8c15cadb2f716
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845520"
---
# <a name="confidential-computing-deployment-models"></a>Modelos de implementación de computación confidencial

La computación confidencial de Azure admite varios modelos de implementación. Estos diferentes modelos admiten la amplia variedad de requisitos de seguridad del cliente para la informática moderna en la nube.

## <a name="infrastructure-as-a-service-iaas"></a>Infraestructura como servicio (IaaS)

En el caso de la infraestructura como servicio (IaaS), puede usar máquinas virtuales (VM) confidenciales en la computación confidencial. Puede usar máquinas virtuales basadas en [enclaves de aplicaciones de Intel Software Guard Extensions (SGX)](confidential-computing-enclaves.md) o en la tecnología AMD SEV-SNP.

## <a name="platform-as-a-service-paas"></a>Plataforma como servicio (PaaS)

En el caso de la plataforma como servicio (PaaS), puede usar [contenedores confidenciales](confidential-containers.md) en la computación confidencial. Esta oferta incluye contenedores que tienen en cuenta el enclave en Azure Kubernetes Service (AKS).

La elección del modelo de implementación adecuado depende de muchos factores. Es posible que deba tener en cuenta la existencia de aplicaciones heredadas, funcionalidades del sistema operativo y migración desde redes locales.

Aunque todavía hay muchas razones para usar máquinas virtuales, los contenedores proporcionan flexibilidad adicional para los muchos entornos de software de TI moderna. 

Los contenedores pueden admitir aplicaciones que:

- Se ejecutan en varias nubes.
- Se conectan a microservicios.
- Usan varios lenguajes de programación y marcos.
- Usan la automatización y Azure Pipelines, incluida la integración continua y la implementación continua (CI/CD).

Los contenedores también aumentan la portabilidad de las aplicaciones y mejoran el uso de los recursos mediante la aplicación de la elasticidad de la nube de Azure.

Normalmente, implementaría la solución en máquinas virtuales confidenciales si:

- Tiene aplicaciones heredadas que no se pueden modificar ni agrupar en contenedores. Sin embargo, sigue siendo necesario introducir la protección de los datos en la memoria, mientras se procesan los datos.
- Ejecuta varias aplicaciones que requieren sistemas operativos (SO) diferentes en una sola parte de la infraestructura.
- Quiere emular un entorno informático completo, incluidos todos los recursos del sistema operativo.
- Migra las máquinas virtuales existentes del entorno local a Azure.

Puede optar por un enfoque confidencial basado en contenedores cuando:

- Le preocupa la asignación de costes y recursos. Sin embargo, necesita una plataforma más ágil para la implementación de sus aplicaciones y conjuntos de datos propietarios.
- Crea una solución nativa de la nube moderna. También tiene control total del código fuente y del proceso de implementación.
- Necesita compatibilidad con varias nubes.

Ambas opciones ofrecen el nivel de seguridad más alto para los servicios de Azure. Hay algunas diferencias en las posturas de seguridad de las [máquinas virtuales confidenciales](#confidential-vms-on-amd-sev-snp) y los [contenedores confidenciales](#secure-enclaves-on-intel-sgx), como se muestra a continuación.

### <a name="confidential-vms-on-amd-sev-snp"></a>Máquinas virtuales confidenciales en AMD SEV-SNP

**Las máquinas virtuales confidenciales en AMD SEV-SNP** ofrecen protección cifrada por hardware de toda la máquina virtual frente al acceso no autorizado por el administrador del host. Este nivel normalmente incluye el hipervisor, que administra el proveedor de servicios en la nube (CSP). Puede usar este tipo de máquina virtual confidencial para evitar que el CSP acceda a los datos y el código ejecutado dentro de la máquina virtual.

Los administradores de las máquinas virtuales o cualquier otra aplicación o servicio que se ejecute dentro de la máquina virtual trabajarán más allá de los límites protegidos. Estos usuarios y servicios pueden acceder a los datos y el código dentro de la máquina virtual.

La tecnología AMD SEV-SNP proporciona aislamiento de máquina virtual del hipervisor. La protección de la integridad de la memoria basada en hardware ayuda a evitar ataques malintencionados basados en hipervisores. El modelo SEV-SNP confía en AMD Secure Processor y en la máquina virtual. El modelo no confía en ningún otro componente de hardware y software. Los componentes que no son de confianza son el BIOS y el hipervisor en el sistema host, entre otros.

:::image type="content" source="media/confidential-computing-deployment-models/amd-sev-snp-vm.png" alt-text="Diagrama de la arquitectura de la máquina virtual de AMD SEV-SNP, que define los componentes de confianza y los que no son de confianza.":::

### <a name="secure-enclaves-on-intel-sgx"></a>Enclaves seguros en Intel SGX

**Los enclaves seguros en Intel SGX protegen** los espacios de memoria dentro de una máquina virtual con cifrado basado en hardware. El límite de seguridad de los enclaves de aplicaciones está más restringido que las máquinas virtuales confidenciales en AMD SEV-SNP. Para Intel SGX, el límite de seguridad se aplica a partes de memoria dentro de una máquina virtual. Los usuarios, las aplicaciones y los servicios que se ejecutan dentro de la máquina virtual con tecnología Intel SGX no pueden acceder a ningún dato ni código en ejecución dentro del enclave.

Intel SGX ayuda a proteger los datos en uso mediante el aislamiento de la aplicación. Al proteger el código y los datos seleccionados frente a modificaciones, los desarrolladores pueden dividir su aplicación en enclaves protegidos o módulos de ejecución de confianza para ayudar a aumentar la seguridad de la aplicación. Las entidades de fuera del enclave no pueden leer la memoria del enclave ni escribir en ella, independientemente de sus niveles de permisos. El hipervisor o el sistema operativo tampoco pueden obtener este acceso a través de llamadas normales de nivel de sistema operativo. Para llamar a una función de enclave, debe usar un nuevo conjunto de instrucciones en las CPU de Intel SGX. Este proceso incluye varias comprobaciones de protección.

:::image type="content" source="media/confidential-computing-deployment-models/intel-sgx-enclave.png" alt-text="Diagrama de la arquitectura de enclaves de Intel SGX, que muestra información segura dentro del enclave de la aplicación.":::

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre contenedores confidenciales](confidential-containers.md)
- [Más información sobre los enclaves de computación confidencial](confidential-computing-enclaves.md)
