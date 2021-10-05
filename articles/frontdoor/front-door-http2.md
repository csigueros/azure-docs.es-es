---
title: 'Azure Front Door: Compatibilidad con HTTP2 | Microsoft Docs'
description: Este artículo le ayuda a comprender la compatibilidad con HTTP/2 en Azure Front Door
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: dc5679036eb241abc82a57779e41e2d667238216
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601433"
---
# <a name="http2-support-in-azure-front-door"></a>Compatibilidad con HTTP/2 en Azure Front Door

Actualmente, la compatibilidad con HTTP/2 está activa para todas las configuraciones de Azure Front Door. No es necesaria ninguna otra acción por parte de los clientes.

HTTP/2 es una revisión principal de HTTP/1.1 que proporciona un rendimiento web más rápido al reducir el tiempo de respuesta. HTTP/2 mantiene los métodos HTTP, los códigos de estado y la semántica conocidos de HTTP/1.1 para mejorar la experiencia del usuario. Aunque HTTP/2 está diseñado para trabajar con HTTP y HTTPS, muchos exploradores web de cliente solo admiten HTTP/2 sobre Seguridad de la capa de transporte (TLS).

> [!NOTE]
> La compatibilidad con el protocolo HTTP/2 solo está disponible para las solicitudes de los clientes a Front Door. La comunicación entre Front Door y los servidores back-end en el grupo de back-end se produce a través de HTTP/1.1. 

### <a name="http2-benefits"></a>Ventajas de HTTP/2

Las ventajas de HTTP/2 incluyen:

*   **Multiplexación y simultaneidad**

    Con HTTP/1.1, la realización de varias solicitudes de recursos requiere varias conexiones TCP, y cada conexión conlleva su propio costo de rendimiento. HTTP/2 permite que se soliciten varios recursos en una única conexión TCP.

*   **Compresión de encabezados**

    Al comprimir los encabezados HTTP de los recursos devueltos, se envían muchos menos datos a través de la conexión.

*   **Dependencias de secuencias**

    Las dependencias de secuencia permiten al cliente indicar al servidor qué recursos tiene prioridad.


## <a name="http2-browser-support"></a>Compatibilidad de HTTP/2 con exploradores

Todos los exploradores principales han implementado la compatibilidad con HTTP/2 en sus versiones actuales. Los exploradores no compatibles conmutan automáticamente a HTTP/1.1.

|Browser|Versión mínima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre HTTP/2, consulte los siguientes recursos:

- [Página principal de especificación de HTTP/2](https://http2.github.io/)
- [Preguntas más frecuentes oficiales de HTTP/2](https://http2.github.io/faq/)
- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).
