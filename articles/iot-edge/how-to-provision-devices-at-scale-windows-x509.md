---
title: Creación y aprovisionamiento de dispositivos IoT Edge a escala mediante certificados X.509 en Windows - Azure IoT Edge | Microsoft Docs
description: Uso de certificados X.509 para probar el aprovisionamiento de dispositivos a escala para Azure IoT Edge con Device Provisioning Service
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d0be2b1752460744d9dad8686f69b27a9350ffb3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699621"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-x509-certificates"></a>Creación y aprovisionamiento de dispositivos IoT Edge a escala en Windows mediante certificados X.509

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones de un extremo a otro para el aprovisionamiento automático de uno o varios dispositivos Windows IoT Edge con certificados X.509. Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con el [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Generar certificados y claves.
1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Instalar el entorno de ejecución de IoT Edge y registrar el dispositivo con IoT Hub.

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Normalmente, los certificados X.509 están organizados en una cadena de certificados de confianza. Comenzando por un certificado raíz de confianza o autofirmado, cada certificado de la cadena firma al certificado inmediatamente inferior. Este patrón crea una cadena delegada de confianza desde el certificado raíz a través de todos los certificados intermedios hasta el certificado "hoja" final que está instalado en un dispositivo.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de IoT Hub activa.
* Un dispositivo Windows físico o virtual para que sea el dispositivo IoT Edge.
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

> [!NOTE]
> Actualmente, una limitación en libiothsm impide el uso de certificados que expiran el 1 de enero de 2038 o en una fecha posterior.

### <a name="use-test-certificates-optional"></a>Uso de certificados de prueba (opcional)

Si no tiene una entidad de certificación disponible para crear nuevos certificados de identidad y quiere probar este escenario, el repositorio de Git de Azure IoT Edge contiene scripts que puede usar para generar certificados de prueba. Estos certificados están diseñados solo para pruebas de desarrollo y no deben usarse en producción.

Para crear certificados de prueba, siga los pasos descritos en [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](how-to-create-test-certificates.md). Complete las dos secciones necesarias para configurar los scripts de generación de certificados y crear un certificado de CA raíz. A continuación, siga los pasos para crear un certificado de identidad del dispositivo. Cuando haya terminado, debe tener la cadena de certificados y el par de claves siguientes:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Necesita ambos certificados en el dispositivo IoT Edge. Si va a usar la inscripción individual en DPS, deberá cargar el archivo. cert.pem. Si va a usar la inscripción de grupos en DPS, también necesitará un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza que se va a cargar. Si usa certificados de demostración, use el certificado `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` para la inscripción de grupos.

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

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

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

Las inscripciones de grupo usan un certificado de CA raíz o intermedio de la cadena de certificados de confianza que se usó para generar los certificados de identidad del dispositivo individuales.

#### <a name="verify-your-root-certificate"></a>Comprobación del certificado raíz

Al crear un grupo de inscripción, tiene la opción de usar un certificado comprobado. Puede comprobar un certificado con DPS al probar que tiene la propiedad del certificado raíz. Para obtener más información, consulte [Realización de una prueba de posesión de certificados de entidad de certificación X.509](../iot-dps/how-to-verify-certificates.md).

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. Seleccione **Certificados** en el menú de la izquierda.

1. Seleccione **Agregar** para agregar un certificado nuevo.

1. Escriba un nombre descriptivo para el certificado y después busque el archivo .cer o .pem que representa la parte pública del certificado X.509.

   Si usa los certificados de demostración, cargue el certificado `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem`.

1. Seleccione **Guardar**.

1. Ahora, el certificado debe aparecer en la página **Certificados**. Selecciónelo para abrir los detalles del certificado.

1. Seleccione **Generar código de comprobación** y, a continuación, copie el código generado.

1. Tanto si proporcionó su propio certificado de CA como si usa los certificados de demostración, puede usar la herramienta de comprobación incluida en el repositorio de IoT Edge para realizar la prueba de posesión. La herramienta de comprobación usa el certificado de CA para firmar un nuevo certificado que tiene el código de comprobación especificado como nombre de asunto.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
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

Está listo para continuar con la sección siguiente.

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

En esta sección, preparará su máquina virtual Windows o dispositivo físico para IoT Edge. A continuación, instalará IoT Edge.

Hay un paso que debe completar en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita un motor de contenedor instalado.

### <a name="install-a-container-engine"></a>Instalación de un motor del contenedor

Azure IoT Edge se basa en un entorno de ejecución de contenedor compatible con OCI como [Moby](https://github.com/moby/moby), un motor basado en Moby que se incluye en el script de instalación. No hay pasos adicionales para instalar el motor.

### <a name="install-iot-edge"></a>Instalación de IoT Edge

El demonio de seguridad de IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El demonio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de Instalación sin conexión o de una versión específica.

1. Ejecute PowerShell como administrador.

   Use una sesión de AMD64 de PowerShell, no PowerShell (x86). Si no está seguro del tipo de sesión que usa, ejecute el comando siguiente:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Ejecute el comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge), que realiza las tareas siguientes:

   * Comprueba que la máquina con Windows se encuentra en una versión compatible.
   * Activa la característica de los contenedores.
   * Descarga el motor de Moby y el entorno de ejecución de Azure IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Si se le solicita, reinicie el dispositivo.

Al instalar IoT Edge en un dispositivo, puede usar parámetros adicionales para modificar el proceso, entre los que se incluyen los siguientes:

* Dirigir el tráfico para que pase por un servidor proxy
* Haga que el instalador apunte a un directorio local, en el caso de una instalación sin conexión.

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio Device Provisioning y a IoT Hub.

Tenga lista la siguiente información:

* El valor de **Ámbito de id.** del DPS. Puede recuperar este valor de la página Información general de la instancia de DPS en Azure Portal.
* El archivo de cadena de certificados de identidad del dispositivo en el dispositivo.
* El archivo de clave de identidad del dispositivo en el dispositivo.

1. Abra una ventana de Azure PowerShell en modo de administrador. Asegúrese de usar una sesión de AMD64 de PowerShell para instalar IoT Edge, no PowerShell (x86).

1. El comando **Initialize-IoTEdge** configura el entorno de ejecución de Azure IoT Edge en el equipo. De forma predeterminada, el comando realiza el aprovisionamiento manual con contenedores de Windows, a menos que se use la marca `-DpsX509` para utilizar el aprovisionamiento automático con la autenticación de certificados X.509.

   Reemplace los valores de marcador de posición de `{scope_id}`, `{identity cert chain path}` y `{identity key path}` por los valores adecuados de la instancia de DPS y las rutas de acceso de archivo en el dispositivo.

   Agregue el parámetro `-RegistrationId {registration_id}` si quiere establecer el identificador de dispositivo con un valor que no sea el nombre CN del certificado de identidad.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >El archivo de configuración almacena la información de certificado y clave como identificadores URI de archivo. Sin embargo, el comando Initialize-IoTEdge controla este paso de formato automáticamente. Por ello, puede proporcionar la ruta de acceso absoluta a los archivos de clave y certificado del dispositivo.

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

Compruebe el estado del servicio IoT Edge.

```powershell
Get-Service iotedge
```

Examine los registros del servicio.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Enumere los módulos en ejecución.

```powershell
iotedge list
```

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
