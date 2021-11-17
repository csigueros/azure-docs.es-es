---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 32adad7acee7c1dbb75516d4ccaa597f2c69b9fb
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845103"
---
## <a name="install-iot-edge"></a>Instalación de IoT Edge

En esta sección, preparará la máquina virtual Linux o el dispositivo físico para IoT Edge. A continuación, instalará IoT Edge.

Tendrá que completar dos pasos en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita acceso a los paquetes de instalación de Microsoft y requiere la instalación de un motor de contenedor.

### <a name="access-the-microsoft-installation-packages"></a>Acceso a los paquetes de instalación de Microsoft

1. Instale la configuración del repositorio que coincida con el sistema operativo del dispositivo.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Copie la lista generada en el directorio sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Instale la clave pública de GPG de Microsoft.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

> [!NOTE]
> Los paquetes de software de Azure IoT Edge están sujetos a los términos de licencia que se encuentran cada paquete (`usr/share/doc/{package-name}` o el directorio `LICENSE`). Lea los términos de licencia antes de usar el paquete. La instalación y el uso de un paquete constituyen la aceptación de estos términos. Si no acepta los términos de licencia, no utilice ese paquete.

### <a name="install-a-container-engine"></a>Instalación de un motor del contenedor

Azure IoT Edge utiliza un runtime de contenedor compatible con OCI. En los escenarios de producción, se recomienda utilizar el motor de Moby. El motor de Moby es el único motor de contenedor compatible oficialmente con IoT Edge. Las imágenes de contenedor de Docker CE/EE son totalmente compatibles con el entorno de ejecución de Moby.

1. Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale el motor de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Si se producen errores al instalar el motor del contenedor Moby, compruebe la compatibilidad con Moby del kernel de Linux. Algunos fabricantes de dispositivos incrustados distribuyen imágenes de dispositivos que contienen kernels de Linux personalizados sin las características necesarias para la compatibilidad del motor del contenedor. Ejecute el siguiente comando, que usa el [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) suministrado por Moby, para comprobar la configuración del kernel:
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > En la salida del script, compruebe que todos los elementos que figuran en `Generally Necessary` y `Network Drivers` estén habilitados. Si faltan características, puede habilitarlas si vuelve a generar el kernel a partir del origen y selecciona los módulos asociados para incluirlos en el archivo .config de kernel adecuado. Igualmente, si usa un generador de configuración de kernel como `defconfig` o `menuconfig`, busque y habilite las características correspondientes y vuelva a generar el kernel como corresponda. Una vez que implemente el kernel recién modificado, vuelva a ejecutar el script check-config para comprobar que se han habilitado correctamente todas las características necesarias.

### <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

El demonio de seguridad de IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El demonio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de **Instalación sin conexión o de una versión específica**, más adelante en este artículo.

Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale IoT Edge versión 1.1.* junto con el paquete **libiothsm-std**:

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge versión 1.1 es la rama de soporte técnico a largo plazo de IoT Edge. Si ejecuta una versión anterior, se recomienda instalar o actualizar a la revisión más reciente, ya que ya no se admiten las versiones anteriores.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

El servicio IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El servicio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

El servicio de identidad de IoT se introdujo junto con la versión 1.2 de IoT Edge. Este servicio controla el aprovisionamiento y la administración de identidades para IoT Edge y otros componentes de dispositivo que necesitan comunicarse con IoT Hub.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de **Instalación sin conexión o de una versión específica**, más adelante en este artículo.

>[!NOTE]
>Los pasos de esta sección le muestran cómo instalar la versión 1.2 de IoT Edge.
>
>Si ya tiene un dispositivo IoT Edge que ejecuta una versión anterior y desea actualizar a 1.2, siga los pasos descritos en [Actualización del demonio de seguridad de IoT Edge y el entorno de ejecución](../articles/iot-edge/how-to-update-iot-edge.md). La versión 1.2 es suficientemente diferente de las versiones anteriores de IoT Edge para que sean necesarios pasos específicos para la actualización.

Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

Compruebe qué versiones de IoT Edge y el servicio de identidad de IoT están disponibles.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

Para instalar la versión más reciente de IoT Edge y el paquete de servicio de identidad de IoT, use el comando siguiente:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- end 1.2 -->
::: moniker-end
