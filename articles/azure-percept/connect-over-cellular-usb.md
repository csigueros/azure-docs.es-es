---
title: Conexión de Azure Percept DK mediante redes 5G y LTE con un módem USB
description: En este artículo se explica cómo conectar Azure Percept DK mediante redes 5G y LTE con un módem USB.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 4bb866c25c81b08be7d4769d33b7d49b3ac6849f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440036"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-usb-modem"></a>Conexión de Azure Percept DK mediante redes 5G y LTE con un módem USB

En este artículo se explica cómo conectar Azure Percept DK a redes 5G o LTE mediante un módem USB. 

> [!NOTE]
> La información de este artículo solo se aplica al software especial Azure Percept DK que se puede descargar conforme a las instrucciones de la sección siguiente. Una imagen especial de Azure Percept DK incluye el software de código abierto ModemManager, que admite una amplia variedad de módems USB. La imagen no admite actualizaciones por vía inalámbrica (OTA) del sistema operativo u otro software. Con el software de código abierto ModemManager puede usar un módem USB LTE simple y rentable o módems 5G más sofisticados para conectar Azure Percept DK a Internet y Azure. 
>
> Las instrucciones de este artículo están pensadas para usarse con módems USB que admiten una interfaz Mobile Broadband Interface Model (MBIM). Antes de obtener un módem USB, asegúrese de que admita la interfaz MBIM. Asegúrese también de que aparezca en la lista de módems admitidos de ModemManager. El software ModemManager se puede usar con otras interfaces, pero este artículo se centra en la interfaz MBIM. Para obtener más información, vaya a la página [freedesktop.org ModemManager](https://www.freedesktop.org/wiki/Software/ModemManager/).


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems-v2.png" alt-text="Ilustración fotográfica de Azure Precept DK con módems USB para conectarse a redes 5G y LTE.":::

## <a name="set-up-azure-percept-dk-to-use-a-usb-modem"></a>Configuración de Azure Percept DK para usar un módem USB

1. [Descargue la imagen de software Azure Percept 5G](https://aka.ms/azpercept5gimage) que admite ModemManager. 

   Estos tres archivos son necesarios para actualizar el software Azure Percept DK para admitir módems USB.

1. [Actualice el software Azure Percept DK](./how-to-update-via-usb.md) con el software especial Azure Percept DK habilitado para 5G/LTE que ha descargado en el paso anterior. 

   > [!IMPORTANT]
   > Siga las instrucciones de [Actualización de Azure Percept DK a través de una conexión USB-C](./how-to-update-via-usb.md), pero asegúrese de usar *solo* los archivos que ha descargado en el paso anterior y no los que se mencionan en el artículo.

1. Siga el proceso normal para la [Configuración del dispositivo de Azure Percept DK](./quickstart-percept-dk-set-up.md) si no está familiarizado con ella. 

   La experiencia de configuración no es diferente en esta versión habilitada para ModemManager de Azure Percept DK.

1. [Conéctese a Azure Percept DK mediante el protocolo de red Secure Shell (SSH)](./how-to-ssh-into-percept-dk.md).

## <a name="connect-to-a-modem"></a>Conexión a un módem

Las tres secciones siguientes contienen instrucciones para conectarse a varios módems USB.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Módem USB Conect 4G v2 de Vodafone

Este módem USB de Vodafone es una simple llave USB LTE CAT-4 sin características especiales. Las instrucciones de este módem se pueden usar para otros módems USB similares sencillos y económicos.

:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Ilustración de las vistas superior e inferior de un módem USB 4G v2 de Vodafone.":::

Para obtener instrucciones para conectar Azure Percept DK mediante un módem USB simple como Vodafone USB Connect 4G v2, vea [Conexión mediante Vodafone Connect 4G frente a módem USB](./connect-over-cellular-usb-vodafone.md).   

### <a name="multitech-multiconnect-usb-modem"></a>Módem USB Multiconnect de MultiTech

Este módem USB de MultiTech ofrece varios modos de funcionamiento USB. En este tipo de módem primero hay que habilitar el modo USB adecuado para habilitar la interfaz MBIM que admite ModemManager.

:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="Ilustración del módem USB Multiconnect de MultiTech.":::

Para conectar Azure Percept DK mediante un módem USB simple como el de MultiTech (MTCM-LNA3-B03), siga las instrucciones de [Conexión mediante módem USB MultiTech](./connect-over-cellular-usb-multitech.md).

### <a name="quectel-5g-developer-kit"></a>Kit para desarrolladores de Quectel 5G

El tercer módem es Quectel 5G DK. También ofrece varios modos, y primero hay que habilitar el modo MBIM adecuado.

:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Ilustración del módem USB Quectel 5G DK.":::

Para obtener instrucciones para conectar Azure Percept DK mediante un módem USB 5G como Quectel RM500Q-GL, vea [Conexión mediante el kit para desarrolladores de Quectel 5G](./connect-over-cellular-usb-quectel.md). 

## <a name="help-your-5g-or-lte-connection-recover-from-reboot"></a>Ayuda para recuperar la conexión 5G o LTE de un reinicio 
Puede configurar el módem USB para conectar a la red, pero si reinicia el dispositivo, tiene que volver a conectar manualmente. Actualmente se está trabajando en una solución para mejorar esta experiencia. Para obtener más información, póngase en contacto con [nuestro equipo de soporte técnico](mailto:azpercept5G@microsoft.com) con una breve nota que haga referencia a este problema. 

## <a name="debugging-information"></a>Información de depuración 
Asegúrese de que la tarjeta SIM funciona en el hardware específico que quiere usar. Varios operadores limitan las tarjetas SIM de IoT de solo datos para que funcionen en un solo dispositivo. Por esta razón, asegúrese de que el IMEI o el número de serie del dispositivo aparecen en la lista de dispositivos permitidos por la tarjeta SIM del operador.

### <a name="modemmanager-debug-mode"></a>Modo de depuración de ModemManager

Para habilitar el modo de depuración de ModemManager, anexe `--debug` a la línea `ExecStart=/usr/sbin/ModemManager [...]` del archivo */lib/systemd/system/ModemManager.service*, como se muestra en el ejemplo siguiente:

```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```

Para que los cambios se apliquen, vuelva a cargar los servicios y reinicie ModemManager, como se muestra aquí:

```
systemctl daemon-reload
systemctl restart ModemManager
```

Al ejecutar los siguientes comandos, puede ver los registros y limpiar los archivos de registro:

```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="enhance-reliability-and-stability"></a>Mejora de la confiabilidad y la estabilidad

Para evitar que ModemManager interactúe con interfaces de serie que no sean módems, puede restringir qué interfaces se sondean (para determinar cuáles son módems) si cambia las [directivas de filtro](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html). 

Se recomienda usar el modo `STRICT`.

Para ello, anexe `--filter-policy=STRICT` a la línea `ExecStart=/usr/sbin/ModemManager [...]` del archivo */lib/systemd/system/ModemManager.service*, como se muestra en el ejemplo siguiente:

```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
Para que los cambios se apliquen, vuelva a cargar los servicios y reinicie ModemManager, como se muestra aquí:

```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>Pasos siguientes

* [Conexión mediante 5G o LTE](./connect-over-cellular.md)
* [Conexión mediante puerta de enlace de telefonía móvil](./connect-over-cellular-gateway.md)
