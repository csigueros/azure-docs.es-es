---
title: Terminología de resistencia de Azure
description: Descripción de los términos
author: mamccrea
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: mamccrea
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: ade8db49435064c31ec3c1262f359069641a11dd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057012"
---
# <a name="terminology"></a>Terminología

Para conocer mejor las regiones y las zonas de disponibilidad en Azure, le resultará más fácil comprender los términos o conceptos clave.

| Término o concepto | Descripción |
| --- | --- |
| region | Un conjunto de centros de datos implementados dentro de un perímetro que define la latencia y conectados a través de una red regional dedicada de baja latencia. |
| geography | Un área del mundo que contiene al menos una región de Azure. Las zonas geográficas definen un mercado discreto que conserva los límites de residencia de datos y cumplimiento. Las zonas geográficas permiten a los clientes con necesidades específicas de residencia de datos y cumplimiento normativo mantener sus datos y aplicaciones cerca. Las zonas geográficas toleran los errores hasta el punto de resistir una interrupción total del funcionamiento de una región gracias a su conexión con nuestra infraestructura de red dedicada de alta capacidad. |
| zona de disponibilidad | Ubicaciones físicas exclusivas dentro de una región. Cada zona de disponibilidad consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. |
| Región recomendada | Una región que proporciona la gama más amplia de capacidades de servicio que está diseñada para admitir las zonas de disponibilidad ahora o en el futuro. Estas regiones se designan en Azure Portal como **Recomendada**. |
| Región alternativa (otra) | Una región que extiende la superficie de Azure dentro de un límite de residencia de datos donde también existe una región recomendada. Las regiones alternativas ayudan a optimizar la latencia y proporcionan una segunda región para las necesidades de recuperación ante desastres. No están diseñadas para admitir zonas de disponibilidad (aunque Azure realiza una evaluación periódica de estas regiones para determinar si deben convertirse en regiones recomendadas). Estas regiones se designan en Azure Portal como **Otras**. |
| replicación entre regiones (anteriormente región emparejada) | Estrategia de confiabilidad e implementación que combina la alta disponibilidad de las zonas de disponibilidad con la protección frente a incidentes en toda la región, para satisfacer las necesidades de recuperación ante desastres y continuidad empresarial. |
| Servicio fundamental | Servicio básico de Azure que está disponible en todas las regiones cuando la región está disponible con carácter general. |
| Servicio estándar | Servicio de Azure que está disponible en todas las regiones recomendadas en un plazo de 90 días a partir de la disponibilidad general de la región o de la disponibilidad basada en la demanda en regiones alternativas. |
| servicio estratégico | Servicio de Azure que ofrece una disponibilidad basada en la demanda entre regiones respaldadas por hardware personalizado o especializado. |
| Servicio regional | Servicio de Azure que se implementa de forma regional y permite al cliente especificar la región en la que se implementará el servicio. Para obtener una lista completa, vea [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Servicio no regional | Servicio de Azure para el que no hay ninguna dependencia en una región específica de Azure. Los servicios no regionales se implementan en dos o más regiones. Si hay un error regional, la instancia del servicio en otra región sigue atendiendo a los clientes. Para obtener una lista completa, vea [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| servicio de zona | Servicio de Azure que admite zonas de disponibilidad y que permite que un recurso se implemente en una zona de disponibilidad específica y seleccionada automáticamente, para lograr requisitos de latencia o rendimiento más estrictos. |
| servicio con redundancia de zona | Servicio de Azure que admite zonas de disponibilidad y que permite que los recursos se repliquen o distribuyan entre zonas automáticamente. |
| servicio siempre disponible | Servicio de Azure que admite zonas de disponibilidad y que permite que los recursos estén siempre disponibles en todas las zonas geográficas de Azure; estos son resistentes a interrupciones en toda la zona y en toda la región. |
