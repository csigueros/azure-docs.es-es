---
title: Introducción a la computación confidencial de Azure
description: Introducción a la computación confidencial de Azure (ACC)
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77c65a7c5418ebefd7b2414d04bec614eccdc5ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284367"
---
# <a name="what-is-confidential-computing"></a>¿Qué es la computación confidencial?

La computación confidencial permite aislar los datos confidenciales mientras se procesan en. Muchos sectores usan la computación confidencial para:

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

Azure ayuda a reducir la superficie expuesta a los ataques para conseguir una mayor protección de los datos. Azure ya ofrece muchas herramientas para proteger los [**datos en reposo**](../security/fundamentals/encryption-atrest.md) mediante modelos como el cifrado del lado cliente y el cifrado del lado servidor. Además, Azure ofrece mecanismos para cifrar los [**datos en tránsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) mediante protocolos seguros como TLS y HTTPS. En esta página se presenta un tercer tipo de cifrado de datos: el cifrado de los **datos en uso**.

## <a name="introduction-to-confidential-computing"></a>Introducción a la computación confidencial  

La computación confidencial es un término del sector que definió el [Consorcio de Computación Confidencial](https://confidentialcomputing.io/) (CCC), una fundación dedicada a definir y acelerar la adopción de la computación confidencial. El CCC define la computación confidencial como: la protección de los datos en uso mediante la realización de cálculos en un entorno de ejecución de confianza (TEE) basado en hardware.

Un TEE es un entorno que aplica la ejecución de solo código autorizado. Los datos de TEE no se pueden leer ni alterar con ningún código ajeno a ese entorno. 

### <a name="lessen-the-need-for-trust"></a>Reducción de la necesidad de confianza
La ejecución de cargas de trabajo en la nube requiere confianza. Esta confianza se concede a varios proveedores que habilitan diferentes componentes de la aplicación.


**Proveedores de software de aplicaciones**: Confíe en el software mediante la implementación en el entorno local, el uso de código abierto o la creación de software de aplicaciones interno.

**Proveedores de hardware**: Confíe en el hardware mediante el uso de hardware local o hardware interno. 

**Proveedores de infraestructura**: Confíe en sus proveedores de servicios en la nube o administre sus propios centros de datos locales.

La computación confidencial de Azure hace que sea más fácil confiar en el proveedor de nube, reduciendo la necesidad de confiar en diversos aspectos de la infraestructura en la nube de proceso. La computación confidencial de Azure minimiza la necesidad de confianza para el kernel del sistema operativo del host, el hipervisor, el administrador de la máquina virtual y el administrador del host.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre todos los productos de computación confidencial en Azure.

> [!div class="nextstepaction"]
> [Introducción a los servicios de computación confidencial de Azure](overview-azure-products.md)
