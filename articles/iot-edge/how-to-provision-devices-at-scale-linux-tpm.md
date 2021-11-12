---
title: 'Aprovisionamiento de dispositivos con un TPM virtual en Linux: Azure IoT Edge'
description: Use un TPM simulado en un dispositivo Linux para probar el servicio de aprovisionamiento de dispositivos de Azure IoT Hub para Azure IoT Edge.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e55d4f2bcb6e8f86a45d22e4a3a16555fefe8f81
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246631"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM en Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones para aprovisionar automáticamente un dispositivo Azure IoT Edge para Linux mediante un Módulo de plataforma segura (TPM). Puede aprovisionar de forma automática los dispositivos Azure IoT Edge con el [servicio de aprovisionamiento de dispositivos de Azure IoT Hub](../iot-dps/index.yml). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

En este artículo, se describen dos metodologías. Seleccione sus preferencias en función de la arquitectura de la solución:

- Aprovisionamiento automático de un dispositivo Linux con hardware de TPM físico. Un ejemplo es el dispositivo [Infineon OPTIGA&trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e).
- Aprovisionamiento automático de una máquina virtual (VM) Linux con un TPM simulado que se ejecuta en una máquina Windows de desarrollo con Hyper-V habilitado. Se recomienda usar esta metodología solo como escenario de prueba. Un TPM simulado no ofrece la misma seguridad que un TPM físico.

Las instrucciones difieren en función de la metodología, por lo que debe asegurarse de que se encuentra en la pestaña correcta para continuar.

# <a name="physical-device"></a>[Dispositivo físico](#tab/physical-device)

Las tareas son las siguientes:

1. Recuperar la información de aprovisionamiento del TPM.
1. Crear una inscripción individual para el dispositivo en una instancia del servicio de aprovisionamiento de dispositivos de IoT Hub.
1. Instalar el entorno de ejecución de IoT Edge y conectar el dispositivo al centro de IoT.

# <a name="virtual-machine"></a>[Máquina virtual](#tab/virtual-machine)

Las tareas son las siguientes:

1. Crear una máquina virtual Linux en Hyper-V con un TPM simulado para la seguridad de hardware.
1. Recuperar la información de aprovisionamiento del TPM.
1. Crear una inscripción individual para el dispositivo en una instancia del servicio de aprovisionamiento de dispositivos de IoT Hub.
1. Instalar el entorno de ejecución de IoT Edge y conectar el dispositivo al centro de IoT.

---

## <a name="prerequisites"></a>Prerrequisitos

# <a name="physical-device"></a>[Dispositivo físico](#tab/physical-device)

* Un centro de IoT activo.
* Una instancia del servicio de aprovisionamiento de dispositivos de IoT Hub en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia del servicio de aprovisionamiento de dispositivos, siga las instrucciones que se encuentran en dos secciones del inicio rápido del servicio de aprovisionamiento de dispositivos de IoT Hub:
     - [Creación de un servicio de aprovisionamiento de dispositivos de IoT Hub](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
     - [Vínculo al centro de IoT y al servicio de aprovisionamiento de dispositivos](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Cuando el servicio de aprovisionamiento de dispositivos esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

# <a name="virtual-machine"></a>[Máquina virtual](#tab/virtual-machine)

* Un centro de IoT activo.
* Una instancia del servicio de aprovisionamiento de dispositivos de IoT Hub en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia del servicio de aprovisionamiento de dispositivos, siga las instrucciones de las dos secciones del inicio rápido del servicio de aprovisionamiento de dispositivos de IoT Hub:
    - [Creación de un servicio de aprovisionamiento de dispositivos de IoT Hub](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [Vínculo al centro de IoT y al servicio de aprovisionamiento de dispositivos](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Cuando el servicio de aprovisionamiento de dispositivos esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.
* Un equipo de desarrollo de Windows [habilitado para Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). En este artículo se usa Windows 10 que ejecuta una máquina virtual de servidor de Ubuntu.

---

> [!NOTE]
> Se necesita TPM 2.0 cuando se usa la atestación de TPM con el servicio de aprovisionamiento de dispositivos.
>
> Solo puede crear inscripciones individuales, no de grupo, del servicio de aprovisionamiento de dispositivos cuando se usa un TPM.

## <a name="set-up-your-device"></a>Configuración del dispositivo

# <a name="physical-device"></a>[Dispositivo físico](#tab/physical-device)

Si usa un dispositivo físico Linux con un TPM, no hay ningún paso adicional para configurar el dispositivo.

Está listo para continuar.

# <a name="virtual-machine"></a>[Máquina virtual](#tab/virtual-machine)

En esta sección, se va a crear una máquina virtual Linux en Hyper-V. Esta máquina virtual se configurará con un TPM simulado para probar cómo funciona el aprovisionamiento automático con IoT Edge.

> [!TIP]
> Si usa una máquina virtual, la copiará y pegará en la máquina virtual muchas veces a lo largo de este artículo. Las tareas de copiar y pegar no son fáciles mediante la aplicación de conexión del Administrador de Hyper-V. Es posible que quiera conectarse a la máquina virtual mediante el administrador de Hyper-V una vez para recuperar su dirección IP. Ejecute `sudo apt install net-tools` primero y, luego, `hostname -I`. Después, puede usar la dirección IP para conectarse a través de SSH: `ssh <username>@<ipaddress>`.

### <a name="create-a-virtual-switch"></a>Crear un conmutador virtual

Un conmutador virtual permite que la máquina virtual se conecte a una red física.

1. Abra el administrador de Hyper-V en la máquina Windows.

1. En el menú **Acciones**, seleccione **Virtual Switch Manager** (Administrador de conmutadores virtuales).

1. Seleccione un conmutador virtual **Externo** y después **Crear conmutador virtual**.

1. Asigne un nombre al nuevo conmutador virtual. Por ejemplo, use **EdgeSwitch**. Asegúrese de que el tipo de conexión esté establecido en **Red externa** y, después, seleccione **Aceptar**.

1. Un mensaje emergente advierte de que es posible que se interrumpa la conectividad de red. Seleccione **Yes** (Sí) para continuar.

> [!TIP]
> Si ve errores al crear el conmutador virtual, asegúrese de que ningún otro conmutador utilice el adaptador Ethernet ni el mismo nombre.

### <a name="create-the-virtual-machine"></a>Creación de la máquina virtual

Cree una nueva máquina virtual a partir de un archivo de imagen de arranque.

1. Descargue un archivo de imagen de disco a fin de usarlo para la máquina virtual y guárdelo en el entorno local. Por ejemplo, [Servidor de Ubuntu 18.04](http://releases.ubuntu.com/18.04/). Para obtener información acerca de los sistemas operativos compatibles para dispositivos IoT Edge, consulte [sistemas compatibles con Azure IoT Edge](./support.md).

1. En el Administrador de Hyper-V, seleccione **Acción** > **Nueva** > **Máquina virtual** en el menú **Acciones**.

1. Complete el **Asistente para crear nueva máquina virtual** con las siguientes configuraciones específicas:

   1. **Especificación de la generación**: seleccione **Generación 2**. Las máquinas virtuales de generación 2 tienen la virtualización anidada habilitada, lo que es necesario para ejecutar IoT Edge en una máquina virtual.
   1. **Configurar funciones de red**: establezca el valor de **Conexión** en el conmutador virtual que creó en la sección anterior.
   1. **Opciones de instalación**: seleccione **Instalar un sistema operativo desde un archivo de imagen de arranque** y busque el archivo de imagen de disco que ha guardado localmente.

1. Seleccione **Finalizar** en el asistente para crear la máquina virtual.

La creación de la máquina virtual puede tardar unos minutos.

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Una vez que se cree la máquina virtual, abra la configuración para habilitar el TPM virtual, que permite aprovisionar de forma automática el dispositivo.

1. En el Administrador de Hyper-V, haga clic con el botón derecho en la VM y seleccione **Configuración**.

1. Vaya a **Seguridad**.

1. Desactive **Habilitar arranque seguro**.

1. Seleccione **Enable Trusted Platform Module** (Habilitar módulo de plataforma segura).

1. Seleccione **Aceptar**.

### <a name="start-the-virtual-machine"></a>Inicio de la máquina virtual

Inicie la máquina virtual para completar el proceso de instalación.

1. En el Administrador de Hyper-V, inicie VM y conéctese a ella.

1. Siga las indicaciones en la máquina virtual para finalizar el proceso de instalación y reinicie la máquina.

Una vez que haya finalizado la instalación y haya vuelto a iniciar sesión en la máquina virtual, estará listo para continuar.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>Recuperación de la información de aprovisionamiento del TPM

En esta sección, se crea una herramienta que podrá utilizar para recuperar el identificador de registro y la clave de aprobación del TPM.

1. Inicie sesión en el dispositivo y luego siga los pasos de [Configuración de un entorno de desarrollo con Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar y compilar el SDK de dispositivo IoT de Azure para C.

1. Ejecute los comandos siguientes para compilar la herramienta del SDK que recupera la información de aprovisionamiento de dispositivos desde el TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. En la ventana de salida se muestra el **Id. de registro** y la **Clave de aprobación**. Copie estos valores para usarlos más tarde al crear una inscripción individual para el dispositivo en el servicio de aprovisionamiento de dispositivos.

> [!TIP]
> Si no quiere usar la herramienta del SDK para recuperar la información, tendrá que buscar otra manera de obtener la información de aprovisionamiento. La clave de aprobación, que es única para cada chip de TPM, se obtiene del fabricante asociado a este. Puede derivar un identificador de registro único para el dispositivo TPM. Por ejemplo, puede crear un hash SHA-256 de la clave de aprobación.

Una vez que tenga el identificador de registro y la clave de aprobación, estará listo para continuar.

## <a name="create-a-device-provisioning-service-enrollment"></a>Creación de una inscripción del servicio de aprovisionamiento de dispositivos

Recupere la información de aprovisionamiento del TPM y úsela para crear una inscripción individual en el servicio de aprovisionamiento de dispositivos.

Al crear una inscripción en el servicio de aprovisionamiento de dispositivos, tiene la oportunidad de declarar un **estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica utilizada en la solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), vaya a la instancia del servicio de aprovisionamiento de dispositivos de IoT Hub.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Agregar inscripción individual** y, después, complete los pasos siguientes para configurar la inscripción:

   1. En **Mecanismo**, seleccione **TPM**.

   1. Proporcione la **clave de aprobación** y el **identificador de registro** que ha copiado de la máquina virtual o el dispositivo físico.

   1. Si quiere, proporcione un identificador para el dispositivo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que esta máquina virtual o el dispositivo físico es un dispositivo IoT Edge.

   1. Elija el centro de IoT vinculado al que quiere conectar el dispositivo o seleccione **Link to new IoT Hub** (Vincular a un nuevo centro de IoT). Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   1. Si quiere, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Para más información, consulte [Implementación de módulos IoT Edge a escala](how-to-deploy-at-scale.md).

   1. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

En esta sección, preparará la máquina virtual Linux o el dispositivo físico para IoT Edge. Después, instalará IoT Edge.

Tendrá que completar dos pasos en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita acceso a los paquetes de instalación de Microsoft y requiere la instalación de un motor de contenedor.

### <a name="access-the-microsoft-installation-packages"></a>Acceso a los paquetes de instalación de Microsoft

El dispositivo debe tener acceso a los paquetes de instalación de Microsoft.

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
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Los paquetes de software de Azure IoT Edge están sujetos a los términos de licencia que se encuentran cada paquete (`usr/share/doc/{package-name}` o el directorio `LICENSE`). Lea los términos de licencia antes de usar el paquete. La instalación y el uso de un paquete constituyen la aceptación de estos términos. Si no acepta los términos de licencia, no utilice ese paquete.

### <a name="install-a-container-engine"></a>Instalación de un motor del contenedor

IoT Edge se basa en un entorno de ejecución de contenedor compatible con OCI. En los escenarios de producción, se recomienda utilizar el motor de Moby. El motor de Moby es el único motor de contenedor compatible oficialmente con IoT Edge. Las imágenes de contenedor de Docker CE/EE son totalmente compatibles con el entorno de ejecución de Moby.

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

### <a name="install-iot-edge"></a>Instalación de IoT Edge

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

El demonio de seguridad de IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El demonio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación sin conexión, siga los pasos de Instalación sin conexión o de una versión específica.

1. Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale IoT Edge versión 1.1* junto con el paquete **libiothsm-std**.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge versión 1.1 es la rama de soporte técnico a largo plazo de IoT Edge. Si ejecuta una versión anterior, se recomienda instalar o actualizar a la revisión más reciente, porque las versiones anteriores ya no se admiten.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

El servicio IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El servicio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

El servicio de identidad de IoT se introdujo junto con la versión 1.2 de IoT Edge. Este servicio controla el aprovisionamiento y la administración de identidades para IoT Edge y otros componentes de dispositivo que necesitan comunicarse con IoT Hub.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación sin conexión, siga los pasos de Instalación sin conexión o de una versión específica.

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

O bien, si decide instalar una versión de IoT Edge diferente a la más reciente, asegúrese de instalar la misma versión para los servicios `aziot-edge` y `aziot-identity-service`.

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio de aprovisionamiento de dispositivos y a IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Tome el **Ámbito de identificador** del servicio de aprovisionamiento de dispositivos y el **Identificador de registro** del dispositivo que se han recopilado antes.

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Busque la sección de configuración de aprovisionamiento del archivo. Quite las marcas de comentario del aprovisionamiento TPM y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   La línea `provisioning:` no debe ir precedida por espacios en blanco y se debe aplicar una sangría de dos espacios a los elementos anidados.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Actualice los valores de `scope_id` y `registration_id` con la información del servicio de aprovisionamiento de dispositivos y del dispositivo. El valor `scope_id` es el **ámbito de identificador** de la página de información general de la instancia del servicio de aprovisionamiento de dispositivos.

1. También puede usar las líneas `always_reprovision_on_startup` o `dynamic_reprovisioning` para configurar el comportamiento de reaprovisionamiento del dispositivo. Si un dispositivo se establece para que se vuelva a aprovisionar en el inicio, siempre se intentará aprovisionar primero con el servicio de aprovisionamiento de dispositivos y, después, revertir a la copia de seguridad de aprovisionamiento si se produce un error. Si un dispositivo se establece para que se vuelva a aprovisionar dinámicamente por sí mismo, IoT Edge se reiniciará y volverá a aprovisionar si se detecta un evento de reaprovisionamiento. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Guarde y cierre el archivo.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Tome el **Ámbito de identificador** del servicio de aprovisionamiento de dispositivos y el **Identificador de registro** del dispositivo que se han recopilado antes.

1. Cree un archivo de configuración para el dispositivo en función de un archivo de plantilla que se proporciona como parte de la instalación de IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Busque la sección de configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario del aprovisionamiento TPM y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Actualice los valores de `id_scope` y `registration_id` con la información del servicio de aprovisionamiento de dispositivos y del dispositivo. El valor `scope_id` es el **ámbito de identificador** de la página de información general de la instancia del servicio de aprovisionamiento de dispositivos.

1. Opcionalmente, busque la sección del modo de reaprovisionamiento automático del archivo. Use el parámetro `auto_reprovisioning_mode` para configurar el comportamiento de reaprovisionamiento del dispositivo en `Dynamic`, `AlwaysOnStartup` o `OnErrorOnly`. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Guarde y cierre el archivo.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acceso a IoT Edge en el TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Para que el entorno de ejecución de IoT Edge aprovisione automáticamente el dispositivo, necesita acceso al TPM.

Puede conceder acceso TPM al entorno de ejecución de Azure IoT Edge mediante la invalidación de la configuración de systemd para que el servicio `iotedge` tenga privilegios raíz. Si no desea elevar los privilegios de servicio, también puede usar los pasos siguientes para proporcionar manualmente el acceso TPM.

1. Cree una regla que conceda al entorno de ejecución de IoT Edge acceso a `tpm0` y `tpmrm0`.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Abra el archivo de reglas.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copie la siguiente información de acceso en el archivo de reglas. Es posible que `tpmrm0` no esté presente en los dispositivos que usan un kernel anterior a 4.12. Los dispositivos que no tengan `tpmrm0` omitirán esa regla de forma segura.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. Guarde y cierre el archivo.

1. Desencadene el sistema `udev` para evaluar la nueva regla.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Compruebe que la regla se haya aplicado correctamente.

   ```bash
   ls -l /dev/tpm*
   ```

   Una salida correcta tiene el siguiente aspecto:

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si no ve que se hayan aplicado los permisos correctos, intente reiniciar la máquina para actualizar `udev`.

1. Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

El entorno de ejecución de IoT Edge se basa en un servicio TPM que accede mediante agentes al TPM de un dispositivo. Para que este servicio aprovisione automáticamente el dispositivo, necesita acceso al TPM.

Puede conceder acceso al TPM mediante la invalidación de la configuración de systemd para que el servicio `aziottpm` tenga privilegios raíz. Si no desea elevar los privilegios de servicio, también puede usar los pasos siguientes para proporcionar manualmente el acceso TPM.

1. Cree una regla que conceda al entorno de ejecución de IoT Edge acceso a `tpm0` y `tpmrm0`.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Abra el archivo de reglas.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copie la siguiente información de acceso en el archivo de reglas. Es posible que `tpmrm0` no esté presente en los dispositivos que usan un kernel anterior a 4.12. Los dispositivos que no tengan `tpmrm0` omitirán esa regla de forma segura.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. Guarde y cierre el archivo.

1. Desencadene el sistema `udev` para evaluar la nueva regla.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Compruebe que la regla se haya aplicado correctamente.

   ```bash
   ls -l /dev/tpm*
   ```

   Una salida correcta tiene el siguiente aspecto:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si no ve que se hayan aplicado los permisos correctos, intente reiniciar la máquina para actualizar `udev`.

1. Aplique los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Reinicie IoT Edge y compruebe que la instalación se ha realizado correctamente

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Si todavía no la hecho, reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Compruebe que el entorno de ejecución de IoT Edge esté en ejecución.

   ```bash
   sudo systemctl status iotedge
   ```

Examine los registros del daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Si ve errores de aprovisionamiento, es posible que los cambios de configuración no hayan surtido efecto todavía. Pruebe a reiniciar de nuevo el demonio de IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

O bien, pruebe a reiniciar la máquina virtual para ver si los cambios surten efecto con un inicio nuevo.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Si todavía no lo ha hecho, aplique los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo iotedge config apply
   ```

Compruebe que el entorno de ejecución de IoT Edge esté en ejecución.

   ```bash
   sudo iotedge system status
   ```

Examine los registros del daemon.

   ```cmd/sh
   sudo iotedge system logs
   ```

Si ve errores de aprovisionamiento, es posible que los cambios de configuración no hayan surtido efecto todavía. Pruebe a reiniciar el demonio de IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

O bien, pruebe a reiniciar la máquina virtual para ver si los cambios surten efecto con un inicio nuevo.
:::moniker-end
<!-- end 1.2 -->

Si el entorno de ejecución se ha iniciado correctamente, puede entrar en la instancia de IoT Hub y ver que el nuevo dispositivo se aprovisionado automáticamente. Ahora el dispositivo está listo para ejecutar módulos de IoT Edge.

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

Puede comprobar que se ha utilizado la inscripción individual que ha creado en el servicio de aprovisionamiento de dispositivos. En Azure Portal, vaya a la instancia del servicio de aprovisionamiento de dispositivos. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción del servicio de aprovisionamiento de dispositivos permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos.

Aprenda a [implementar y supervisar módulos de IoT Edge a gran escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).