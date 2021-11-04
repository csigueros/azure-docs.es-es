---
title: Introducción a la versión preliminar de Azure Container Apps
description: Información sobre los escenarios y usos comunes de Azure Container Apps
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: overview
ms.date: 10/19/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b7f0cfb02b5aa2b7b37efc306213915a855a7bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093604"
---
# <a name="azure-container-apps-preview-overview"></a>Introducción a la versión preliminar de Azure Container Apps

Azure Container Apps permite ejecutar microservicios y aplicaciones contenedorizadas en una plataforma sin servidor. Entre los usos comunes de Azure Container Apps se incluyen:

- Implementación de puntos de conexión de API
- Hospedaje de aplicaciones de procesamiento en segundo plano
- Control del procesamiento controlado por eventos
- Ejecución de microservicios

Las aplicaciones creadas en Azure Container Apps se pueden escalar dinámicamente en función de las siguientes características:

- Tráfico HTTP
- Procesamiento controlado por eventos
- Carga de CPU o de memoria
- Cualquier [escalador compatible con KEDA](https://keda.sh/docs/scalers/)

:::image type="content" source="media/overview/azure-container-apps-example-scenarios.png" alt-text="Escenarios de ejemplo para Azure Container Apps.":::

Azure Container Apps permite ejecutar código de aplicación empaquetado en cualquier contenedor y no tiene en cuenta el entorno de ejecución o el modelo de programación. Con Container Apps, puede disfrutar de las ventajas de ejecutar contenedores y, al mismo tiempo, olvidarse de administrar la infraestructura en la nube y orquestadores de contenedores complejos.

Con Azure Container Apps, puede:

- [Ejecutar varias revisiones del contenedor](application-lifecycle-management.md) y administrar el ciclo de vida de la aplicación del contenedor.

- [Escalar automáticamente](scale-app.md) las aplicaciones en función de cualquier desencadenador de escalado compatible con KEDA. La mayoría de las aplicaciones se pueden escalar a cero <sup>1</sup>.

- [Habilitar la entrada HTTPS](ingress.md) sin tener que administrar otra infraestructura de Azure.

- [Dividir el tráfico](revisions.md) entre varias versiones de una aplicación para las implementaciones azul-verde y los escenarios de prueba A-B.

- [Usar la entrada interna y la detección de servicios](connect-apps.md) para proteger los puntos de conexión solo internos con la detección de servicios basada en DNS integrada.

- [Crear microservicios con Dapr](microservices.md) y acceder a su amplio conjunto de API.

- [Ejecutar contenedores desde cualquier registro](containers.md), público o privado, incluidos Docker Hub y Azure Container Registry (ACR).

- [Usar la extensión CLI de Azure o plantillas de ARM](get-started.md) para administrar las aplicaciones.

- [Administrar secretos de forma segura](secure-app.md) directamente en una aplicación.

- [Ver los registros de aplicaciones](monitor.md) mediante Azure Log Analytics.

<sup>1</sup> Las aplicaciones que se [escalan en la carga de CPU o memoria](scale-app.md) no se pueden escalar a cero.

### <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de la primera aplicación de contenedor](get-started.md)
