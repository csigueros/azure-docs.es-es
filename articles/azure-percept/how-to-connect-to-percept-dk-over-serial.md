---
title: Conexión serie a Azure Percept DK
description: Obtenga información acerca de cómo configurar una conexión serie a Azure Percept DK con un cable serie de USB a TTL.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: a9b864f5d8341253c70fbde6d111b04a4d924f1d
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371483"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Conexión serie a Azure Percept DK

Siga los pasos que se indican a continuación para configurar una conexión serie a Azure Percept DK mediante [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> **Si tiene un kit de desarrollo en versión preliminar privada**, **NO** se recomienda conectar el kit de desarrollo a través de serie, excepto en casos de errores extremos (por ejemplo, si el dispositivo está inutilizable). La conexión a través de serie requiere desensamblar el kit de desarrollo en versión preliminar privada para acceder a los conectores GPIO. Retirar el alojamiento de la placa base resulta muy difícil y podría romper los cables de la antena de Wi-Fi.

## <a name="prerequisites"></a>Requisitos previos

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- PC host
- Azure Percept DK
- [Cable serie USB a TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Cable serie USB a TTL.":::

## <a name="start-the-serial-connection"></a>Inicio de la conexión serie

1. Conecte el [cable serie USB a TTL](https://www.adafruit.com/product/954) a los tres conectores GPIO de la placa base, como se muestra a continuación.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/apdk-serial-pins.jpg" alt-text="Conexiones de conectores serie de la placa base.":::

1. Encienda el kit de desarrollo y conecte el lado USB del cable serie a su equipo.

1. En Windows, vaya a **Inicio** -> **Configuración de Windows Update** -> **Ver actualizaciones opcionales** -> **Actualizaciones de controladores**. Busque una actualización de serie a USB en la lista, active la casilla situada junto a ella y seleccione **Descargar e instalar**.  

1. A continuación, abra el administrador de dispositivos de Windows (**Inicio** -> **Administrador de dispositivos**). Vaya a **Puertos** y seleccione **USB a UART** para abrir las **Propiedades**. Tenga en cuenta a qué puerto COM está conectado el dispositivo.

1. Seleccione la pestaña **Configuración de puerto**. Asegúrese de que **Bits por segundo** esté establecido en 115 200.

1. Abra PuTTY. Escriba lo siguiente y seleccione **Abrir** para conectarse al dispositivo DevKit mediante una conexión serie:

    1. Línea serie: COM[n.º de puerto]
    1. Velocidad: 115200
    1. Tipo de conexión: Serie

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Ventana de sesión de PuTTY con parámetros de serie seleccionados.":::
