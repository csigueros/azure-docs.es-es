---
title: Conexión de aplicaciones en Azure Container Apps (versión preliminar)
description: Aprenda a implementar varias aplicaciones que se comunican de forma conjunta en Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 75ccb6730d9fdad76f7b7f6f78d3695e86a349f0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027401"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Conexión de aplicaciones en Azure Container Apps (versión preliminar)

Azure Container Apps expone cada aplicación de contenedor mediante un nombre de dominio si la [entrada](ingress.md) está habilitada. Los puntos de conexión de entrada se pueden exponer de forma pública al mundo, o bien de manera interna y solo estar disponibles para otras aplicaciones de contenedor en el mismo [entorno](environment.md).

Una vez que conozca el nombre de dominio de una aplicación de contenedor, puede llamar a la ubicación dentro del código de la aplicación para conectar varias aplicaciones de contenedor.

> [!NOTE]
> Cuando se llama a otro contenedor en el mismo entorno mediante el FQDN, el tráfico de red nunca sale del entorno.

Puede encontrar una solución de ejemplo que muestra cómo puede llamar entre contenedores mediante la ubicación de FQDN o Dapr en [Ejemplos de Azure](https://github.com/Azure-Samples/container-apps-connect-multiple-apps).

## <a name="location"></a>Location

La ubicación de una aplicación de contenedor se compone de valores asociados a su entorno, nombre y región. Disponible mediante el dominio de nivel superior `azurecontainerapps.io`, el nombre de dominio completo (FQDN) usa:

- el nombre de la aplicación de contenedor
- el identificador único del entorno
- nombre de la región

En el diagrama siguiente se muestra cómo se usan estos valores para crear el nombre de dominio completo de una aplicación de contenedor.

:::image type="content" source="media/connect-apps/azure-container-apps-location.png" alt-text="Nombre de dominio completo de la aplicación de contenedor de Azure Container Apps.":::

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

## <a name="dapr-location"></a>Ubicación de Dapr

En el desarrollo de microservicios a menudo es necesario implementar patrones comunes a la arquitectura distribuida. Dapr permite proteger microservicios con TLS mutuo, desencadenar reintentos cuando se producen errores y aprovechar el seguimiento distribuido cuando Azure Application Insights está habilitado.

Un microservicio que usa Dapr está disponible mediante el siguiente patrón de dirección URL:

:::image type="content" source="media/connect-apps/azure-container-apps-location-dapr.png" alt-text="Ubicación de la aplicación de contenedor de Azure Container Apps con Dapr.":::

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción](get-started.md)
