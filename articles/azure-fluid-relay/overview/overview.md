---
title: Introducción a Azure Fluid Relay
description: Introducción a Fluid Framework y Azure Fluid Relay.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 08/19/2021
ms.topic: how-to
ms.service: azure-fluid
ms.openlocfilehash: 30c0e289951555a5ffcd1d0776e0b273f8573da7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661695"
---
# <a name="azure-fluid-relay-overview"></a>Introducción a Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

[Fluid Framework](https://fluidframework.com/) es un marco de código abierto independiente de la plataforma. [Azure Fluid Relay](../overview/overview.md) es una oferta administrada para Fluid Framework que permite a los desarrolladores crear experiencias de colaboración en tiempo real y replicar el estado entre clientes de JavaScript conectados en tiempo real.

## <a name="what-is-the-fluid-framework"></a>¿Qué es Fluid Framework?

Fluid Framework es una colección de bibliotecas cliente para distribuir y sincronizar el estado compartido. Estas bibliotecas permiten que varios clientes creen estructuras de datos compartidas y operen en ellas de manera simultánea mediante patrones de codificación similares a los que se usan para trabajar con datos locales.

Puede encontrar más información al respecto en [FluidFramework.com](https://fluidframework.com).

## <a name="why-fluid"></a>¿Por qué Fluid?

Porque es difícil garantizar experiencias colaborativas de baja latencia.

Fluid Framework ofrece:

- Un modelo de aplicación centrado en el cliente con persistencia de datos que no requiere código de servidor personalizado.
- Estructuras de datos distribuidas con modelos de programación conocidos.
- Latencia muy baja.

Los desarrolladores de Microsoft integraron la colaboración en varias aplicaciones, pero muchas requerían una lógica del lado servidor específica de la aplicación para administrar la experiencia colaborativa. Fluid Framework es el resultado de la inversión de Microsoft en reducir la complejidad de la creación de aplicaciones colaborativas.

¿Qué pasaría si no tuviera que invertir en código de servidor en absoluto? Imagine poder utilizar un servidor de uso general diseñado para ser ligero y de bajo costo. Imagine centrar todo el desarrollo en la experiencia del cliente y que la sincronización de datos la administraran por usted. Esa es la promesa de Fluid.

## <a name="focused-on-the-client-developer"></a>Centrado en el desarrollador de cliente

Las aplicaciones creadas con Fluid Framework no requieren ningún código personalizado en el servidor a fin de habilitar escenarios sofisticados de sincronización de datos, como escribir en tiempo real en distintos editores de texto. Los desarrolladores de cliente pueden centrarse en las experiencias del cliente, a la vez que permiten que Fluid se encargue del trabajo de mantener los datos sincronizados.

Fluid Framework funciona con el marco de trabajo de la aplicación que prefiera. Ya sea que prefiera JavaScript o un marco como React, Angular o Vue, Fluid Framework permite que la creación de experiencias de colaboración sea algo sencillo y flexible.

## <a name="how-fluid-works"></a>Funcionamiento de Fluid

Fluid está diseñado para ofrecer experiencias de colaboración con un rendimiento excepcional. A fin de lograr este objetivo, el equipo mantiene la lógica del servidor lo más simple y ligera posible. Este enfoque ha ayudado a garantizar la sincronización prácticamente instantánea entre clientes con costos de servidor bajos.

Para que el servidor se mantenga sencillo, cada cliente de Fluid es responsable de su propio estado. Si bien los sistemas anteriores mantienen una fuente confiable en el servidor, el servicio Fluid se encarga de realizar las operaciones de datos, secuenciarlas y devolverlas, secuenciadas, a los clientes. Cada cliente puede usar esa secuencia para generar de manera independiente y precisa el estado actual, sin importar del orden en que recibe las operaciones.

Los pasos siguientes son un flujo típico.

1. El código de cliente cambia los datos de manera local.
1. El runtime de Fluid envía ese cambio al servicio Fluid.
1. El servicio Fluid secuencia esa operación y la difunde a todos los clientes.
1. El runtime de Fluid incorpora esa operación en los datos locales y genera un evento "valueChanged".
1. El código de cliente controla ese evento (actualiza la vista, ejecuta la lógica de negocios).

## <a name="getting-to-version-10"></a>Obtención de la versión 1.0

La tecnología principal que impulsa Fluid Framework es madura y estable; sin embargo, las capas creadas sobre esa base siguen siendo un trabajo en curso. En los próximos meses, evolucionaremos las API, agregaremos características nuevas y trabajaremos para simplificar aún más el uso del marco. Estos cambios los impulsa el uso de Fluid por parte de Microsoft de manera interna, demás de los requisitos que estamos recopilando de los desarrolladores que actualmente usan Fluid.

Fluid Framework todavía no está listo para impulsar soluciones de calidad para los entornos de producción, pero nos entusiasma abrir el código ahora para ofrecer a los desarrolladores una oportunidad para explorar, aprender y colaborar tanto a través de comentarios como de la participación directa.
