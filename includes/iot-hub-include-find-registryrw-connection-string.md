---
title: archivo de inclusión
description: archivo de inclusión
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: ce13f1cdd3effdd08bfa76a996587edc213c5701
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287370"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Para obtener la cadena de conexión de IoT Hub para la directiva **registryReadWrite**, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), seleccione **Grupos de recursos**. Seleccione el grupo de recursos donde se encuentra el centro y, a continuación, seleccione el centro en la lista de recursos.

2. En el panel de la izquierda del centro, seleccione **Directivas de acceso compartido**.

3. En la lista de directivas, seleccione la directiva **registryReadWrite**.

4. En **Claves de acceso compartido**, seleccione el icono de copia de **Cadena de conexión principal** y guarde el valor.

    :::image type="content" source="./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png" alt-text="Captura de pantalla que muestra cómo recuperar la cadena de conexión" border="true":::

Para obtener más información sobre las directivas de acceso compartido y los permisos de IoT Hub, consulte [Permisos y control del acceso](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
