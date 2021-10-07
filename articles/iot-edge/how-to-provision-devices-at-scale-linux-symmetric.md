---
title: 'Creación y aprovisionamiento de IoT Edge para dispositivos con claves simétricas en Linux: Azure IoT Edge | Microsoft Docs'
description: Uso de la atestación de clave simétrica para probar el aprovisionamiento de dispositivos Linux a escala para Azure IoT Edge con Device Provisioning Service
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c2bae93e325a36b6db28d1a9c1e6e47ee947db39
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699801"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>Creación y aprovisionamiento de dispositivos IoT Edge a escala en Linux mediante claves simétricas

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones de un extremo a otro para el aprovisionamiento automático de uno o varios dispositivos Linux IoT Edge mediante claves simétricas. Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con el [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Instale el entorno de ejecución de IoT Edge y conéctese a IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Para una experiencia simplificada, pruebe la [herramienta de configuración de Azure IoT Edge](https://github.com/azure/iot-edge-config). Esta herramienta de línea de comandos, actualmente en versión preliminar pública, instala IoT Edge en el dispositivo y lo aprovisiona mediante DPS y la atestación de clave simétrica.
:::moniker-end

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia del servicio Device Provisioning. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivo mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificado X.509](../iot-dps/concepts-x509-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos.

## <a name="prerequisites"></a>Prerrequisitos

* Una instancia de IoT Hub activa.
* Un dispositivo Linux físico o virtual para que sea el dispositivo IoT Edge.
  * Se debe definir un **identificador de registro** *único* para identificar cada dispositivo. Puede usar la dirección MAC, el número de serie o cualquier otra información única del dispositivo. Por ejemplo, podría usar una combinación de una dirección MAC y un número de serie que formen la siguiente cadena de un identificador de registro: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Solo se pueden usar caracteres alfanuméricos en minúsculas y guiones (`-`).
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de un nuevo IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y el Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) de la guía de inicio rápido IoT Hub Device Provisioning Service.
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Cree una inscripción para aprovisionar uno o varios dispositivos mediante DPS.

Si desea aprovisionar un único dispositivo IoT Edge, cree una **inscripción individual**. Si necesita aprovisionar varios dispositivos, siga los pasos para crear una **inscripción de grupo** de DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

Para más información sobre las inscripciones en Device Provisioning Service, consulte [Administración de inscripciones de dispositivos](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Creación de una inscripción individual de DPS

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. En **Mecanismo**, seleccione **Clave simétrica**.

   1. Proporcione un **Id. de registro** único para el dispositivo.

   1. De manera opcional, proporcione un **Id. de dispositivo IoT Hub** para el dispositivo. Puede usar identificadores de dispositivo para dirigirse a un dispositivo individual para la implementación del módulo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge.

   1. De manera opcional, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Seleccione **Guardar**.

1. Copie el valor de **Clave principal** de la inscripción individual para usarlo al instalar el entorno de ejecución de Azure IoT Edge.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Creación de una inscripción de grupo de DPS

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones de grupo mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para dispositivos de IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. Escriba un **Nombre de grupo**.

   1. Seleccione **Clave simétrica** como tipo de atestación.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

   1. De manera opcional, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Seleccione **Guardar**.

1. Copie el valor de **Clave principal** del grupo de inscripción para usarlo al crear claves de dispositivo para usarlas con una inscripción del grupo.

Ahora que existe un grupo de inscripciones, el entorno de ejecución de Azure IoT Edge puede aprovisionar los dispositivos durante la instalación de forma automática.

#### <a name="derive-a-device-key"></a>Derivación de una clave de dispositivo

Cada dispositivo que se aprovisiona como parte de una inscripción de grupo necesita una clave de dispositivo derivada para realizar la atestación de clave simétrica con la inscripción durante el aprovisionamiento.

Para generar la clave del dispositivo, use la clave que copió del grupo de inscripciones de DPS para calcular un valor [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) del identificador de registro único del dispositivo y convierta el resultado a formato Base64.

> [!IMPORTANT]
> No incluya la clave principal o secundaria de la inscripción en el código del dispositivo.

En Linux, puede usar openssl para generar la clave de dispositivo derivada tal y como se muestra en el ejemplo siguiente.

Reemplace el valor de **KEY** por el de la **clave principal** que ha apuntado anteriormente.

Reemplace el valor de **REG_ID** por el identificador de registro del dispositivo.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

A continuación se muestra una salida de ejemplo de una clave de dispositivo derivada:

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

En esta sección, preparará su máquina virtual Linux o dispositivo físico para IoT Edge. A continuación, instalará IoT Edge.

Hay dos pasos que debe completar en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita acceso a los paquetes de instalación de Microsoft y requiere la instalación de un motor de contenedor.

### <a name="access-the-microsoft-installation-packages"></a>Acceso a los paquetes de instalación de Microsoft

El dispositivo debe tener acceso a los paquetes de instalación de Microsoft.

1. Instale la configuración del repositorio que coincida con el sistema operativo del dispositivo.

   * **Ubuntu Sever 18.04**:

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
> Los paquetes de software de Azure IoT Edge están sujetos a los términos de licencia que se encuentran cada paquete (`usr/share/doc/{package-name}` o el directorio `LICENSE`). Lea los términos de licencia antes de usar el paquete. La instalación y el uso de un paquete constituyen la aceptación de estos términos. Si no acepta los términos de licencia, no utilice el paquete.

### <a name="install-a-container-engine"></a>Instalación de un motor del contenedor

Azure IoT Edge utiliza un runtime de contenedor compatible con OCI. En los escenarios de producción, se recomienda utilizar el motor de Moby. El motor Moby es el único motor de contenedor compatible oficialmente con Azure IoT Edge. Las imágenes de contenedor de Docker CE/EE son totalmente compatibles con el entorno de ejecución de Moby.

1. Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale el motor de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Si recibe algún error al instalar el motor del contenedor Moby, compruebe la compatibilidad con Moby del kernel de Linux. Algunos fabricantes de dispositivos incrustados distribuyen imágenes de dispositivos que contienen kernels de Linux personalizados sin las características necesarias para la compatibilidad del motor del contenedor. Ejecute el siguiente comando, que usa el [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) suministrado por Moby, para comprobar la configuración del kernel.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > En la salida del script, compruebe que todos los elementos que figuran en `Generally Necessary` y `Network Drivers` estén habilitados. Si faltan características, puede habilitarlas volviendo a generar el kernel a partir del origen y seleccionando los módulos asociados para incluirlos en el archivo .config del kernel adecuado. Igualmente, si usa un generador de configuración de kernel como `defconfig` o `menuconfig`, busque y habilite las características correspondientes y vuelva a generar el kernel como corresponda. Una vez que haya implementado el kernel recién modificado, vuelva a ejecutar el script check-config para comprobar que se han habilitado correctamente todas las características necesarias.

### <a name="install-iot-edge"></a>Instalación de IoT Edge

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

El demonio de seguridad de IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El demonio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de [Instalación sin conexión o de una versión específica](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

1. Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale IoT Edge versión 1.1* junto con el paquete **libiothsm-std**.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge versión 1.1 es la rama de soporte técnico a largo plazo de IoT Edge. Si ejecuta una versión anterior, se recomienda instalar o actualizar a la revisión más reciente, ya que ya no se admiten las versiones anteriores.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

El servicio IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El servicio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

El servicio de identidad de IoT se introdujo junto con la versión 1.2 de IoT Edge. Este servicio controla el aprovisionamiento y la administración de identidades para IoT Edge y otros componentes de dispositivo que necesitan comunicarse con IoT Hub.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de [Instalación sin conexión o de una versión específica](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

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

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio Device Provisioning y a IoT Hub.

Tenga lista la siguiente información:

* El valor **Ámbito de id.** del DPS
* El **Id. de registro** del dispositivo que ha creado
* La **Clave principal** de una inscripción individual o una [clave derivada](#derive-a-device-key) para dispositivos que usan una inscripción de grupo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Busque la sección configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario de las líneas del aprovisionamiento de claves simétricas de DPS y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   La línea `provisioning:` no debe ir precedida por espacios en blanco y se debe aplicar una sangría de dos espacios a los elementos anidados.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Actualice los valores de `scope_id`, `registration_id` y `symmetric_key` con la información de DPS y del dispositivo.

1. También puede usar las líneas `always_reprovision_on_startup` o `dynamic_reprovisioning` para configurar el comportamiento de reaprovisionamiento del dispositivo. Si un dispositivo se establece para que se vuelva a aprovisionar en el inicio, siempre intentará aprovisionar con DPS primero y, a continuación, revertir a la copia de seguridad de aprovisionamiento si se produce un error. Si un dispositivo se establece para que se vuelva a aprovisionar dinámicamente, IoT Edge se reiniciará y volverá a aprovisionar si se detecta un evento de reaprovisionamiento. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Cree un archivo de configuración para el dispositivo basándose en un archivo de plantilla que se proporciona como parte de la instalación de IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Busque la sección **Provisioning** (Aprovisionamiento) del archivo. Quite las marcas de comentario de las líneas del aprovisionamiento a DPS de clave simétrica y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Actualice los valores de `id_scope`, `registration_id` y `symmetric_key` con la información de DPS y del dispositivo.

   El parámetro de clave simétrica puede aceptar un valor de una clave insertada, un URI de archivo o un URI de PKCS#11. Quite la marca de comentario de una sola línea de clave simétrica, en función del formato que esté usando.

   Si usa cualquier URI de PKCS#11, busque la sección **PKCS#11** en el archivo de configuración y proporcione información sobre la configuración de PKCS#11.

1. Guarde y cierre el archivo config.toml.

1. Aplique los cambios de configuración que ha realizado a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Compruebe el estado del servicio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine los registros del servicio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Compruebe el estado del servicio IoT Edge.

```cmd/sh
sudo iotedge system status
```

Examine los registros del servicio.

```cmd/sh
sudo iotedge system logs
```

Enumere los módulos en ejecución.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
