---
title: Archivo de inclusión
description: archivo de inclusión
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0a2e035cdc5118cc7d952e4046e9093f95094585
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070430"
---
Para configurar la identidad administrada que permite a la aplicación IoT Central exportar datos de forma segura al centro de eventos:

1. En [Azure Portal](https://portal.azure.com), navegue hasta el recurso de la aplicación de IoT Central.

    > [!TIP]
    > De forma predeterminada, las aplicaciones de IoT Central se crean en el grupo de recursos de **IOTC** de la suscripción.

1. Seleccione **Identidad**. A continuación, en la página **Asignado por el sistema**, cambie el estado a **Activado** y, a continuación, seleccione **Guardar**.

1. Después de unos segundos, la identidad administrada asignada por el sistema de la aplicación de IoT Central está habilitada y puede seleccionar **Asignaciones de roles de Azure**:

    :::image type="content" source="media/iot-central-managed-identity/azure-role-assignments.png" alt-text="Captura de pantalla de la página de identidad de la aplicación de IoT Central en Azure Portal.":::

1. En la página **Asignaciones de roles de Azure**, seleccione **+ Agregar asignación de roles**.
