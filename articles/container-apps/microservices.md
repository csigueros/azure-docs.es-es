---
title: Microservicios con Azure Container Apps (versión preliminar)
description: Información sobre cómo crear un microservicio en Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39d095f28c9f642815e5463975b9836e228d9c3a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028750"
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
