---
title: Cifrado de datos en Azure Fluid Relay
description: Comprenda mejor el cifrado de datos en Fluid Relay Server.
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 8a818ea51e1d4a3f2df03ae1c4bafcf16da35b13
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261013"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Cifrado de datos en Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Microsoft Azure Fluid Relay Server aprovecha la funcionalidad de cifrado en reposo de [Azure Kubernetes](../../aks/enable-host-encryption.md), [Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest) y [Azure Blob Storage](../../storage/common/storage-service-encryption.md). La comunicación servicio a servicio entre AFRS y estos recursos está cifrada con TLS y se incluye con el límite de Azure Virtual Network, protegido contra interferencias externas mediante reglas de seguridad de red.

En el diagrama siguiente se muestra de forma general cómo se implementa Azure Fluid Relay Server y cómo gestiona el almacenamiento de los datos.

:::image type="content" source="../images/data-encryption.png" alt-text="Diagrama de almacenamiento de datos de Azure Fluid Relay":::

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>¿Cuánto aumenta el costo de Azure Fluid Relay Server si se habilita el cifrado?

El cifrado en reposo está habilitado de forma predeterminada. No hay costo adicional.

### <a name="who-manages-the-encryption-keys"></a>¿Quién administra las claves de cifrado?

Las administra Microsoft.

### <a name="how-often-are-encryption-keys-rotated"></a>¿Con qué frecuencia se alternan las claves de cifrado?

Microsoft cuenta con un conjunto de directrices internas para la rotación de claves de cifrado y Azure Fluid Relay Server las sigue. Estas directrices específicas no se han publicado. Microsoft sí que publica el [Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft](https://www.microsoft.com/sdl/default.aspx), que se considera un subconjunto de orientaciones internas e incluye procedimientos recomendados para desarrolladores de gran utilidad.

### <a name="can-i-use-my-own-encryption-keys"></a>¿Puedo usar mis propias claves de cifrado?

No, esta característica todavía no está disponible. Esté atento a más actualizaciones al respecto. 

### <a name="what-regions-have-encryption-turned-on"></a>¿Qué regiones tienen activado el cifrado?

Todas las regiones de Azure Fluid Relay Server tienen activado el cifrado en todos los datos de usuario.

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>¿Afecta el cifrado a los Acuerdos de Nivel de Servicio de rendimiento y de latencia del rendimiento?

R: No hay ningún efecto ni cambios en los Acuerdos de Nivel de Servicio de rendimiento con el cifrado en reposo habilitado.

## <a name="see-also"></a>Consulte también

- [Introducción a la arquitectura de Azure Fluid Relay](architecture.md)
- [Contrato de token de Azure Fluid Relay](../how-tos/fluid-json-web-token.md)
- [Autenticación y autorización en una aplicación](authentication-authorization.md)