---
title: Administración del ciclo de vida de las aplicaciones en la versión preliminar de Azure Container Apps
description: Obtenga información sobre el ciclo de vida completo de las aplicaciones en la versión preliminar de Azure Container Apps.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: be6070817a8d0b419ba73788b2cde48b02d56af3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091019"
---
# <a name="application-lifecycle-management-in-azure-container-apps-preview"></a>Administración del ciclo de vida de las aplicaciones en la versión preliminar de Azure Container Apps

El ciclo de vida de las aplicaciones de Azure Container Apps gira en torno a las [revisiones](revisions.md).

Al implementar una aplicación de contenedor, se crea automáticamente la primera revisión. [Se crean más revisiones](revisions.md) a medida que cambian los [contenedores](containers.md) o se realizan ajustes en la sección `template` de la configuración.

Una aplicación de contenedor fluye por tres fases: implementación, actualización y desactivación.

## <a name="deployment"></a>Implementación

Cuando se implementa una aplicación de contenedor, se crea automáticamente la primera revisión.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deployment.png" alt-text="Azure Container Apps: fase de implementación":::

## <a name="update"></a>Actualizar

Cuando se actualiza una aplicación de contenedor con un [cambio de ámbito de revisión](revisions.md#revision-scope-changes), se crea una nueva revisión. Puede elegir si [desactivar automáticamente las nuevas revisiones antiguas o permitir que sigan estando disponibles](revisions.md).

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-update.png" alt-text="Azure Container Apps: fase de actualización":::

## <a name="deactivate"></a>Desactivación

Una vez que ya no se necesite una revisión, puede desactivarla con la opción de volver a activarla más adelante. Durante la desactivación, el contenedor se [apaga](#shutdown).

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deactivate.png" alt-text="Azure Container Apps: fase de desactivación":::

## <a name="shutdown"></a>Shutdown

Los contenedores se apagan en las situaciones siguientes:

- Cuando se reduce horizontalmente una aplicación de contenedor
- Cuando se elimina una aplicación de contenedor
- Cuando se desactiva una revisión

Cuando se inicia un apagado, el host del contenedor envía un [mensaje SIGTERM](https://wikipedia.org/wiki/Signal_(IPC)) al contenedor. El código implementado en el contenedor puede responder a este mensaje de nivel de sistema operativo para controlar la finalización.

Si la aplicación no responde al mensaje `SIGTERM`, [SIGKILL](https://wikipedia.org/wiki/Signal_(IPC)) finaliza el contenedor.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Trabajar con revisiones](revisions.md)
