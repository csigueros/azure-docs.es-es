---
title: Diagrama de arquitectura de referencia | Azure Marketplace
description: Cómo crear un diagrama de arquitectura de referencia para una oferta en el marketplace comercial de Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/22/2021
ms.openlocfilehash: 63c820a7dd4bcbe1322478a46043963edc45dd2b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129054269"
---
# <a name="reference-architecture-diagram"></a>Diagrama de arquitectura de referencia

El diagrama de arquitectura de referencia es un modelo que representa la infraestructura en la que se basa su oferta. En el caso de las soluciones de propiedad intelectual de Azure, el diagrama también debe mostrar cómo utiliza la oferta los servicios en la nube de Microsoft según los requisitos técnicos de la venta conjunta de propiedad intelectual. No está diseñado para evaluar la calidad de la arquitectura. Está diseñado para mostrar cómo utiliza la solución los servicios de Microsoft.

El diagrama de arquitectura de referencia se puede crear con varias herramientas. Se recomienda Microsoft Visio, ya que tiene varias galerías de símbolos que describen los modelos de arquitectura de Azure.

Un punto de partida útil para crear diagramas de arquitectura de referencia es aprovechar los [modelos de arquitectura de Azure](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Componentes típicos de un diagrama de arquitectura de referencia

El diagrama debe identificar claramente la dirección IP como una solución, aplicación o código de servicio implementado en Microsoft Azure, impulsando su consumo. Este código debe ser muy reutilizable y no depender de una amplia personalización por implementación.

- Servicios en la nube que hospedan e interactúan con la oferta, incluidos los que consumen recursos de Azure.
- Conexiones de datos, capas de datos y servicios de datos que va a consumir la oferta.
- Servicios en la nube usados para controlar la seguridad, la autenticación y los usuarios de la oferta.
- Interfaces de usuario y otros servicios que exponen la oferta a los usuarios.
- Conectividad e integraciones híbridas o locales, o una combinación de ambos tipos.

Esta captura de pantalla muestra un ejemplo de un diagrama de arquitectura de referencia.

[![Esta imagen es un diagrama de arquitectura de venta conjunta de ejemplo.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Este diagrama de arquitectura de referencia de ejemplo corresponde a un bot de chat de industria vertical que se puede integrar con sitios de intranet para ayudar a predecir escenarios de demanda por medio de un algoritmo de aprendizaje automático. Esta solución utiliza datos de la cadena de suministro y de programación de fabricación de distintos sistemas de ERP. El bot está diseñado para abordar cuestiones relativas a cuándo un vendedor puede confirmar las fechas de entrega posibles de un pedido.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la venta conjunta para una oferta del marketplace comercial](./co-sell-configure.md)
