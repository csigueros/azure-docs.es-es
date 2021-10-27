---
title: Conexión de Azure Percept a través de redes 5G o LTE mediante un módem USB
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE mediante un módem USB.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e9fb2ae534d11fb0a85199baf7256ab174e3f25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007069"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-usb-modem"></a>Conexión de Azure Percept a través de redes 5G o LTE mediante un módem USB

A continuación encontrará los pasos para preparar y conectar Azure Percept DK a redes 5G o LTE mediante módems USB. Estas instrucciones solo se aplican al software de Azure Percept DK que puede descargar siguiendo la información que presentamos a continuación. Esta imagen especial de Azure Percept incluye el software de código abierto de ModemManager que admite una amplia variedad de módems USB. TENGA EN CUENTA que esta imagen no admite actualizaciones de OTA en el sistema operativo u otro SW. Con este SW, puede usar un módem USB LTE simple y rentable o módems 5G más sofisticados para conectar Azure Percept a Internet y Azure. 

> [!Note]
> Estas instrucciones están diseñadas para usarse con módems USB que admiten la interfaz MBIM. Antes de obtener un módem USB, asegúrese de que admite la interfaz MBIM. A continuación, asegúrese de que aparece en la lista de módems compatibles con ModemManager. El software de ModemManager se puede usar con otras interfaces, pero aquí nos hemos centrado en la interfaz MBIM. Obtenga más información sobre ModemManager aquí: https://www.freedesktop.org/wiki/Software/ModemManager/


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems.png" alt-text="Módems USB conectados a Azure Percept.":::

## <a name="setting-up-the-devkit-for-using-usb-modem"></a>Configuración del Devkit para usar el módem USB

- **Descargue la imagen del software de Azure Percept, compatible con ModemManager** - [ Archivos de imagen de software de Azure Percept 5G comprimidos](https://aka.ms/azpercept5gimage) para descargar los 3 archivos necesarios para actualizar su software de Azure Percept para que admita módems USB
- **Actualice Azure Percept con los archivos descargados mediante el método USB**: siga la guía [Cómo actualizar a través de USB](./how-to-update-via-usb.md) para actualizar el devkit con el software especial de Azure Percept habilitado para LTE y 5G que descargó en el paso anterior. Recuerde usar SOLO los archivos descargados en *el paso anterior* y no los que se indican en la guía de Cómo actualizar a través de USB.
- **Pase por el proceso de configuración normal** - [ quickstart-percept-dk-set-up](./quickstart-percept-dk-set-up.md) para seguir el proceso de configuración si no le resulta familiar. La experiencia de configuración no es diferente en esta versión habilitada para ModemManager de Azure Percept.
- **Conéctese a Azure Percept a través de SSH** - [how-to-ssh-into-percept-dk](./how-to-ssh-into-percept-dk.md) para obtener instrucciones

## <a name="step-by-step-instructions-for-connecting-three-different-modems"></a>Instrucciones paso a paso para conectar tres módems diferentes

A continuación encontrará instrucciones para tres módems USB diferentes. El primero es una simple llave USB LTE CAT-4 de Vodafone que no tiene características especiales. Las instrucciones de este módem se pueden usar también para módems USB sencillos y económicos parecidos. El segundo (MultiTech) es un ejemplo de módem USB que tiene diferentes modos de funcionamiento USB. Para este tipo de módem, primero debe habilitar el modo USB adecuado para habilitar la interfaz MBIM que admite ModemManager. El tercero es un módem 5G (Quectel DK) que también tiene distintos modos. Para empezar, debe habilitar el modo MBIM adecuado.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Módem USB Conect 4G v2 de Vodafone
:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Módem USB de Vodafone":::

Estas son las instrucciones para conectar el dispositivo Azure Percept DK mediante un sencillo módem USB como el USB Conect 4G v2 de Vodafone.

[Conexión mediante Vodafone Connect 4G frente a módem](./connect-over-cellular-usb-vodafone.md).   

### <a name="multitech-multiconnect-usb-modem"></a>Módem USB Multiconnect de MultiTech
:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="Módem USB de MultiTech":::

Estas son las instrucciones para conectar Azure Percept DK mediante un sencillo módem USB como el módem USB de MultiTech (MTCM-LNA3-B03).

[Conexión mediante el módem USB de Multitech](./connect-over-cellular-usb-multitech.md).   

### <a name="quectel-5g-developer-kit"></a>Kit para desarrolladores de Quectel 5G
:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Kit para desarrolladores de Quectel 5G":::

Estas son las instrucciones para conectar Azure Percept DK mediante un módem USB 5G como Quectel RM500Q-GL.

[Conexión mediante el kit para desarrolladores de Quectel 5G](./connect-over-cellular-usb-quectel.md). 

## <a name="how-to-make-your-lte5g-connection-recover-from-reboot"></a>Cómo hacer que la conexión LTE/5G se recupere del reinicio 
Con las instrucciones anteriores puede configurar el módem USB para conectarse a la red, pero si reinicia el dispositivo, tendrá que volver a conectarse manualmente. Estamos trabajando para encontrar una solución. Si está interesado en obtener más información, envíe un correo electrónico a azpercept5G@microsoft.com con una breve nota que haga referencia aquí. 

## <a name="debugging-information"></a>Información de depuración 
Recuerde siempre comprobar que la tarjeta SIM funciona con el hardware específico que desea usar. Varios operadores limitan los datos solo a las tarjetas SIM de IoT solo para que funcionen en un solo dispositivo. Por lo tanto, si ese es el caso, debe asegurarse de que el número IMEI o número de serie del dispositivo aparece en la "lista de dispositivos permitidos" de la tarjeta SIM por parte del operador.

### <a name="modemmanager-debug-mode"></a>Modo de depuración de ModemManager

El modo de depuración de ModemManager se puede habilitar editando el archivo `/lib/systemd/system/ModemManager.service` en la línea `ExecStart=/usr/sbin/ModemManager [...]` y anexando `--debug`, como en el ejemplo siguiente:
```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```
A continuación, tendrá que volver a cargar los servicios y reiniciar ModemManager para que los cambios tengan efecto:
```
systemctl daemon-reload
systemctl restart ModemManager
```
Estos son algunos comandos que le permiten ver los registros y limpiar los archivos de registro:
```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="reliability-and-stability-enhancement"></a>Mejora de la confiabilidad y la estabilidad

#### <a name="strict-mode"></a>Modo strict
Para evitar que ModemManager interactúe con interfaces de serie que no sean de módem, puede restringir qué interfaces se sondean (para determinar cuáles son módems) cambiando las [directivas de filtro](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html). Se recomienda usar el modo `STRICT`

Para ello, tendría que editar `/lib/systemd/system/ModemManager.service` en la línea `ExecStart=/usr/sbin/ModemManager [...]`, y agregar `--filter-policy=STRICT`, como en el ejemplo siguiente:
```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
A continuación, tendrá que volver a cargar los servicios y reiniciar ModemManager para que los cambios tengan efecto:
```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>Pasos siguientes
Ahora consulte las instrucciones para diferentes módems USB:

[Conexión mediante Vodafone Connect 4G frente a módem USB](./connect-over-cellular-usb-vodafone.md)

[Conexión mediante módem USB de Multitech](./connect-over-cellular-usb-multitech.md)

[Conexión mediante el kit para desarrolladores de Quectel 5G](./connect-over-cellular-usb-quectel.md)

Vuelva al artículo principal sobre 5G o LTE:

[Conexión mediante 5G o LTE](./connect-over-cellular.md)

[Conexión mediante puerta de enlace de telefonía móvil](./connect-over-cellular-gateway.md)
