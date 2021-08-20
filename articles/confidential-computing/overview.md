---
title: Introducción a la computación confidencial de Azure
description: Introducción a la computación confidencial de Azure (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: ef92a0f8c560dd20edf8711fba36cdf005ef15be
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443701"
---
# <a name="confidential-computing-on-azure"></a>Computación confidencial en Azure

La computación confidencial de Azure permite aislar los datos confidenciales mientras se procesan en la nube. Muchos sectores usan la computación confidencial para:

- Proteger los datos financieros
- Proteger la información de los pacientes
- Ejecutar procesos de aprendizaje automático sobre información confidencial
- Realizar algoritmos en conjuntos de datos cifrados de varios orígenes


## <a name="overview"></a>Información general
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sabemos que es importante proteger los datos en la nube, y somos conscientes de sus preocupaciones. Estas son solo algunas preguntas que nuestros clientes pueden plantearse al mover cargas de trabajo confidenciales a la nube: 

- ¿Cómo tener la seguridad de que Microsoft no puede acceder a los datos que no están cifrados?
- ¿Cómo se evitan las amenazas de seguridad de los administradores con privilegios dentro de mi empresa?
- ¿Cuáles son otras formas de evitar que otros usuarios accedan a los datos confidenciales de los clientes?

Microsoft Azure ayuda a reducir la superficie expuesta a los ataques para conseguir una mayor protección de los datos. Azure ya ofrece muchas herramientas para proteger los [**datos en reposo**](../security/fundamentals/encryption-atrest.md) mediante modelos como el cifrado del lado cliente y el cifrado del lado servidor. Además, Azure ofrece mecanismos para cifrar los [**datos en tránsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) mediante protocolos seguros como TLS y HTTPS. En esta página se presenta un tercer tipo de cifrado de datos: el cifrado de los **datos en uso**.

## <a name="introduction-to-confidential-computing"></a>Introducción a la computación confidencial  

La computación confidencial es un término del sector que definió el [Consorcio de Computación Confidencial](https://confidentialcomputing.io/) (CCC), una fundación dedicada a definir y acelerar la adopción de la computación confidencial. El CCC define la computación confidencial como: la protección de los datos en uso mediante la realización de cálculos en un entorno de ejecución de confianza (TEE) basado en hardware.

Un TEE es un entorno que aplica la ejecución de solo código autorizado. Los datos de TEE no se pueden leer ni alterar con ningún código ajeno a ese entorno. 

### <a name="lessen-the-need-for-trust"></a>Reducción de la necesidad de confianza
La ejecución de cargas de trabajo en la nube requiere confianza. Esta confianza se concede a varios proveedores que habilitan diferentes componentes de la aplicación.


**Proveedores de software de aplicaciones**: Confíe en el software mediante la implementación en el entorno local, el uso de código abierto o la creación de software de aplicaciones interno.

**Proveedores de hardware**: Confíe en el hardware mediante el uso de hardware local o hardware interno. 

**Proveedores de infraestructura**: Confíe en sus proveedores de servicios en la nube o administre sus propios centros de datos locales.


La computación confidencial de Azure hace que sea más fácil confiar en el proveedor de nube, reduciendo la necesidad de confiar en diversos aspectos de la infraestructura en la nube de proceso. La computación confidencial de Azure minimiza la necesidad de confianza para el kernel del sistema operativo del host, el hipervisor, el administrador de la máquina virtual y el administrador del host.

### <a name="reducing-the-attack-surface"></a>Reducción de la superficie expuesta a ataques
La base de computación de confianza (TCB) hace referencia a todos los componentes de hardware, firmware y software del sistema que proporcionan un entorno seguro. Los componentes de la TCB se consideran "críticos". Si un componente de la TCB está en peligro, toda la seguridad del sistema puede verse comprometida. 

Una TCB menor significa una mayor seguridad. Existe menos riesgo de exposición a diversas vulnerabilidades, malware, ataques y usuarios malintencionados. La computación confidencial de Azure pretende reducir la TCB de las cargas de trabajo en la nube ofreciendo TEE. TEE reduce la TCB a los archivos binarios, el código y las bibliotecas del entorno de ejecución de confianza. Al usar la infraestructura y los servicios de Azure para la computación confidencial, puede quitar todo lo relacionado con Microsoft de la TCB.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Uso de Azure para la computación confidencial basada en la nube <a id="cc-on-azure"></a>

La computación confidencial de Azure le permite aprovechar las funcionalidades de computación confidencial en un entorno virtualizado. Ahora puede usar herramientas, software e infraestructura de nube para crear a partir de hardware seguro.  

**Impida el acceso no autorizado**: Ejecute información confidencial en la nube. Confíe en que Azure proporciona la mejor protección de datos posible, con poco o ningún cambio respecto a lo que se hace hoy.

**Cumplimiento normativo**: Migre a la nube y mantenga el control total de los datos para satisfacer las regulaciones gubernamentales para proteger la información personal y la dirección IP de la organización.

**Colaboración segura y que no es de confianza**: Afronte los problemas de escala de trabajo de todo el sector mediante la combinación de datos entre organizaciones, incluso competidores, para desbloquear amplios análisis de datos y obtener información más detallada.

**Procesamiento aislado**: Ofrezca una nueva ola de productos que eliminen la responsabilidad sobre los datos privados con un procesamiento ciego. El proveedor de servicios no puede ni siquiera recuperar los datos de usuario. 

## <a name="get-started"></a>Introducción
### <a name="azure-compute"></a>Azure Compute
Cree aplicaciones sobre las ofertas de IaaS de computación confidencial de Azure.
- Máquinas virtuales: [Serie DCsv2](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [Orquestación de contenedores confidenciales](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure Security 
Asegúrese de que las cargas de trabajo están protegidas mediante métodos de comprobación y administración de claves vinculadas al hardware. 
- Attestation: [Microsoft Azure Attestation (versión preliminar)](../attestation/overview.md)
- Administración de claves: HSM administrado

### <a name="develop"></a>Desarrollar
Comience a desarrollar y a usar aplicaciones basadas en enclaves y a implementar algoritmos confidenciales mediante el marco de inferencias confidenciales.
- Escriba aplicaciones para que se ejecuten en máquinas virtuales DCsv2: [SDK de Open Enclave](https://github.com/openenclave/openenclave)
- Modelos confidenciales de aprendizaje automático en el entorno en tiempo de ejecución de ONNX: [Inferencia confidencial (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Pasos siguientes

Implemente una máquina virtual de la serie DCsv2 e instale OE SDK en ella.

> [!div class="nextstepaction"]
> [Implementación de una máquina virtual de computación confidencial en Azure Marketplace](quick-create-marketplace.md)