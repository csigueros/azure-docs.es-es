---
title: Solución de problemas con Azure Percept DK
description: Obtenga sugerencias para la solución de algunos de los problemas más comunes que pueden surgir con Azure Percept DK e IoT Edge.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: caea6bbd8ebcf3dbe2d6f8b45174326a5ba4f169
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066740"
---
# <a name="azure-percept-dk-troubleshooting"></a>Solución de problemas de Azure Percept DK

El propósito de este artículo de solución de problemas es ayudar a los usuarios de Azure Percept DK a resolver rápidamente problemas comunes con sus kits de desarrollo. También se proporcionan instrucciones sobre la recopilación de registros para cuando se necesita soporte técnico adicional.

## <a name="log-collection"></a>Recopilación de registros
En esta sección, se proporcionan instrucciones sobre qué registros se recopilan y cómo recopilarlos.

### <a name="how-to-collect-logs"></a>Cómo recopilar registros
1. Conéctese al kit de desarrollo [a través de SSH](./how-to-ssh-into-percept-dk.md).
1. Ejecute los comandos necesarios en la ventana del terminal SSH. Consulte la sección siguiente para obtener la lista de comandos de recopilación de registros.
1. Para redirigir la salida a un archivo .txt para su posterior análisis, use la sintaxis siguiente:
    ```console
    sudo [command] > [file name].txt
    ```
1. Cambie los permisos del archivo .txt para que se pueda copiar:
    ```console
    sudo chmod 666 [file name].txt
    ```
1. Copie el archivo en el equipo host a través de SCP:
    ```console
    scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
    ```

    ```[local host file path]``` hace referencia a la ubicación del equipo host en el que desea copiar el archivo .txt. ```[remote username]``` es el nombre de usuario de SSH elegido durante la [experiencia de instalación](./quickstart-percept-dk-set-up.md).

### <a name="log-types-and-commands"></a>Tipos de registros y comandos

|Finalidad del registro      |Cuándo recopilarlo         |Get-Help                     |
|-----------------|---------------------------|----------------------------|
|*Conjunto de soporte técnico*: proporciona un conjunto de registros necesarios para la mayoría de las solicitudes de soporte técnico del cliente.|Recopílelo cada vez que solicite soporte técnico.|```sudo iotedge support-bundle --since 1h``` <br><br>*"--since 1h" se puede cambiar a cualquier intervalo de tiempo; por ejemplo, "6h" (6 horas), "6d" (6 días) o "6 m" (6 minutos).*|
|*Registros de OOBE*: registra los detalles sobre la experiencia de configuración.|Recopílelos cuando tenga problemas durante la experiencia de configuración.|```sudo journalctl -u oobe -b```|
|*Registros de edgeAgent*: registra los números de versión de todos los módulos que se ejecutan en el dispositivo.|Recopílelos cuando no funcionen uno o varios módulos.|```sudo iotedge logs edgeAgent```|
|*Registros de contenedor de módulos*: registra los detalles sobre contenedores de módulos IoT Edge específicos.|Recopílelos cuando tenga problemas con un módulo.|```sudo iotedge logs [container name]```|
|*Registros de punto de acceso Wi-Fi*: registra los detalles sobre la conexión al punto de acceso Wi-Fi del kit de desarrollo.|Recopílelos cuando tenga problemas al conectarse al punto de acceso Wi-Fi del kit de desarrollo.|```sudo journalctl -u hostapd.service```|
|*Registros de red*: un conjunto de registros que abarcan los servicios Wi-Fi y la pila de red.|Recopílelos cuando tenga problemas de Wi-Fi o de red.|```sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd > network_log.txt```<br><br>```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version && rpm -q ztpd > system_ver.txt```<br><br>Ejecute ambos comandos. Cada comando recopila varios registros y los coloca en una única salida.|

## <a name="troubleshooting-commands"></a>Solución de problemas de comandos
Este es un conjunto de comandos que se pueden usar para solucionar problemas que puede encontrar con el kit de desarrollo. Para ejecutar estos comandos, primero debe conectarse al kit de desarrollo [a través de SSH](./how-to-ssh-into-percept-dk.md). 

Para más información sobre los comandos de Azure IoT Edge, consulte la [documentación de solución de problemas de dispositivos de Azure IoT Edge](../iot-edge/troubleshoot.md). 

|Función         |Cuándo se usa                    |Get-Help                 |
|------------------|----------------------------|---------------------------|
|Comprueba la versión de software en el kit de desarrollo.|Úsela cada vez que necesite confirmar qué versión de software está en el kit de desarrollo.|```cat /etc/adu-version```|
|Comprueba la temperatura del kit de desarrollo.|Úsela en casos en los que piense que el kit de desarrollo podría estar sobrecalentándose.|```cat /sys/class/thermal/thermal_zone0/temp```|
|Comprueba el identificador de telemetría del kit de desarrollo.|Úsela en casos en los que necesite conocer el identificador de telemetría único del kit de desarrollo.|```sudo azure-device-health-id```|
|Comprueba el estado de IoT Edge.|Úsela siempre que haya problemas con los módulos de IoT Edge que se conectan a la nube.|```sudo iotedge check```|
|Reinicia el demonio de seguridad de Azure IoT Edge.|Úsela cuando IoT Edge no responda o no funcione correctamente.|```sudo systemctl restart iotedge``` |
|Enumera los módulos de Azure IoT Edge implementados.|Úsela cuando necesite ver todos los módulos implementados en el kit de desarrollo.|```sudo iotedge list``` |
|Muestra el espacio disponible o total en los sistemas de archivos especificados.|Úsela si necesita conocer el almacenamiento disponible en el kit de desarrollo.|```df [option] [file]```|
|Muestra la información de IP e interfaz del kit de desarrollo.|Úsela cuando necesite conocer la dirección IP del kit de desarrollo.|`ip route get 1.1.1.1`        | 
|Muestra solo la dirección IP del kit de desarrollo.|Úsela cuando solo desee obtener la dirección IP del kit de desarrollo y ninguna otra información de la interfaz.|<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |

## <a name="usb-update-errors"></a>Errores de actualización de USB

|Error:                                    |Solución:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX en la unidad flash a través de UUU |Este error es el resultado de un error de conexión USB durante la actualización de UUU. Si el cable USB no está conectado correctamente a los puertos USB del equipo o a la placa base de Percept DK, se producirá un error con este formato. Intente desconectar y reconectar ambos extremos del cable USB y mueva el cable para garantizar una conexión segura.|

## <a name="clearing-hard-drive-space-on-the-azure-percept-dk"></a>Borrado del espacio de la unidad de disco duro en Azure Percept DK
Hay dos componentes que ocupan el espacio de la unidad de disco duro en Azure Percept DK: los registros de contenedor de Docker y los propios contenedores de Docker. Para asegurarse de que los registros de contenedor no ocuparán todo el espacio de la unidad de disco duro, Azure Percept DK tiene integrada la rotación de registros, que va eliminando los registros antiguos a medida que se generan nuevos registros.

Para situaciones en las que el número de contenedores de Docker provoca problemas de espacio en la unidad de disco duro, puede eliminar los contenedores no usados siguiendo estos pasos:
1. [Conéctese mediante SSH al kit de desarrollo](./how-to-ssh-into-percept-dk.md)
1. Ejecute este comando: `docker system prune`.

Esto quitará todos los contenedores, redes, imágenes y, opcionalmente, volúmenes que no se usan. [Consulte esta página](https://docs.docker.com/engine/reference/commandline/system_prune/) para obtener más información.

## <a name="azure-percept-dk-carrier-board-led-states"></a>Estados de los indicadores LED de la placa base de Azure Percept DK

Hay tres pequeños indicadores LED en la parte superior de la carcasa de la placa base. Un icono de nube está impreso junto al indicador LED 1, un icono de Wi-Fi junto al LED 2 y un icono de signo de exclamación junto al LED 3. Consulte la tabla siguiente para obtener información sobre cada estado de LED.

|LED             |State      |Descripción                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |Encendido (luz fija) |El dispositivo está conectado a una instancia de IoT Hub. |
|LED 2 (Wi-Fi)   |Luz intermitente lenta |El dispositivo está listo para que Wi-Fi Easy Connect pueda configurarlo y anuncia su presencia a un configurador. |
|LED 2 (Wi-Fi)   |Luz intermitente rápida |La autenticación se realizó correctamente y la asociación del dispositivo está en curso. |
|LED 2 (Wi-Fi)   |Encendido (luz fija) |La autenticación y la asociación se realizaron correctamente; el dispositivo está conectado a una red Wi-Fi. |
|LED 3           |N/D         |El indicador LED no está en uso. |