---
title: Creación y aprovisionamiento de dispositivos IoT Edge a escala mediante certificados X.509 en Linux - Azure IoT Edge | Microsoft Docs
description: Uso de certificados X.509 para probar el aprovisionamiento de dispositivos a escala para Azure IoT Edge con Device Provisioning Service
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2dc45c3b088dc25a39496280b1e612a27c9dd69d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699732"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>Creación y aprovisionamiento de dispositivos IoT Edge a escala en Linux mediante certificados X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones de un extremo a otro para el aprovisionamiento automático de uno o varios dispositivos Linux IoT Edge con certificados X.509. Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con el [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Generar certificados y claves.
1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Instalar el entorno de ejecución de IoT Edge y registrar el dispositivo con IoT Hub.

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Normalmente, los certificados X.509 están organizados en una cadena de certificados de confianza. Comenzando por un certificado raíz de confianza o autofirmado, cada certificado de la cadena firma al certificado inmediatamente inferior. Este patrón crea una cadena delegada de confianza desde el certificado raíz a través de todos los certificados intermedios hasta el certificado "hoja" final que está instalado en un dispositivo.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de IoT Hub activa.
* Un dispositivo Linux físico o virtual para que sea el dispositivo IoT Edge.
* Tener instalada la versión más reciente de [Git](https://git-scm.com/download/).
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de un nuevo IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y el Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) de la guía de inicio rápido IoT Hub Device Provisioning Service.
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generación de los certificados de identidad del dispositivo

El certificado de identidad del dispositivo es un certificado de hoja que se conecta a través de una cadena de certificados de confianza con el certificado de la entidad de certificación X.509 (CA) superior. El nombre común (CN) del certificado de identidad del dispositivo debe estar establecido en el id. de dispositivo que quiere que tenga el dispositivo en el centro de IoT.

Los certificados de identidad del dispositivo solo se usan para aprovisionar al dispositivo IoT Edge y autenticarlo con Azure IoT Hub. No son certificados de firma, a diferencia de los certificados de CA que el dispositivo IoT Edge presenta a los módulos o los dispositivos hoja para la comprobación. Para obtener más información, consulte los [detalles de uso de los certificados de Azure IoT Edge](iot-edge-certs.md).

Después de crear el certificado de identidad del dispositivo, debe tener dos archivos: un archivo. cer o. pem que contiene la parte pública del certificado y un archivo. cer o. pem con la clave privada del certificado. Si planea usar una inscripción de grupo en DPS, también necesitará la parte pública de un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza.

Necesita los siguientes archivos para configurar el aprovisionamiento automático con X.509:

* El certificado de identidad del dispositivo y su certificado de clave privada. El certificado de identidad de dispositivo se carga en DPS si crea una inscripción individual. La clave privada se pasa al entorno de ejecución de Azure IoT Edge.
* Una cadena de certificados completa, que debe contener al menos la identidad del dispositivo y los certificados intermedios. La cadena de certificados completa se pasa al entorno de ejecución de Azure IoT Edge.
* Un certificado intermedio o raíz de entidad de certificación de la cadena de certificados de confianza. Este certificado se carga en DPS si crea una inscripción de grupo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Actualmente, una limitación en libiothsm impide el uso de certificados que expiran el 1 de enero de 2038 o en una fecha posterior.

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>Uso de certificados de prueba (opcional)

Si no tiene una entidad de certificación disponible para crear nuevos certificados de identidad y quiere probar este escenario, el repositorio de Git de Azure IoT Edge contiene scripts que puede usar para generar certificados de prueba. Estos certificados están diseñados solo para pruebas de desarrollo y no deben usarse en producción.

Para crear certificados de prueba, siga los pasos descritos en [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](how-to-create-test-certificates.md). Complete las dos secciones necesarias para configurar los scripts de generación de certificados y crear un certificado de CA raíz. A continuación, siga los pasos para crear un certificado de identidad del dispositivo. Cuando haya terminado, debe tener la cadena de certificados y el par de claves siguientes:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Necesita ambos certificados en el dispositivo IoT Edge. Si va a usar la inscripción individual en DPS, deberá cargar el archivo. cert.pem. Si va a usar la inscripción de grupos en DPS, también necesitará un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza que se va a cargar. Si usa certificados de demostración, use el certificado `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem` para la inscripción de grupos.

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Use las claves y los certificados generados para crear una inscripción en DPS para uno o más dispositivos IoT Edge.

Si desea aprovisionar un único dispositivo IoT Edge, cree una **inscripción individual**. Si necesita aprovisionar varios dispositivos, siga los pasos para crear una **inscripción de grupo** de DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

Para más información sobre las inscripciones en Device Provisioning Service, consulte [Administración de inscripciones de dispositivos](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Creación de una inscripción individual de DPS

Las inscripciones individuales usan la parte pública del certificado de identidad de un dispositivo y la asocian con el certificado del dispositivo.

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   * **Mecanismo**: Seleccione **X.509**.

   * **Primary Certificate .pem or .cer file** (Archivo .pem o .cer del certificado principal): cargue el archivo público desde el certificado de identidad del dispositivo. Si usó los scripts para generar un certificado de prueba, elija el siguiente archivo:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **Id. de dispositivo IoT Hub**: Si lo desea, proporcione un identificador para el dispositivo. Puede usar identificadores de dispositivo para dirigirse a un dispositivo individual para la implementación del módulo. Si no especifica un id. de dispositivo, se usa el nombre común (CN) en el certificado X. 509.

   * **Dispositivo IoT Edge**: Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge.

   * **Seleccione los centros de IoT a los que se puede asignar este dispositivo**: elija la instancia de IoT Hub vinculada a la que quiere conectar el dispositivo. Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   * **Estado inicial de dispositivo gemelo**: agregue un valor de etiqueta para que se agregue al dispositivo gemelo, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino de la implementación automática. Por ejemplo:

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

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Creación de una inscripción de grupo de DPS

Las inscripciones de grupo usan un certificado de CA raíz o intermedio de la cadena de certificados de confianza para verificar los certificados de identidad del dispositivo individuales.

#### <a name="verify-your-root-certificate"></a>Comprobación del certificado raíz

Al crear un grupo de inscripción, tiene la opción de usar un certificado comprobado. Puede comprobar un certificado con DPS al probar que tiene la propiedad del certificado raíz. Para obtener más información, consulte [Realización de una prueba de posesión de certificados de entidad de certificación X.509](../iot-dps/how-to-verify-certificates.md).

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. Seleccione **Certificados** en el menú de la izquierda.

1. Seleccione **Agregar** para agregar un certificado nuevo.

1. Escriba un nombre descriptivo para el certificado y después busque el archivo .cer o .pem que representa la parte pública del certificado X.509.

   Si usa los certificados de demostración, cargue el certificado `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem`.

1. Seleccione **Guardar**.

1. Ahora, el certificado debe aparecer en la página **Certificados**. Selecciónelo para abrir los detalles del certificado.

1. Seleccione **Generar código de comprobación** y, a continuación, copie el código generado.

1. Tanto si proporcionó su propio certificado de CA como si usa los certificados de demostración, puede usar la herramienta de comprobación incluida en el repositorio de IoT Edge para realizar la prueba de posesión. La herramienta de comprobación usa el certificado de CA para firmar un nuevo certificado que tiene el código de comprobación especificado como nombre de asunto.

   ```bash
   ./certGen.sh create_verification_certificate <verification code>
   ```

1. En la misma página de detalles del certificado en Azure Portal, cargue el certificado de comprobación recién generado.

1. Seleccione **Comprobar**.

#### <a name="create-enrollment-group"></a>Creación del grupo de inscripción

Para más información sobre las inscripciones en Device Provisioning Service, consulte [Administración de inscripciones de dispositivos](../iot-dps/how-to-manage-enrollments.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones de grupo mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para dispositivos de IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add enrollment group** (Agregar grupo de inscripción) y, a continuación, complete los pasos siguientes para configurar la inscripción:

   * **Nombre de grupo**: proporcione un nombre fácil de recordar para esta inscripción de grupo.

   * **Tipo de atestación**: Seleccione **Certificado**.

   * **Dispositivo IoT Edge**: Seleccione **True**. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

   * **Tipo de certificado**: seleccione **Certificado de CA**.

   * **Certificado principal**: elija el certificado en la lista desplegable.

   * **Seleccione los centros de IoT a los que se puede asignar este dispositivo**: elija la instancia de IoT Hub vinculada a la que quiere conectar el dispositivo. Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   * **Estado inicial de dispositivo gemelo**: agregue un valor de etiqueta para que se agregue al dispositivo gemelo, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino de la implementación automática. Por ejemplo:

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

Ahora que existe una inscripción para estos dispositivos, el entorno de ejecución de Azure IoT Edge puede aprovisionar los dispositivos durante la instalación de forma automática.

---

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

En esta sección, preparará su máquina virtual Linux o dispositivo físico para IoT Edge. A continuación, instalará IoT Edge.

Hay dos pasos que debe completar en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita acceso a los paquetes de instalación de Microsoft y requiere la instalación de un motor de contenedor.

### <a name="access-the-microsoft-installation-packages"></a>Acceso a los paquetes de instalación de Microsoft

El dispositivo debe tener acceso a los paquetes de instalación de Microsoft.

1. Instale la configuración del repositorio que coincida con el sistema operativo del dispositivo.

   * **Ubuntu Sever 18.04**:

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

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de Instalación sin conexión o de una versión específica.

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

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de Instalación sin conexión o de una versión específica.

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

* El valor de **Ámbito de id.** del DPS. Puede recuperar este valor de la página Información general de la instancia de DPS en Azure Portal.
* El archivo de cadena de certificados de identidad del dispositivo en el dispositivo.
* El archivo de clave de identidad del dispositivo en el dispositivo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Busque la sección configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario de las líneas de aprovisionamiento de certificado X.509 de DPS y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   La línea `provisioning:` no debe ir precedida por espacios en blanco y se debe aplicar una sangría de dos espacios a los elementos anidados.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Actualice los valores de `scope_id`, `identity_cert` y `identity_pk` con la información de DPS y del dispositivo.

   Al agregar el certificado X.509 y la información de clave al archivo config.yaml, las rutas de acceso se deben especificas como URI de archivo. Por ejemplo:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. De manera opcional, proporcione el `registration_id` del dispositivo, que debe coincidir con el nombre común (CN) del certificado de identidad. Si deja esa línea comentada, se aplicará automáticamente el CN.

1. También puede usar las líneas `always_reprovision_on_startup` o `dynamic_reprovisioning` para configurar el comportamiento de reaprovisionamiento del dispositivo. Si un dispositivo se establece para que se vuelva a aprovisionar en el inicio, siempre intentará aprovisionar con DPS primero y, a continuación, revertir a la copia de seguridad de aprovisionamiento si se produce un error. Si un dispositivo se establece para que se vuelva a aprovisionar dinámicamente, IoT Edge se reiniciará y volverá a aprovisionar si se detecta un evento de reaprovisionamiento. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Guarde y cierre el archivo config.yaml.

1. Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. Busque la sección **Provisioning** (Aprovisionamiento) del archivo. Quite las marcas de comentario de las líneas de aprovisionamiento de DPS con el certificado X.509 y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "<REGISTRATION ID>"

   identity_cert = "<DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Actualice el valor de `id_scope` con el identificador de ámbito que copió de la instancia de DPS.

1. Proporcione un `registration_id` para el dispositivo, que es el identificador que el dispositivo tendrá en IoT Hub. El identificador de registro debe coincidir con el nombre común (CN) del certificado de identidad.

1. Actualice los valores de `identity_cert` y `identity_pk` con la información del certificado y el dispositivo.

   El valor del certificado de identidad se puede proporcionar como un URI de archivo o se puede emitir dinámicamente mediante EST o una entidad de certificación local. Quite la marca de comentario de una sola línea, en función del formato que decida usar.

   El valor de clave privada de identidad se puede proporcionar como un URI de archivo o como un URI de PKCS#11. Quite la marca de comentario de una sola línea, en función del formato que decida usar.

   Si usa cualquier URI de PKCS#11, busque la sección **PKCS#11** en el archivo de configuración y proporcione información sobre la configuración de PKCS#11.

1. Guarde y cierre el archivo.

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

Puede comprobar que la inscripción de grupo que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Elementos registrados** para ver todos los dispositivos registrados en ese grupo.

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
