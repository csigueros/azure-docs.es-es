---
title: archivo de inclusión
description: archivo de inclusión
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: fc2dd8938f627be669519b843a97b87ddf1e3203
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287895"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para crear una directiva de acceso compartido que conceda los permisos **Conexión del servicio** y **Lectura del Registro**, y obtenga una cadena de conexión para esta directiva, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), seleccione **Grupos de recursos**. Seleccione el grupo de recursos donde se encuentra el centro y, a continuación, seleccione el centro en la lista de recursos.

1. En el panel de la izquierda del centro, seleccione **Directivas de acceso compartido**.

1. En el menú superior situado encima de la lista de directivas, seleccione **Agregar**.

1. En **Agregar una directiva de acceso compartida**, escriba un nombre descriptivo para la directiva, por ejemplo *serviceAndRegistryRead*. En **Permisos**, seleccione **Lectura del Registro** y **Conexión del servicio**, y después seleccione **Agregar**.

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png" alt-text="Captura de pantalla que muestra cómo agregar una nueva directiva de acceso compartido" border="true":::

1. Seleccione la directiva nueva en la lista de directivas.

1. Seleccione el icono de copia para **Cadena de conexión principal** y guarde el valor.

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png" alt-text="Captura de pantalla que muestra cómo recuperar la cadena de conexión" border="true":::

Para obtener más información sobre las directivas de acceso compartido y los permisos de IoT Hub, consulte [Permisos y control del acceso](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
