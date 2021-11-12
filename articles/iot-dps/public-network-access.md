---
title: Administración del acceso a la red pública para Azure IoT Device Provisioning Service (DPS)
description: Documentación sobre cómo deshabilitar y habilitar el acceso a la red pública para Azure IoT Device Provisioning Service (DPS)
ms.author: v-stharr
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 74c22d802c022d51a1ef8b4aa231cc92661d582b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858468"
---
# <a name="manage-public-network-access-for-your-iot-device-provisioning-service"></a>Administración del acceso a la red pública para IoT Device Provisioning Service

Para restringir el acceso a [un punto de conexión privado para DPS en la red virtual](virtual-network-support.md), deshabilite el acceso a la red pública. Para ello, use Azure Portal o la API de `publicNetworkAccess`. También puede permitir el acceso público mediante el portal o la API de `publicNetworkAccess`.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Desactivación del acceso a la red pública mediante Azure Portal

Para desactivar el acceso a la red pública:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.
3. Seleccione la instancia de Device Provisioning Service.
4. En el menú **Configuración** de la izquierda, seleccione *Redes*.
5. En **Public network access** (Acceso a la red pública), seleccione *Deshabilitado*.
6. Seleccione **Guardar**.

    :::image type="content" source="media/iot-dps-public-network-access/disable-public-access.png" alt-text="Imagen que muestra en Azure Portal dónde desactivar el acceso a la red pública" :::

Para activar el acceso a la red pública:

1. Seleccione **Todas las redes**.
2. Seleccione **Guardar**.

## <a name="access-the-dps-after-disabling-the-public-network-access"></a>Acceso a DPS después de deshabilitar el acceso a la red pública

Una vez deshabilitado el acceso a la red pública, la instancia de DPS solo es accesible a través de [su punto de conexión privado de red virtual mediante el Azure Private Link](virtual-network-support.md). Esta restricción incluye el acceso a través de Azure Portal.

## <a name="dps-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Punto de conexión de DPS, dirección IP y puertos después de deshabilitar el acceso a la red pública

DPS es una plataforma como servicio (PaaS) multiinquilino, en la que diferentes clientes comparten el mismo grupo de recursos de proceso, redes y hardware de almacenamiento. Los nombres de host de DPS se asignan a un punto de conexión público con una dirección IP enrutable públicamente a través de Internet. Diferentes clientes comparten este punto de conexión público de DPS, que es accesible para los dispositivos de IoT en redes de área extensa y redes locales. 

La deshabilitación del acceso a la red pública se aplica en un recurso de DPS específico, lo que garantiza el aislamiento. Para mantener el servicio activo para otros recursos del cliente usando la ruta de acceso pública, su punto de conexión público aún se puede resolver, las direcciones IP aún se pueden detectar y los puertos permanecen abiertos. Esto no es motivo de preocupación, ya que Microsoft integra varias capas de seguridad para garantizar el aislamiento completo entre los inquilinos. Para más información, consulte [Aislamiento en la nube pública de Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtro IP

Si el acceso a la red pública está deshabilitado, todas las reglas de [Filtro IP](../iot-dps/iot-dps-ip-filtering.md) se omiten. Esto se debe a que se bloquean todas las direcciones IP de la red pública de Internet. Para usar el filtro IP, utilice la opción **Selected IP ranges** (Intervalos IP seleccionados).

### <a name="turn-on-all-network-ranges"></a>Activación de todos los intervalos de red

Para activar todos los intervalos de red:

1. Vaya a [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.
3. Seleccione la instancia de Device Provisioning Service.
4. En el menú **Configuración** de la izquierda, seleccione *Redes*.
5. En **Public network access** (Acceso a la red pública), seleccione *Todas las redes*.
6. Seleccione **Guardar**.
