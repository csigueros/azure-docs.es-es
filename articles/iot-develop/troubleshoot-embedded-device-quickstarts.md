---
title: Solución de problemas de inicios rápidos de dispositivos insertados de Azure RTOS
description: Pasos para ayudarle a solucionar problemas comunes al usar los inicios rápidos de dispositivos insertados de Azure RTOS
author: JimacoMS4
ms.author: v-jbrannian
ms.service: iot-develop
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.openlocfilehash: 26353064f929c382559216c28651757c7b4ee498
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124861"
---
# <a name="troubleshooting-the-azure-rtos-embedded-device-quickstarts"></a>Solución de problemas de inicios rápidos de dispositivos insertados de Azure RTOS

A medida que siga los [inicios rápidos de desarrollo de dispositivos insertados](quickstart-devkit-mxchip-az3166.md), puede experimentar algunos problemas comunes. En general, se pueden producir problemas en cualquiera de los orígenes siguientes:

* **El entorno**. La configuración y conexión de la máquina, el software o la red.
* **Los recursos de Azure IoT**. El centro de IoT y el dispositivo que ha creado para conectarse a Azure IoT.
* **El dispositivo**. La placa física y su configuración.

En este artículo se proporcionan sugerencias de resolución para los problemas más comunes que se pueden producir al completar los inicios rápidos.

## <a name="prerequisites"></a>Requisitos previos

En todos los pasos de solución de problemas es necesario que haya completado los siguientes requisitos previos para el inicio rápido en el que trabaje:

* Ha instalado o adquirido todos los requisitos previos y las herramientas de software para el inicio rápido.
* Ha creado un centro de Azure IoT o Azure IoT Central, y ha registrado un dispositivo, como se indica en el inicio rápido.
* Ha creado una imagen para el dispositivo, como se indica en el inicio rápido.

## <a name="issue-the-source-directory-doesnt-contain-cmakeliststxt-file"></a>Problema: El directorio de origen no contiene el archivo CMakeLists.txt
### <a name="description"></a>Descripción
Este problema se puede producir al intentar compilar el proyecto. Es el resultado de que el proyecto se ha clonado incorrectamente desde GitHub. El proyecto contiene varios submódulos que no se clonarán de forma predeterminada a menos que se utilice la marca **--recursive**.

### <a name="resolution"></a>Resolución
* Al clonar el repositorio mediante Git, confirme que la opción **--recursive** está presente.

## <a name="issue-the-build-fails"></a>Problema: Se produce un error en la compilación

### <a name="description"></a>Descripción

El problema se puede producir porque la ruta de un archivo de objeto supera la longitud máxima de ruta predeterminada en Windows. Examine la salida de compilación para ver un mensaje similar al siguiente:

```output
-- Configuring done
CMake Warning in C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/core/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeLists.txt:
  The object file directory

    C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/NXP/MIMXRT1060-EVK/build/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeFiles/asc_security_core.dir/./

  has 208 characters.  The maximum full path to an object file is 250
  characters (see CMAKE_OBJECT_PATH_MAX).  Object file

    src/serializer/extensions/custom_builder_allocator.c.obj

  cannot be safely placed under this directory.  The build may not work
  correctly.


-- Generating done
```

### <a name="resolution"></a>Resolución

Puede probar una de las opciones siguientes para resolver este error:
* Clone el repositorio en un directorio con una ruta más corta e inténtelo de nuevo.
* Siga las instrucciones de [Limitación de longitud máxima de ruta](/windows/win32/fileio/maximum-file-path-limitation) para habilitar rutas largas en Windows 10 versión 1607 y posteriores.

## <a name="issue-device-cant-connect-to-iot-hub"></a>Problema: El dispositivo no se puede conectar a IoT Hub

### <a name="description"></a>Descripción

El problema se puede producir después de haber creado recursos de Azure y de haber actualizado el dispositivo. Al intentar conectar el dispositivo recién actualizado a Azure IoT, verá un mensaje de consola como el siguiente:

```output
Unable to resolve DNS for MQTT Server
```

### <a name="resolution"></a>Resolución

* Compruebe la ortografía y las mayúsculas y minúsculas de los valores de configuración que ha escrito para la configuración de IoT en el archivo *azure_config.h*. Los valores de algunos atributos de recursos de IoT, como `deviceID` y `primaryKey`, distinguen mayúsculas y minúsculas.

## <a name="issue--wi-fi-is-unable-to-connect"></a>Problema: No se puede conectar la red Wi-Fi

### <a name="description"></a>Descripción

Después de actualizar un dispositivo que usa una conexión Wi-Fi e intentar conectarse a la red Wi-Fi, recibe un mensaje de error que indica que la red Wi-Fi no se puede conectar.

### <a name="resolution"></a>Resolución

* Compruebe la frecuencia y la configuración de la red Wi-Fi. En todos los dispositivos de los inicios rápidos de dispositivo insertado se usan 2,4 GHz. Confirme que el enrutador Wi-Fi está configurado para admitir una red de 2,4 GHz.
* Compruebe el modo de Wi-Fi. Confirme qué configuración ha usado para la constante WIFI_MODE en el archivo *azure_config.h*. Compruebe la configuración Wi-Fi de autenticación o seguridad de red para confirmar que el modo de seguridad de Wi-Fi coincide con lo que tiene en el archivo de configuración.

## <a name="issue-flashing-the-board-fails"></a>Problema: Se produce un error al actualizar la placa

### <a name="description"></a>Descripción

No puede completar el proceso de actualización del dispositivo. Lo sabrá si experimenta alguno de los síntomas siguientes:

* El archivo de imagen * *.bin* que ha creado no se copia en el dispositivo.
* La utilidad que usa para actualizar el dispositivo genera una advertencia o un error.
* La utilidad que usa para actualizar el dispositivo no indica que la programación se ha completado correctamente.

### <a name="resolution"></a>Resolución

* Asegúrese de que está conectado al puerto USB correcto en el dispositivo. Algunos dispositivos tienen más de un puerto.
* Pruebe a usar otro cable Micro USB. Algunos dispositivos y cables no son compatibles.
* Intente conectarse a otro puerto USB en el equipo. Es posible que un puerto USB esté desconectado internamente, deshabilitado en el software o en un estado inutilizable temporal.
* Reinicie el equipo.

## <a name="issue-device-fails-to-connect-to-port"></a>Problema: El dispositivo no se puede conectar al puerto

### <a name="description"></a>Descripción

Después de actualizar el dispositivo y conectarlo al equipo, recibe un mensaje como el siguiente en el software de terminal:

```output
Failed to initialize the port.
Please verify the COM port settings.
```

### <a name="resolution"></a>Resolución

* En la configuración del software de terminal, compruebe la opción **Puerto** para confirmar que ha seleccionado el puerto correcto. Si se muestran varios puertos, puede abrir el Administrador de dispositivos de Windows y seleccionar el nodo **Puertos** para buscar el puerto correcto para el dispositivo conectado.

## <a name="issue-terminal-output-shows-garbled-text"></a>Problema: La salida del terminal muestra texto codificado

### <a name="description"></a>Descripción

Después de actualizar el dispositivo correctamente y conectarlo al equipo, ve una salida de texto codificado en el software de terminal.

### <a name="resolution"></a>Resolución

* En la configuración del software de terminal, confirme que el valor de **Velocidad en baudios** es *115,200*.

## <a name="issue-terminal-output-shows-no-text"></a>Problema: La salida del terminal no muestra texto

### <a name="description"></a>Descripción

Después de actualizar el dispositivo correctamente y conectarlo al equipo, no ve ninguna salida en el software de terminal.

### <a name="resolution"></a>Resolución

* Confirme que la configuración del software de terminal coincide con la del inicio rápido.
* Reinicie el software de terminal.
* Presione el botón **Reset** (Restablecer) en el dispositivo.
* Confirme que el cable USB está conectado correctamente.

## <a name="issue-communication-between-device-and-iot-hub-fails"></a>Problema: No funciona la comunicación entre el dispositivo e IoT Hub

### <a name="description"></a>Descripción

Después de actualizar el dispositivo y conectarlo al equipo, recibe un mensaje repetido como el siguiente en la ventana de terminal:

```output
Failed to publish temperature
```

### <a name="resolution"></a>Resolución

* Confirme que el *Nivel de precios y de escala* es *Gratis* o *Estándar*. **No se admite Básico**, ya que no permite la comunicación de la nube al dispositivo y de dispositivos gemelos.

## <a name="next-steps"></a>Pasos siguientes

Si después de revisar los problemas de este artículo, todavía no puede supervisar el dispositivo en un terminal ni conectarse a Azure IoT, es posible que haya un problema con el hardware o la configuración física del dispositivo. Vea la página del fabricante del dispositivo para buscar documentación y opciones de soporte técnico.

* [STMicroelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)
* [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)
* [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)