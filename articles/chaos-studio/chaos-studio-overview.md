---
title: ¿Qué es Azure Chaos Studio?
description: Conozca Azure Chaos Studio, un servicio de Azure que le ayuda a medir, entender y construir la resistencia de aplicaciones y servicios a los incidentes del mundo real utilizando la ingeniería del caos para insertar errores en su servicio y luego supervisar cómo responde a las interrupciones.
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-overview,ignite-fall-2021
ms.openlocfilehash: 4df530e20733e5f24db930e88bb30894293416a7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399340"
---
# <a name="what-is-azure-chaos-studio-preview"></a>¿Qué es la versión preliminar de Azure Chaos Studio?

Azure Chaos Studio es un servicio administrado para mejorar la resistencia mediante la inserción de errores en las aplicaciones de Azure. La ejecución de experimentos de inserción de errores controlados en las aplicaciones, una práctica conocida como ingeniería del caos, le ayuda a medir, comprender y mejorar la resistencia frente a incidentes reales, como interrupciones de una región o errores de la aplicación que provocan un uso elevado de la CPU en una máquina virtual.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Videos/Azure-Chaos-Studio-Curious-to-control-chaos/player]

## <a name="why-should-i-use-chaos-studio"></a>¿Por qué debo usar Chaos Studio?

Tanto si está desarrollando una nueva aplicación que se hospedará en Azure, migrando una aplicación existente a Azure o usando una aplicación que ya se ejecuta en Azure, es importante validar y mejorar la resistencia de la aplicación. La resistencia es la capacidad de un sistema de controlar las interrupciones y recuperarse de ellas. Las interrupciones en la disponibilidad de la aplicación pueden dar lugar a errores y funcionamiento anómalo para los usuarios, lo que a su vez puede tener consecuencias negativas en su negocio u objetivo empresarial.

Al ejecutar una aplicación en la nube, para evitar estas consecuencias negativas es necesario validar que la aplicación responde de forma eficaz a las interrupciones que podrían provocar un servicio del que depende, las interrupciones ocasionadas por un error en el propio servicio o incluso las interrupciones en las herramientas y los procesos de respuesta a incidentes. La experimentación de Chaos le permite probar que la aplicación hospedada en la nube es resistente a los errores.

## <a name="when-would-i-use-chaos-studio"></a>¿Cuándo se debe usar Chaos Studio?

La ingeniería del caos se puede usar en una amplia variedad de escenarios de validación de la resistencia. Por ejemplo, se podría usar en todo el ciclo de vida de desarrollo y funcionamiento del servicio, y se puede clasificar como *desplazar a la derecha*, donde el escenario se valida mejor en un entorno de producción o preproducción, o *desplazar a la izquierda*, donde el escenario se puede validar en un entorno de desarrollo o en un entorno de prueba compartido. Normalmente, los escenarios de desplazamiento a la derecha deben realizarse con tráfico de cliente real o carga simulada, mientras que los escenarios de desplazamiento a la izquierda se pueden realizar sin tráfico real del cliente. Algunos escenarios comunes en los que se puede aplicar la ingeniería de caos son:
* Reproducción de un incidente que ha afectado a la aplicación para comprender mejor el modo de error o asegurarse de que los elementos de reparación posteriores al incidente impedirán que se repita.
* Ejecución de "días de juego": carga, escala, rendimiento y validación de resistencia de un servicio como preparación para un evento o una temporada importante para los usuarios.
* Realización de simulacros de continuidad empresarial y recuperación ante desastres (BCDR) para tener la seguridad de que si la aplicación se ha visto afectada por un desastre importante, podría recuperarse rápidamente y se conservan los datos críticos.
* Ejecución de simulacros de alta disponibilidad para probar la resistencia de la aplicación frente a errores específicos, como interrupciones de la región, errores de configuración de red, eventos de gran esfuerzo o problemas de vecinos ruidosos.
* Desarrollo de pruebas comparativas de rendimiento de aplicaciones.
* Planeamiento de las necesidades de capacidad de los entornos de producción.
* Ejecución de pruebas de esfuerzo o pruebas de carga.
* Garantía de que los servicios migrados desde un entorno local u otro entorno en la nube sigan siendo resistentes a errores conocidos.
* Generación de confianza en los servicios basados en arquitecturas nativas de nube.
* Validación de que las herramientas de sitio en directo, los datos de observabilidad y los procesos disponibles funcionan según lo previsto en condiciones inesperadas.

En muchos de estos escenarios, primero se crea resistencia mediante experimentos de caos ad hoc y, luego, se valida continuamente que las nuevas implementaciones no revertirán la resistencia mediante experimentos del caos como puerta de implementación en la canalización de CI/CD.

## <a name="how-does-chaos-studio-work"></a>¿Cómo funciona Chaos Studio?

Chaos Studio permite orquestar la inserción de errores en los recursos de Azure de forma segura y controlada. La base de Chaos Studio es el experimento del caos. Un experimento del caos es un recurso de Azure que describe los errores que se deben ejecutar y los recursos en los que se deben ejecutar esos errores. Los errores se pueden organizar para ejecutarse en paralelo o secuencialmente, en función de sus necesidades. Chaos Studio admite dos tipos de errores: errores *directos del servicio*, que se ejecutan directamente en un recurso de Azure sin ninguna instalación o instrumentación (por ejemplo, reiniciar un clúster de Azure Cache for Redis o agregar latencia de red a pods de AKS) y errores *basados en agente*, que se ejecutan en máquinas virtuales o conjuntos de escalado de máquinas virtuales para ejecutar errores de invitado (por ejemplo, aplicación de presión de memoria virtual o terminación de un proceso). Cada error tiene parámetros específicos que puede controlar, como el proceso que se va a terminar o cuánta presión de memoria se va a generar.

Cuando se crea un experimento del caos, se definen uno o varios *pasos* que se ejecutan secuencialmente, cada paso uno delos cuales contiene una o varias *ramas* que se ejecutan en paralelo dentro del paso y cada rama contiene una o varias *acciones*, como insertar un error o esperar una duración determinada. Por último, se organizan los recursos (*destinos*) con los que se ejecutará cada error en grupos denominados selectores para que pueda hacer referencia fácilmente a un grupo de recursos en cada acción.

![Diagrama que muestra el diseño de un experimento del caos.](images/chaos-experiment.png)

Un experimento del caos es un recurso de Azure que reside en una suscripción y un grupo de recursos. Puede usar Azure Portal o la API REST de Chaos Studio para crear, actualizar, iniciar, cancelar y ver el estado de un experimento.

## <a name="next-steps"></a>Pasos siguientes
Empiece a crear y ejecutar experimentos del caos para mejorar la resistencia de las aplicaciones con Chaos Studio mediante los vínculos siguientes.
- [Crear y ejecutar tu primer experimento](chaos-studio-tutorial-service-direct-portal.md)
- [Más información sobre la ingeniería de caos](chaos-studio-chaos-engineering-overview.md)
