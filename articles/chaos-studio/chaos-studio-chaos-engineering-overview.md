---
title: Descripción de la ingeniería y resistencia del caos con Azure Chaos Studio
description: Comprenda los conceptos de ingeniería y resistencia del caos.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 75a225031986b595f3e3d83b729b73588726a465
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090993"
---
# <a name="understanding-chaos-engineering-and-resilience"></a>Descripción de la ingeniería y resistencia del caos

Antes de empezar a usar Chaos Studio, es útil que comprenda los conceptos básicos de ingeniería de confiabilidad de sitios que se aplican.

## <a name="what-is-resilience"></a>¿Qué es la resistencia?

La creación de aplicaciones distribuidas a gran escala nunca ha sido tan fácil. La infraestructura se hospeda en la nube, la compatibilidad con lenguajes de programación es diversa y hay una gran cantidad de componentes y servicios hospedados y de código abierto sobre los que construir. Desafortunadamente, no hay ninguna garantía de confiabilidad para estos componentes y dependencias subyacentes, ni para los sistemas basados en ellos. La infraestructura puede desconectarse y se pueden producir interrupciones del servicio en cualquier momento. Las interrupciones menores de un área se pueden ampliar y tienen efectos secundarios de larga duración en otra. 

Las aplicaciones y los servicios deben planear y dar cabida a interrupciones del servicio, interrupciones en dependencias conocidas y desconocidas, carga inesperada repentina y latencias en todo el sistema. Las aplicaciones y los servicios deben diseñarse para controlar los errores y protegerse contra las interrupciones. 

Las aplicaciones y servicios que gestionan correctamente los problemas son **resistentes**. La confiabilidad de componentes individuales es buena, pero la **resistencia es una propiedad de todo el sistema**. La resistencia del sistema de un extremo a otro debe validarse en un entorno integrado, de tipo producción, con las condiciones y la carga a las que se enfrentará en producción.

## <a name="what-are-chaos-engineering-and-fault-injection"></a>¿Qué son la ingeniería del caos y la inyección de errores?

La **ingeniería del caos** es la práctica de someter aplicaciones y servicios a las tensiones y errores del mundo real para compilar y validar la resiliencia a condiciones inestables y a la falta de dependencias. La **inyección de errores** es el acto de introducir un error en un sistema. Se pueden usar distintos errores, como la latencia de red o la pérdida de acceso al almacenamiento, para atacar a los componentes del sistema, lo que provoca escenarios que una aplicación o servicio debe ser capaz de controlar o recuperarse de ellos. Un experimento de caos es la aplicación de errores de manera individual, en paralelo o secuencialmente en uno o varios recursos de suscripción o dependencias, con el objetivo de supervisar el comportamiento y el estado del sistema y actuar ante cualquier problema que surja. Un experimento puede representar un escenario real, como una interrupción de energía del centro de datos o una latencia de red en un servidor DNS. También se puede usar para simular condiciones perimetrales que se producen con las compras de Black Friday o cuando las entradas de un concierto de una banda conocida salen a la venta.
