---
title: Versión preliminar de los entornos de Azure Container Apps
description: Obtenga información sobre cómo se administran los entornos en Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 122b172fc9fe2a66bae742f907d910cbccbf8fbc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025805"
---
# <a name="azure-container-apps-preview-environments"></a>Entornos de la versión preliminar de Azure Container Apps

Las aplicaciones de contenedor individuales se implementan en un único entorno de Container Apps, que actúa como un límite seguro alrededor de los grupos de aplicaciones de contenedor. La instancia de Container Apps en el mismo entorno se implementa en la misma red virtual y escribe registros en la misma área de trabajo de Log Analytics.

:::image type="content" source="media/environments/azure-container-apps-environments.png" alt-text="Entornos de Azure Container Apps.":::

Las razones para implementar aplicaciones de contenedor en el mismo entorno incluyen situaciones en las que necesita:

- Administrar servicios relacionados.
- Implementar aplicaciones diferentes en la misma red virtual.
- Hacer que las aplicaciones se comuniquen entre sí mediante Dapr.
- Hacer que las aplicaciones compartan la misma configuración de Dapr.
- Hacer que las aplicaciones compartan la misma área de trabajo de Log Analytics.

Las razones para implementar aplicaciones de contenedor en distintos entornos incluyen situaciones en las que quiere asegurarse de lo siguiente:

- Que dos aplicaciones nunca comparten los mismos recursos del proceso.
- Que dos aplicaciones no se puedan comunicar entre sí a través de Dapr.

## <a name="logs"></a>Registros

Configuración relevante para el recurso de API del entorno de Azure Container Apps.

| Propiedad | Descripción |
|---|---|
| `properties.appLogsConfiguration` | Se usa para configurar el área de trabajo de Log Analytics donde se publicarán los registros de todas las aplicaciones del entorno. |
| `properties.containerAppsConfiguration.daprAIInstrumentationKey` | Clave de instrumentación de Application Insights proporcionada a Dapr para realizar el seguimiento. |

## <a name="billing"></a>Facturación

La facturación solo es relevante para aplicaciones de contenedor individuales y el uso que hagan de los recursos. No hay cargos base asociados al entorno de Container Apps.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración del comportamiento del escalado automático](scale-app.md)