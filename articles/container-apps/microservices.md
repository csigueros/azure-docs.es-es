---
title: Microservicios con Azure Container Apps (versión preliminar)
description: Información sobre cómo crear un microservicio en Azure Container Apps
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 80abae4eaaab3b047367c01d59b670b23ea490ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093609"
---
# <a name="microservices-with-azure-containers-apps-preview"></a>Microservicios con Azure Container Apps (versión preliminar)

Las [arquitecturas de microservicios](https://azure.microsoft.com/solutions/microservice-applications/#overview) permiten desarrollar, actualizar, controlar versiones y escalar áreas básicas de funcionalidad de forma independiente en un sistema general. Azure Container Apps proporciona la base para implementar microservicios con las siguientes características:

- [Escalado](scale-app.md), [control de versiones](application-lifecycle-management.md) y [actualizaciones](application-lifecycle-management.md) independientes
- [Detección de servicios](connect-apps.md)
- [Integración de Dapr](microservices-dapr.md) nativa

:::image type="content" source="media/microservices/azure-container-services-microservices.png" alt-text="Las aplicaciones de contenedor se implementan como microservicios":::

Un [entorno](environment.md) de Container Apps proporciona un límite seguro alrededor de un grupo de aplicaciones de contenedor. Normalmente, una aplicación de contenedor única representa un microservicio, que se compone de pods formados por uno o varios [contenedores](containers.md).

## <a name="dapr-integration"></a>Integración de Dapr

Al implementar un sistema compuesto de microservicios, las llamadas de función se reparten por la red. Para dar cabida a la naturaleza distribuida de los microservicios, debe tener en cuenta los errores, reintentos y tiempos de espera. Aunque Container Apps incluye los bloques de creación para ejecutar microservicios, el uso de [Dapr](https://docs.dapr.io/concepts/overview/) proporciona un modelo de programación de microservicios aún más completo. Dapr incluye características como la observabilidad, la publicación/suscripción y la invocación de servicio a servicio con TLS mutuo, reintentos, etc.

Para obtener más información sobre el uso de Dapr, vea [Creación de microservicios con Dapr](microservices-dapr.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Escalado](scale-app.md)
