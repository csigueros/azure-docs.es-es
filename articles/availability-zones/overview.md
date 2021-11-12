---
title: Resistencia en Azure
description: Obtenga información sobre la resistencia en Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 8956555eb5393919b1e951fd13d2de17013744cc
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858471"
---
# <a name="resiliency-in-azure"></a>Resistencia en Azure

La **resistencia** es la capacidad de un sistema de recuperarse de los errores y seguir funcionando. No solo se trata de evitar errores, sino también de responder a los errores de forma que se minimicen el tiempo de inactividad o la pérdida de datos. Dado que los errores pueden producirse en varios niveles, es importante tener protección para todos los tipos en función de los requisitos de disponibilidad del servicio. La resistencia en Azure admite y avanza funcionalidades que responden a interrupciones en tiempo real para garantizar la protección continua de los datos y el servicio para aplicaciones críticas que requieren un tiempo de inactividad casi nulo y una alta confianza del cliente.

Azure incluye servicios de resistencia integrados que puede aprovechar y administrar en función de sus necesidades empresariales. Tanto si se trata de un único error de nodo de hardware, un error de nivel de bastidor, una interrupción del centro de datos o una interrupción regional a gran escala, Azure proporciona soluciones que mejoran la resistencia. Por ejemplo, los conjuntos de disponibilidad garantizan que las máquinas virtuales implementadas en Azure se distribuyan entre varios nodos de hardware aislados en un clúster. Las zonas de disponibilidad protegen las aplicaciones y los datos de los clientes frente a errores del centro de datos en varias ubicaciones físicas dentro de una región. Las **regiones** y las **zonas de disponibilidad** son fundamentales para el diseño de aplicaciones y la estrategia de resistencia, y se analizan con más detalle más adelante en este artículo.

## <a name="resiliency-requirements"></a>Requisitos de resistencia

El nivel de resistencia necesario para cualquier solución de Azure depende de varias consideraciones. El Acuerdo de Nivel de Servicio de disponibilidad y latencia y otros requisitos empresariales rigen las opciones de arquitectura y el nivel de resistencia, y deben tenerse en cuenta antes que nada. Los requisitos de disponibilidad van desde el tiempo de inactividad aceptable (y cuánto le cuesta a su empresa) hasta la cantidad de dinero y de tiempo que puede invertir de forma realista en hacer que una aplicación ofrezca alta disponibilidad.  

La creación de sistemas resistentes en Azure es una **responsabilidad compartida**. Microsoft es responsable de la confiabilidad de la plataforma en la nube, incluidos sus centros de datos y red globales. Los clientes y asociados de Azure deben responsabilizarse de la resistencia de sus aplicaciones en la nube a través de los procedimientos recomendados de arquitectura basados en los requisitos de cada carga de trabajo. Aunque Azure se esfuerza continuamente por lograr la máxima resistencia posible en el Acuerdo de Nivel de Servicio para la plataforma en la nube, debe definir sus propios Acuerdos de Nivel de Servicio de destino para cada carga de trabajo de la solución. Un Acuerdo de Nivel de Servicio permite evaluar si la arquitectura cumple con los requisitos empresariales. A medida que se esfuerza por obtener mayores porcentajes de tiempo de actividad garantizado del Acuerdo de Nivel de Servicio, aumenta el costo y la complejidad para lograr aumentar la disponibilidad. Un tiempo de actividad del 99,99 % se traduce en unos 5 minutos de tiempo de inactividad total al mes. ¿Valen la pena la complejidad y el costo adicionales para alcanzar ese porcentaje? La respuesta depende de los requisitos empresariales individuales. Al decidir los compromisos finales del Acuerdo de Nivel de Servicio, comprenda los Acuerdos de Nivel de Servicio que admite Microsoft. Cada servicio de Azure tiene su propio Acuerdo de Nivel de Servicio. 

## <a name="building-resiliency"></a>Creación de resistencia

Debe definir los requisitos de disponibilidad de la aplicación al principio del planeamiento. Muchas aplicaciones no necesitan una alta disponibilidad del 100 % y tenerlo en cuenta puede ayudar a optimizar los costos durante períodos no críticos. Identifique el tipo de errores que puede experimentar una aplicación, así como el efecto potencial de cada error. Un plan de recuperación debe cubrir todos los servicios críticos al finalizar la estrategia de recuperación en el componente individual y en el nivel general de la aplicación. Diseñe la estrategia de recuperación para protegerse frente a errores de nivel de aplicación, región y zona. Asimismo, realice pruebas del entorno de aplicación de un extremo a otro para medir la resistencia de la aplicación y la recuperación frente a errores inesperados.  

La siguiente lista de comprobación cubre el ámbito del planeamiento de resistencia. 

| **Planeamiento de resistencia** |
| --- | 
| **Defina** los objetivos de disponibilidad y recuperación para satisfacer los requisitos empresariales. | 
| **Diseñe** las características de resistencia de las aplicaciones en función de los requisitos de disponibilidad. |
| **Adapte** las plataformas de aplicaciones y de datos para que cumplan los requisitos de confiabilidad. | 
| **Configure** las rutas de conexión para promover la disponibilidad. | 
| **Use** las zonas de disponibilidad y el planeamiento de recuperación ante desastres cuando proceda para mejorar la confiabilidad y optimizar los costos. |
| **Asegúrese** de que la arquitectura de la aplicación sea resistente a los errores. | 
| **Sepa** qué ocurre si no se cumplen los requisitos del Acuerdo de Nivel de Servicio. |
| **Identifique** posibles puntos de error en el sistema; el diseño de la aplicación debe tolerar errores de dependencia mediante la implementación de la interrupción del circuito. | 
| **Compile** aplicaciones que funcionen en ausencia de sus dependencias. | 

## <a name="regions-and-availability-zones"></a>Regiones y zonas de disponibilidad

Las regiones y las zonas de disponibilidad son una parte importante de la ecuación de resistencia. Las regiones cuentan con varias zonas de disponibilidad físicamente independientes, conectadas por una red de alto rendimiento con una latencia inferior a 2 ms entre las zonas físicas para facilitar que los datos permanezcan sincronizados y accesibles cuando se produzcan problemas. Puede aprovechar esta infraestructura estratégicamente a medida que diseña una infraestructura de aplicaciones y datos que replique y entregue automáticamente servicios ininterrumpidos entre zonas y entre regiones. Elija la mejor región para sus necesidades en función de las consideraciones técnicas y normativas: funcionalidades del servicio, residencia de datos, requisitos de cumplimiento y latencia, y comience a mejorar su estrategia de resistencia.

Los servicios de Microsoft Azure admiten zonas de disponibilidad y están habilitados para impulsar las operaciones en la nube con una alta disponibilidad óptima, a la vez que respaldan las necesidades de la estrategia de continuidad empresarial y recuperación ante desastres. Elija la mejor región para sus necesidades en función de las consideraciones técnicas y normativas: funcionalidades del servicio, residencia de datos, requisitos de cumplimiento y latencia, y comience a mejorar su estrategia de resistencia. Para obtener más información, consulte [Regiones y zonas de disponibilidad de Azure](az-overview.md).

## <a name="shared-responsibility"></a>Responsabilidad compartida

La creación de sistemas resistentes en Azure es una responsabilidad compartida. Microsoft es responsable de la confiabilidad de la plataforma en la nube, incluidos sus centros de datos y red globales. Los clientes y asociados de Azure deben responsabilizarse de la resistencia de sus aplicaciones en la nube a través de los procedimientos recomendados de arquitectura basados en los requisitos de cada carga de trabajo. Consulte [Programa de administración de continuidad empresarial en Azure](business-continuity-management-program.md) para obtener más información. 

## <a name="next-steps"></a>Pasos siguientes

- [Regiones y Availability Zones en Azure](az-overview.md)
- [Servicios de Azure compatibles con las zonas de disponibilidad](az-region.md)
- [Notas del producto sobre la resistencia en Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/resilience-in-azure-whitepaper/Resilience%20in%20Azure.pdf)
- [Marco de arquitectura de Azure](https://www.aka.ms/WellArchitected/Framework)
- [Guía de arquitectura de Azure](/architecture/high-availability/building-solutions-for-high-availability)
