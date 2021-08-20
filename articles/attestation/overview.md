---
title: Introducción a Azure Attestation
description: Información general sobre Microsoft Azure Attestation, una solución para la atestación de entornos de ejecución de confianza (TEE)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: fe025fad4955095ef16b546b7d326d80b4aea15c
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112427579"
---
# <a name="microsoft-azure-attestation"></a>Microsoft Azure Attestation 

Microsoft Azure Attestation es una solución unificada para comprobar de forma remota la confiabilidad de una plataforma y la integridad de los archivos binarios que se ejecutan en ella. El servicio admite la atestación de las plataformas respaldadas por módulos de plataforma segura (TPM), junto con la posibilidad de atestiguar el estado de los entornos de ejecución de confianza (TEE), como los enclaves [Software Guard Extensions de Intel®](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) y de [seguridad basada en virtualización](/windows-hardware/design/device-experiences/oem-vbs) (VBS). 

La atestación es un proceso que se utiliza para demostrar que se ha creado correctamente una instancia de los archivos binarios de software en una plataforma de confianza. Así, los usuarios de confianza remotos pueden tener la seguridad de que solo se ejecuta el software previsto en el hardware de confianza. Azure Attestation consta de un servicio y un marco de trabajo unificados orientados al cliente para la atestación.

Azure Attestation utiliza paradigmas de seguridad de vanguardia, como la [computación confidencial en Azure](../confidential-computing/overview.md) y la protección de inteligencia perimetral. Los clientes llevan tiempo solicitando la posibilidad de comprobar de forma independiente la ubicación de una máquina, la posición de una máquina virtual en esa máquina y el entorno en el que se ejecutan los enclaves en esa máquina virtual. Con Azure Attestation se podrán satisfacer estas y muchas otras solicitudes de los clientes.

Azure Attestation recibe la evidencia de entidades de proceso, las convierte en un conjunto de notificaciones, las valida con respecto a directivas configurables y genera pruebas criptográficas para aplicaciones basadas en notificaciones (por ejemplo, usuarios de confianza y autoridades de auditoría).

## <a name="use-cases"></a>Casos de uso

Azure Attestation proporciona completos servicios de atestación para varios entornos y diferentes casos de uso.

### <a name="sgx-attestation"></a>Atestación de SGX

SGX hace referencia al aislamiento de nivel de hardware, que se admite en ciertos modelos de CPU de Intel. SGX permite que el código se ejecute en compartimientos saneados conocidos como enclaves SGX. Después, el hardware administra los permisos de acceso y memoria para garantizar una superficie mínima de ataque con el aislamiento adecuado.

Las aplicaciones cliente se pueden diseñar para aprovechar las ventajas de los enclaves SGX y delegar tareas que afectan a la seguridad de modo que se lleven a cabo dentro de esos enclaves. Estas aplicaciones pueden usar Azure Attestation para establecer de forma rutinaria la confianza en el enclave y su capacidad para acceder a datos confidenciales.

Los procesadores escalables Intel® Xeon® solo admiten [soluciones de atestación basadas en ECDSA](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html#Elliptic%20Curve%20Digital%20Signature%20Algorithm%20(ECDSA)%20Attestation) para la atestación remota de enclaves SGX. Mediante el modelo de atestación basado en ECDSA, Azure Attestation admite la validación de plataformas de servidor basadas en los procesadores escalables Intel® Xeon® E3 e Intel® Xeon®. 

> [!NOTE]
> Para realizar la atestación de plataformas de servidor basadas en los procesadores escalables Intel® Xeon® mediante Azure Attestation, se espera que los usuarios instalen la [versión 1.10.0 de Azure DCAP](https://github.com/microsoft/Azure-DCAP-Client) o posterior.

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) es una colección de bibliotecas destinadas a crear una única abstracción unificada de enclave para que los desarrolladores creen aplicaciones basadas en TEE. Ofrece un modelo de aplicación segura universal que reduce las características específicas de cada plataforma. Microsoft considera que esta colección es un paso esencial para democratizar las tecnologías de enclave basadas en hardware, como SGX, y aumentar su aceptación en Azure.

OE normaliza los requisitos específicos para la comprobación de una evidencia de enclave. Esto hace de OE un consumidor de atestación de Azure Attestation con gran capacidad de ajuste.

### <a name="tpm-attestation"></a>Atestación de TPM 

La atestación basada en el Módulo de plataforma segura (TPM) es fundamental para proporcionar una prueba del estado de las plataformas. TPM actúa como raíz de confianza y coprocesador de seguridad para proporcionar validez criptográfica de las medidas (evidencia). Los dispositivos con un TPM pueden confiar en la atestación para comprobar que la integridad del arranque no se ve comprometida, junto con el uso de las notificaciones para detectar la habilitación de los estados de características durante el arranque. 

Las aplicaciones cliente se pueden diseñar para aprovechar las ventajas de la atestación de TPM mediante la delegación de tareas que afectan a la seguridad para que solo tengan lugar después de que se haya validado una plataforma como segura. Estas aplicaciones pueden usar Azure Attestation para establecer de forma rutinaria la confianza en la plataforma y su capacidad para acceder a datos confidenciales.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation se puede ejecutar en un TEE

Azure Attestation es fundamental en los escenarios de computación confidencial, ya que realiza las acciones siguientes:

- Comprueba si la evidencia del enclave es válida.
- Evalúa la evidencia del enclave con respecto a una directiva definida por el cliente.
- Administra y almacena directivas específicas del inquilino.
- Genera y firma un token que emplean los usuarios de confianza para interactuar con el enclave.

Azure Attestation se ha creado para ejecutarse en dos tipos de entornos:
- Azure Attestation se ejecuta en un entorno de ejecución de confianza habilitado para SGX.
- Azure Attestation se ejecuta en un entorno que no es de ejecución de confianza.

Los clientes de Azure Attestation han pedido que Microsoft esté de forma operativa fuera de una base informática de confianza. Con esto se pretende evitar que las entidades de Microsoft, como administradores de máquinas virtuales, administradores de hosts y desarrolladores de Microsoft, modifiquen las solicitudes de atestación, las directivas y los tokens emitidos por Azure Attestation. Azure Attestation también se ha creado para ejecutarse en un TEE, en el que las características de Azure Attestation, como la validación de ofertas, la generación de tokens y la firma de tokens, se mueven a un enclave SGX.

## <a name="why-use-azure-attestation"></a>Razones para usar Azure Attestation

Azure Attestation es la opción preferida para la atestación de los entornos de ejecución de confianza, ya que ofrece las siguientes ventajas: 

- Ofrece un marco unificado para la atestación de varios entornos, como los TPM y los enclaves SGX y VBS. 
- Permite la creación de proveedores de atestación personalizados y la configuración de directivas para restringir la generación de tokens
- Ofrece proveedores regionales compartidos que pueden hacer comprobaciones sin que los usuarios tengan que configurar nada.
- Protege sus datos mientras está en uso con la implementación en un enclave SGX.
- Servicio de alta disponibilidad 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Compatibilidad con la continuidad empresarial y recuperación ante desastres (BCDR)

La [continuidad empresarial y recuperación ante desastres ](../best-practices-availability-paired-regions.md) (BCDR) para Azure Attestation permiten reducir las interrupciones del servicio que se producen como consecuencia de desastres o problemas de disponibilidad significativos en una región.

En circunstancias normales, los clústeres implementados en dos regiones funcionarán de forma independiente. Si se produce un error o una interrupción en una región, sucederá lo siguiente:

- La BCDR de Azure Attestation proporcionará una conmutación por error fluida, en la que los clientes no tendrán que realizar ningún paso adicional para la recuperación.
- El servicio de [Azure Traffic Manager](../traffic-manager/index.yml) para la región detectará que el sondeo de estado está degradado y cambiará el punto de conexión a la región emparejada.
- Las conexiones existentes no funcionarán y recibirán problemas de tiempo de espera o errores internos del servidor.
- Se bloquearán todas las operaciones del plano de control. Los clientes no podrán crear proveedores de atestación en la región primaria.
- Todas las operaciones de plano de datos, incluidas las llamadas de atestación y la configuración de directiva, se atenderán en la región secundaria. Los clientes pueden seguir trabajando en las operaciones de plano de datos con el URI original correspondiente a la región primaria.

## <a name="next-steps"></a>Pasos siguientes
- Más información acerca de los [conceptos básicos de Azure Attestation](basic-concepts.md)
- [Creación y firma de una directiva de atestación](author-sign-policy.md)
- [Configuración de Azure Attestation con PowerShell](quickstart-powershell.md)
