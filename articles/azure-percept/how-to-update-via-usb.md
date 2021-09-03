---
title: Actualización de Azure Percept DK a través de una conexión por cable USB-C
description: Aprenda a actualizar Azure Percept DK a través de una conexión por cable USB-C
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e40a75f91ebf371d27b541b74ba427f42bc518f3
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652912"
---
# <a name="update-the-azure-percept-dk-over-a-usb-c-cable-connection"></a>Actualización de Azure Percept DK por medio de una conexión por cable USB-C

En esta guía se muestra cómo actualizar correctamente el firmware y el sistema operativo del kit de desarrollo a través de una conexión USB. Esta es una introducción de lo que va a hacer durante este procedimiento.
1. Descarga del paquete de actualización en un equipo host
1. Ejecución del comando que transfiere el paquete de actualización al kit de desarrollo
1. Establecer el kit de desarrollo en modo USB mediante conmutadores SSH o DIP
1. Conexión del kit de desarrollo al equipo host mediante un cable USB-C
1. Espera a que se complete la purga

> [!WARNING]
> Al actualizar el kit de desarrollo a través de USB, se eliminarán todos los datos existentes en el dispositivo, incluidos los modelos y contenedores de AI.
>
> Siga todas las instrucciones en orden. Omitir los pasos podría poner el kit de desarrollo en un estado inutilizable.


## <a name="prerequisites"></a>Requisitos previos

- Una instancia de Azure Percept DK
- Un equipo host basado en Windows o Linux con capacidad Wi-Fi y un puerto USB-C o USB-A disponible
- Un cable USB-C a USB-a (opcional, vendido por separado)
- Una cuenta de inicio de sesión SSH, creada durante la [experiencia de instalación de Azure Percept DK](./quickstart-percept-dk-set-up.md)
- Una llave inglesa, incluida con el kit de desarrollo, para quitar los tornillos de la parte posterior del kit de desarrollo (si se usa el método de conmutador DIP)

> [!NOTE]
> **Usuarios de Mac**: la actualización de Azure Percept DK por medio de una conexión USB no funcionará con un equipo Mac como equipo host. 

## <a name="download-software-tools-and-update-files"></a>Descargar herramientas de software y archivos de actualización

1. [Herramienta NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Descargue la **versión más reciente** del archivo uuu.exe versión (para Windows) o del archivo uuu (para Linux) en la pestaña **Recursos**. UUU es una herramienta creada por NXP que se usa para actualizar los paneles de desarrollo NXP.

1. [Descargue los archivos de actualización](https://go.microsoft.com/fwlink/?linkid=2155734). Todos están incluidos en un archivo ZIP que se extraerá en la sección siguiente.

1. Asegúrese de que los tres artefactos de compilación están presentes:
    - Azure-Percept-DK- *&lt;número de versión&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configurar el entorno

1. Cree una carpeta o un directorio en el equipo host en una ubicación a la que pueda acceder fácilmente a través de la línea de comandos.

1. Copie la herramienta UUU (**uuu.exe** o **uuu**) en la nueva carpeta.

1. Extraiga los archivos de actualización descargados anteriormente en la nueva carpeta que contiene la herramienta UUU.

## <a name="run-the-command-that-transfers-the-update-package-to-the-dev-kit"></a>Ejecución del comando que transfiere el paquete de actualización al kit de desarrollo

1. Abra un símbolo del sistema de Windows (Inicio > cmd) o un terminal de Linux y **vaya a la carpeta donde se almacenan los archivos de actualización y la herramienta UUU**. 

1. Escriba el comando siguiente en el símbolo del sistema o terminal.

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. En la ventana del símbolo del sistema se mostrará un mensaje que indica "**Waiting for Known USB Device to Appear...** " (Esperando a que aparezca un dispositivo USB conocido...). La herramienta UUU espera a que el equipo host detecte el kit de desarrollo. **Continúe con los pasos siguientes y coloque el kit de desarrollo en modo USB.**

## <a name="set-the-dev-kit-into-usb-mode"></a>Configuración del kit de desarrollo en modo USB
Hay dos maneras de establecer el kit de desarrollo en "modo USB", mediante SSH o cambiando los conmutadores DIP en el kit de desarrollo. Elija el método más adecuado para la situación.

### <a name="using-ssh"></a>Uso de SSH
SSH es el método más seguro y preferido para establecer el kit de desarrollo en modo USB. Pero es necesario que se pueda conectar al punto de acceso Wi-Fi del kit de desarrollo. Si no se puede conectar al punto de acceso Wi-Fi del kit de desarrollo, tendrá que usar el método de conmutador DIP.

1. Conecte el cable USB-C suministrado al puerto USB-C del kit de desarrollo y al puerto USB-C del equipo host. Si el equipo solo tiene un puerto USB-A, conecte un cable USB-C a USB-A (se vende por separado) al kit de desarrollo y al equipo host.

1. Conéctese al kit de desarrollo a través de SSH. Si necesita ayuda para SSH, [siga estas instrucciones](./how-to-ssh-into-percept-dk.md).

1. En el terminal SSH, escriba los siguientes comandos:

    1. Establezca el dispositivo en modo de actualización USB:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Reinicie el dispositivo. Se iniciará la instalación de la actualización.

        ```bash
        sudo reboot -f
        ```

### <a name="using-the-dip-switch-method"></a>Uso del método de conmutador DIP
Use el método de conmutador DIP cuando no se pueda conectar mediante SSH al dispositivo.

1. Desconecte la placa de desarrollo si está conectada al cable de alimentación.
1. Quite los cuatro tornillos de la parte posterior de la placa de desarrollo con la llave inglesa incluida con el kit de desarrollo.

    :::image type="content" source="media/how-to-usb-update/dip-switch-01.jpg" alt-text="Extracción de los cuatro tornillos de la parte posterior de la placa de desarrollo":::

1. Deslice con suavidad la placa de desarrollo en la dirección de los LED. El receptor térmico permanecerá conectado a la parte superior de la placa de desarrollo. Deslice la placa de desarrollo solo 2-3 centímetros para evitar desconectar los cables.

    :::image type="content" source="media/how-to-usb-update/dip-switch-02.jpg" alt-text="Deslizamiento de la placa unos centímetros":::

1. Los conmutadores DIP se pueden encontrar en la esquina de la placa. Hay cuatro conmutadores, cada uno con dos posiciones: arriba (1) o abajo (0). Las posiciones predeterminadas de los conmutadores son arriba-abajo-abajo-arriba (1001). Con un clip de papel u otro instrumento afilado, cambie las posiciones de los conmutadores a abajo-arriba-abajo-arriba (0101).

    :::image type="content" source="media/how-to-usb-update/dip-switch-03.jpg" alt-text="Ubicación de los conmutadores en la esquina inferior de la placa":::

1. Ahora el kit de desarrollo está en modo USB y puede continuar con los pasos siguientes. **Una vez que complete la actualización, cambie los conmutadores DIP a la posición predeterminada de arriba-abajo-abajo-arriba (1001).** Después, vuelva a colocar la placa de desarrollo y los cuatro tornillos de la parte posterior.

## <a name="connect-the-dev-kit-to-the-host-computer-via-a-usb-c-cable"></a>Conexión del kit de desarrollo al equipo host mediante un cable USB-C
Este procedimiento usa el único puerto USB-C del kit de desarrollo para la actualización.  Si el equipo tiene un puerto USB-C, puede usar el cable USB-C a USB-C incluido con el kit de desarrollo.  Si el equipo solo tiene un puerto USB-A, tendrá que usar un cable USB-C a USB-A (se vende por separado).

1. Conecte el kit de desarrollo al equipo host mediante el cable USB-C adecuado.
1. El equipo host ahora debe detectar el kit de desarrollo como un dispositivo USB. Si ha ejecutado correctamente el comando que transfiere el paquete de actualización al kit de desarrollo y el símbolo del sistema indica Waiting for Known USB Device to Appear...,** (Esperando a que aparezca el dispositivo USB conocido) la actualización debería iniciarse automáticamente en unos 10 segundos.

## <a name="wait-for-the-update-to-complete"></a>Espera a que se complete la purga

1. Vuelva al otro símbolo del sistema o terminal. Cuando finalice la actualización, verá un mensaje con ```Success 1    Failure 0```:

    > [!NOTE]
    > Después de la actualización, el dispositivo se restablecerá a la configuración de fábrica y perderá la conexión Wi-Fi y el inicio de sesión de SSH.

1. Una vez finalizada la actualización, desconecte el kit de desarrollo. Desconecte el cable USB del equipo.
1. Si ha usado el método de conmutador DIP para configurar el kit de desarrollo en modo USB, asegúrese de volver a colocar los conmutadores DIP en las posiciones predeterminadas. Después, vuelva a colocar la placa de desarrollo y los cuatro tornillos de la parte posterior.   

## <a name="next-steps"></a>Pasos siguientes

Trabaje con la [experiencia de instalación de Azure Percept DK](./quickstart-percept-dk-set-up.md) para volver a configurar el dispositivo.
