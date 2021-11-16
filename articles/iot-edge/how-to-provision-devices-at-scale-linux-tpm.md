---
title: 'Creación y aprovisionamiento de dispositivos con un TPM virtual en Linux: Azure IoT Edge'
description: Use un TPM simulado en un dispositivo Linux para probar el servicio de aprovisionamiento de dispositivos de Azure IoT Hub para Azure IoT Edge.
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b353f683a64444a0ad89f062d0b826484260681f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852054"
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

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

# <a name="physical-device"></a>[Dispositivo físico](#tab/physical-device)

Un dispositivo Linux físico para que sea el dispositivo IoT Edge físico.

# <a name="virtual-machine"></a>[Máquina virtual](#tab/virtual-machine)

Un equipo de desarrollo de Windows [habilitado para Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). En este artículo se usa Windows 10 que ejecuta una máquina virtual de servidor de Ubuntu.

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

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

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
     scope_id: "SCOPE_ID_HERE"
     attestation:
       method: "tpm"
       registration_id: "REGISTRATION_ID_HERE"
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
   id_scope = "SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "REGISTRATION_ID_HERE"
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

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

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
