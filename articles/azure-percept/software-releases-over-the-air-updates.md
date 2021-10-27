---
title: Versiones de software para actualizaciones OTA de Azure Percept DK
description: Vínculos de información y descarga de los paquetes de actualización de forma inalámbrica de Azure Percept DK
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: 1eb6f41d738f7ab5a2dbd68fab341c2b7c9917ad
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001170"
---
# <a name="software-releases-for-ota-updates"></a>Versiones de software para actualizaciones de OTA

La actualización OTA se ha creado para los usuarios que tienden a mantener siempre actualizado el kit de desarrollo. Este es el motivo por el que aquí solo se proporcionan las versiones de detención inmediata y la versión más reciente. Para cambiar el kit de desarrollo a una versión específica (anterior), use la actualización mediante el cable USB. Consulte [Actualización de Azure Percept DK por medio de una conexión por cable USB-C](./how-to-update-via-usb.md). Use también la actualización mediante USB si desea pasar a una versión muy avanzada.

>[!CAUTION]
>El kit de desarrollo no admite el cambio a una versión de software anterior con OTA. El marco Device Update for IoT Hub no impedirá la implementación de una imagen con una versión anterior a la actual. Pero si lo hace en el kit de desarrollo, se pierden los datos y la funcionalidad.

>[!IMPORTANT]
>Asegúrese de consultar el documento siguiente antes de decidirse a usar la actualización de tipo OTA o cable USB.
>
>[Procedimiento para determinar una estrategia de actualización](./how-to-determine-your-update-strategy.md)

## <a name="hard-stop-version-of-ota"></a>Versión de detención inmediata de OTA

Microsoft prestará servicio a todas las versiones del kit de desarrollo con paquetes OTA. Sin embargo, dado que existen cambios importantes para el firmware, el sistema operativo del kit de desarrollo o la plataforma OTA, el cambio de OTA directamente de una versión anterior a una versión muy avanzada puede ser problemático. Por lo general, si se produce un cambio importante, Microsoft se asegurará de que el proceso de actualización OTA realiza la transición del sistema antiguo sin problemas a la **primera versión que introduce o entrega este cambio importante**. Esta versión específica se convierte en una versión de detención inmediata de OTA. Tome como ejemplo una versión de detención inmediata conocida: la **versión de junio**. OTA funcionará si un usuario actualiza el kit de desarrollo de la versión 2104 a la 2106 y, luego, de la versión 2106 a la 2107. Sin embargo, no funcionará si un usuario intenta omitir la versión de detención inmediata (2106) y actualizar el kit de desarrollo de la versión 2104 directamente a la 2107.

:::image type="content" source="./media/azure-percept-devkit-software-releases-ota-update/hard-stop-illustrate.png" alt-text="Versión de detención inmediata de OTA":::

## <a name="recommendations-for-applying-the-ota-update"></a>Recomendaciones para aplicar la actualización de OTA

**Escenario 1:** actualización frecuente (mensual) del kit de desarrollo para asegurarse de que siempre está actualizado.

- No debería haber ningún problema si siempre usa OTA para actualizar el kit de desarrollo de la última versión a la versión más reciente.

**Escenario 2:** actualización con la posible omisión de algunas versiones.

1. Identifique la versión de software actual del kit de desarrollo.
1. Revise la lista de versiones del paquete OTA para buscar cualquier versión de detención inmediata entre la versión actual y la versión de destino.
    - Si encuentra alguna, debe implementar secuencialmente las versiones de detención inmediata hasta que pueda implementar el paquete de actualización más reciente.
    - Si no hay ninguna, puede implementar directamente el paquete OTA más reciente en el kit de desarrollo.

## <a name="identify-the-current-software-version-of-dev-kit"></a>Identificación de la versión de software actual del kit de desarrollo

**Opción 1:**

1. Inicie sesión en [Azure Percept Studio](./overview-azure-percept-studio.md).
1. En **Dispositivos**, elija el dispositivo del kit de desarrollo.
1. En la pestaña **General**, busque la información del **modelo** y la **versión de SW**.

**Opción 2:**

1. Consulte el **Dispositivo IoT Edge** del servicio **IoT Hub** en Microsoft Azure Portal.
1. Elija el dispositivo del kit de desarrollo de la lista de dispositivos.
1. Seleccione **Device twin** (Dispositivo gemelo).
1. Desplácese por las propiedades del dispositivo gemelo, busque los elementos **"model"** (modelo) y **"swVersion"** (versión de software) en **"deviceInformation"** (información del dispositivo) y anote sus valores.

## <a name="identify-the-ota-packages-to-be-deployed"></a>Identificación de los paquetes OTA que se van a implementar

>[!IMPORTANT]
>Si la versión actual del kit de desarrollo no se incluye en ninguna de las versiones siguientes, significa que no se admite para la actualización OTA. Realice una actualización por cable USB para llegar a la versión más reciente.

**Versión más reciente:**

|Release|Versiones a las que se aplica|Vínculos de descarga|Nota|
|---|---|---|---|
|Versión del servicio de septiembre (2109)|2021.106.111.115,<br>2021.107.129.116|[Paquete de actualización OTA 2021.109.129.108](https://go.microsoft.com/fwlink/?linkid=2174634)||

**Versiones de detención inmediata:**

|Release|Versiones a las que se aplica|Vínculos de descarga|Nota|
|---|---|---|---|
|Versión del servicio de junio (2106)|2021.102.108.112, 2021.104.110.103, 2021.105.111.122 |[Manifiesto OTA 2021.106.111.115 (para PE-101)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_PE-101_2021.106.111.115_v3.json)<br>[Manifiesto OTA 2021.106.111.115 (para APDK-101)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_Azure-Percept-DK_2021.106.111.115_v3.json) <br>[Paquete de actualización OTA 2021.106.111.115](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/Microsoft-Azure-Percept-DK-2021.106.111.115.swu) |Asegúrese de usar el manifiesto correcto en función del "nombre del modelo" (PE-101/APDK-101).|

## <a name="next-steps"></a>Pasos siguientes

[Actualización de Azure Percept DK de forma inalámbrica (OTA)](./how-to-update-over-the-air.md)
