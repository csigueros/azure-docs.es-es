---
title: 'Creación y aprovisionamiento de dispositivos IoT Edge mediante certificados X.509 en Linux en Windows: Azure IoT Edge | Microsoft Docs'
description: Uso de la atestación de certificados X.509 para probar el aprovisionamiento de dispositivos a gran escala para Azure IoT Edge con Device Provisioning Service
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 309ac9bf4810d245695ad92c316a23d4d8571ac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699478"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>Creación y aprovisionamiento de IoT Edge para Linux en dispositivos Windows a gran escala mediante certificados X.509

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones de un extremo a otro para el aprovisionamiento automático de uno o varios dispositivos [IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md) mediante certificados X.509. Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Generar certificados y claves.
1. Crear una **inscripción individual** para un único dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Implementar una máquina virtual Linux con el entorno de ejecución de Azure IoT Edge instalado y conectarla a IoT Hub.

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Normalmente, los certificados X.509 están organizados en una cadena de certificados de confianza. Comenzando por un certificado raíz de confianza o autofirmado, cada certificado de la cadena firma al certificado inmediatamente inferior. Este patrón crea una cadena delegada de confianza desde el certificado raíz a través de todos los certificados intermedios hasta el certificado "hoja" final que está instalado en un dispositivo.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de IoT Hub activa.
* Tener instalada la versión más reciente de [Git](https://git-scm.com/download/).
* Un dispositivo Windows con los siguientes requisitos mínimos del sistema:
  * Windows 10, versión 1809 o posterior; compilación 17763 o posterior
  * Ediciones Pro, Enterprise o Server
  * Memoria libre mínima: 1 GB
  * Espacio libre en disco mínimo: 10 GB
  * Compatibilidad con la virtualización
    * En Windows 10, habilite Hyper-V. Para obtener más información, vea [Información de Hyper-V en Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
    * En Windows Server, instale el rol de Hyper-V y cree un conmutador de red predeterminado. Para más información, consulte [Virtualización anidada en Azure IoT Edge para Linux en Windows](nested-virtualization.md).
    * En una máquina virtual, configure la virtualización anidada. Para obtener más información, consulte [Virtualización anidada](nested-virtualization.md).
  * Soporte de red
    * Windows Server no incluye un conmutador predeterminado. Para poder implementar EFLOW en un dispositivo Windows Server, debe crear un conmutador virtual.  Para obtener más información, consulte [Creación de un conmutador virtual para Linux en Windows](how-to-create-virtual-switch.md).
    * Las versiones de escritorio de Windows incluyen un conmutador predeterminado que se puede usar para la instalación de EFLOW. Si es necesario, puede crear su propio conmutador virtual personalizado.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de una instancia de IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y a Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) del inicio rápido de IoT Hub Device Provisioning Service.
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.
* Si quiere usar **módulos de Linux con aceleración por GPU** en la implementación de Azure IoT Edge para Linux en Windows, debe tener en cuenta varias opciones de configuración. Tendrá que instalar los controladores correctos en función de la arquitectura de GPU y es posible que necesite acceder a una compilación del programa Windows Insider. Para determinar sus necesidades de configuración y cumplir estos requisitos previos, consulte [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md).

Puede usar tanto **PowerShell** como **Windows Admin Center** para instalar y gestionar los dispositivos IoT Edge. Cada utilidad tiene sus propios requisitos previos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si quiere usar PowerShell, siga estos pasos para descargar e instalar Azure IoT Edge para Linux en Windows:

1. En una sesión de PowerShell con privilegios elevados, ejecute cada uno de los siguientes comandos para descargar IoT Edge para Linux en Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Instale IoT Edge para Linux en Windows en el dispositivo.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Puede especificar la instalación personalizada y los directorios VHDX agregando los parámetros `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` y `VHDXDIR="<FULLY_QUALIFIED_PATH>"` al comando install.

1. Establezca la directiva de ejecución del dispositivo de destino en `AllSigned`. Puede comprobar la directiva de ejecución actual en un símbolo del sistema de PowerShell con privilegios elevados con el siguiente comando:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la directiva de ejecución de `local machine` no es `AllSigned`, puede establecer la directiva de ejecución con el siguiente comando:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Para obtener más información sobre el módulo de PowerShell de Azure IoT Edge para Linux en Windows, consulte la [referencia de funciones de PowerShell](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si quiere usar Windows Admin Center, asegúrese de que dispone de acceso a este y que tiene instalada la extensión de Azure IoT Edge:

   1. Descargue y ejecute el [instalador de Windows Admin Center](https://aka.ms/wacdownload). Siga las indicaciones del Asistente para la instalación a fin de instalar Windows Admin Center.

   1. Una vez instalado, use un explorador compatible para abrir Windows Admin Center. Entre los exploradores admitidos se incluyen Microsoft Edge (Windows 10, versión 1709 o posterior), Google Chrome y Microsoft Edge Insider.

   1. Al usar Windows Admin Center por primera vez, se le pedirá que seleccione un certificado para usarlo. Seleccione **Cliente de Windows Admin Center** como certificado.

   1. Instale la extensión de Azure IoT Edge. Seleccione el icono de engranaje en la parte superior derecha del panel de Windows Admin Center.

      ![Seleccione el icono de engranaje en la parte superior derecha del panel para acceder a la configuración, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. En el menú **Configuración**, en **Puerta de enlace**, seleccione **Extensiones**.

   1. En la pestaña **Extensiones disponibles**, busque **Azure IoT Edge** en la lista de extensiones. Esta dicha opción y seleccione la indicación **Instalar** sobre la lista de extensiones.

   1. Una vez finalizada la instalación, debería ver Azure IoT Edge en la lista de extensiones instaladas en la pestaña **Extensiones instaladas**.

---

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

Use las claves y los certificados generados para crear una inscripción en DPS para uno o varios dispositivos IoT Edge.

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

## <a name="create-a-new-iot-edge-for-linux-on-windows-deployment"></a>Creación de una nueva implementación de IoT Edge para Linux en Windows

Implemente Azure IoT Edge para Linux en Windows en el dispositivo de destino.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instale IoT Edge para Linux en Windows en el dispositivo de destino si todavía no lo ha hecho.

> [!NOTE]
> En el siguiente proceso de PowerShell se muestra cómo implementar IoT Edge para Linux en Windows en el dispositivo local. Si quiere realizar la implementación en un dispositivo de destino remoto mediante PowerShell, puede usar [PowerShell remoto](/powershell/module/microsoft.powershell.core/about/about_remote) para establecer una conexión a un dispositivo remoto y ejecutar estos comandos de manera remota en ese dispositivo.

1. En una sesión de PowerShell con privilegios elevados, ejecute cada uno de los siguientes comandos para descargar IoT Edge para Linux en Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Instale IoT Edge para Linux en Windows en el dispositivo.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Para especificar la instalación personalizada de IoT Edge para Linux en Windows y los directorios VHDX, agregue los parámetros `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` y `VHDXDIR="<FULLY_QUALIFIED_PATH>"` al comando de instalación.

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned` si todavía no lo está. Consulte los requisitos previos de PowerShell para los comandos a fin de comprobar la directiva de ejecución actual y establecer la directiva de ejecución en `AllSigned`.

1. Creación de la implementación de IoT Edge para Linux en Windows.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >De forma predeterminada, el comando `Deploy-Eflow` crea la máquina virtual Linux con 1 GB de RAM, 1 núcleo de vCPU y 16 GB de espacio en disco. Sin embargo, los recursos que necesita la máquina virtual dependen en gran medida de las cargas de trabajo que implemente. Si la máquina virtual no tiene memoria suficiente para dar servicio a las cargas de trabajo, no se iniciará.
   >
   >Puede personalizar los recursos disponibles de la máquina virtual mediante los parámetros opcionales del comando `Deploy-Eflow`.
   >
   >Por ejemplo, el comando siguiente crea una máquina virtual con 4 núcleos de vCPU, 4 GB de RAM y 20 GB de espacio en disco:
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md).

   Para usar un acceso directo de GPU, deberá agregar los parámetros **gpuName**, **gpuPassthroughType** y **gpuCount** en el comando `Deploy-Eflow`. Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Escriba "Y" para aceptar los términos de licencia.

1. Escriba "O" o "R" para activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Una vez completada la implementación, la ventana de PowerShell indica **Implementación correcta**.

   ![Una implementación correcta indicará "Implementación correcta" al final de los mensajes, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-powershell-deployment.png)

Una vez completada la implementación, está listo para aprovisionar el dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>La extensión de Azure IoT Edge para Windows Admin Center está actualmente en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Los procesos de instalación y administración pueden ser diferentes de los de las características disponibles con carácter general.

En la página de inicio de Windows Admin Center, en la lista de conexiones, verá una conexión de host local que representa el equipo en el que se ejecuta Windows Admin Center. Los servidores, equipos o clústeres adicionales que administre también aparecerán aquí.

Puede usar Windows Admin Center para instalar y administrar Azure IoT Edge para Linux en Windows tanto en el dispositivo local como en los dispositivos administrados remotos. En esta guía, la conexión de host local actuará como el dispositivo de destino para la implementación de Azure IoT Edge para Linux en Windows.

Si quiere realizar la implementación en un dispositivo de destino remoto en lugar de en el dispositivo local y no ve el dispositivo de destino deseado en la lista, siga las [instrucciones para agregar el dispositivo](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Panel inicial de Windows Admin Center con el dispositivo de destino en la lista, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

1. Seleccione **Agregar**.

1. En el panel **Agregar o crear recursos**, busque el elemento **Azure IoT Edge**. Seleccione **Crear nuevo** para instalar una nueva instancia de Azure IoT Edge para Linux en Windows en un dispositivo.

   Si ya tiene una instancia de IoT Edge para Linux en Windows ejecutándose en un dispositivo, puede seleccionar **Agregar** para conectarse a ese dispositivo de IoT Edge existente y administrarlo con Windows Admin Center.

   ![Seleccione "Crear nuevo" en el elemento Azure IoT Edge de Windows Admin Center, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/resource-creation-tiles.png)

1. Se abrirá el panel **Crear una implementación de Azure IoT Edge para Linux en Windows**. En la pestaña **1. Getting Started** (1. Tareas iniciales), revise los requisitos mínimos y seleccione **Siguiente**.

1. Revise los términos de licencia, marque la casilla **Acepto** y seleccione **Siguiente**.

1. Puede activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Seleccione **Siguiente: Implementación**.

   ![Seleccione el botón "Siguiente: Implementación" después de activar o desactivar los datos de diagnóstico opcionales según sus preferencias, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-next-deploy.png)

1. En la pestaña **2. Implementar**, en **Seleccione un dispositivo de destino**, haga clic en su dispositivo para validar que cumple los requisitos mínimos. Una vez que su estado se confirme como compatible, seleccione **Siguiente**.

   ![Seleccione el dispositivo para comprobar si es compatible, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/evaluate-supported-device.png)

1. En la pestaña **Configuración 2.2**, revise los valores de configuración de la implementación.

   >[!NOTE]
   >IoT Edge para Linux en Windows usa un conmutador predeterminado, que asigna a la máquina virtual Linux una dirección IP interna. No se puede acceder a esta dirección IP interna desde fuera de la máquina virtual de Windows. Puede conectar localmente con la máquina virtual mientras tenga la sesión iniciada en la máquina virtual de Windows.
   >
   >Si usa Windows Server, configure un conmutador predeterminado antes de implementar IoT Edge para Linux en Windows.

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md). Si va a instalar estos requisitos previos en este momento del proceso de implementación, tendrá que empezar de nuevo desde el principio.

   Hay dos opciones disponibles para el acceso directo de GPU: **asignación directa de dispositivos (DDA)** y **paravirtualización de GPU (GPU-PV)** , en función del adaptador de GPU que asigne a la implementación. A continuación se muestran ejemplos de cada método.

   Para el método de asignación directa de dispositivos, seleccione el número de procesadores de GPU que se asignarán a la máquina virtual Linux.

   ![Opciones de configuración con la asignación directa de dispositivos de GPU habilitada, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   Para el método de paravirtualización, no se necesita ninguna configuración adicional.

   ![Opciones de configuración con la paravirtualización de GPU habilitada, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Cuando esté satisfecho con la configuración, seleccione **Siguiente**.

1. En la pestaña **2.3 Implementación**, puede ver el progreso de la implementación. El proceso completo incluye la descarga del paquete de Azure IoT Edge para Linux en Windows, la instalación del paquete, la configuración del dispositivo host y la configuración de la máquina virtual Linux. Este proceso puede tardar varios minutos en completarse. A continuación se muestra una implementación correcta.

   ![Una implementación correcta mostrará todos los pasos con una marca de verificación verde y la etiqueta "Completado", PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-deployment.png)

Una vez completada la implementación, está listo para aprovisionar el dispositivo. Seleccione **Siguiente: Conectar** para ir a la pestaña **3. Conectar**, que controla el aprovisionamiento de dispositivos de Azure IoT Edge.

---

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio Device Provisioning y a IoT Hub.

Tenga lista la siguiente información:

* El valor de **Ámbito de id.** del DPS. Puede recuperar este valor de la página Información general de la instancia de DPS en Azure Portal.
* El archivo de cadena de certificados de identidad del dispositivo en el dispositivo.
* El archivo de clave de identidad del dispositivo en el dispositivo.

Puede usar PowerShell o Windows Admin Center para aprovisionar el dispositivo de IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para PowerShell, ejecute el siguiente comando con los valores de marcador de posición actualizados con sus propios valores:

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Para Windows Admin Center, siga los pasos a continuación:

1. En el panel **Azure IoT Edge device provisioning** (Aprovisionamiento de dispositivos Azure IoT Edge), seleccione **X509 Certificate (DPS)** (Certificado X509 [DPS]) en el menú desplegable de método de aprovisionamiento.

1. Proporcione el identificador de ámbito de DPS, el identificador de registro del dispositivo y la clave principal de inscripción o la clave derivada en los campos de Windows Admin Center.

1. Elija **Provisioning with the selected method** (aprovisionar con el método seleccionado).

   ![Elija aprovisionar con el método seleccionado después de rellenar los campos obligatorios para el aprovisionamiento de claves simétricas, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. Una vez completado el aprovisionamiento, seleccione **Finalizar**. Volverá al panel principal. Ahora debería mostrarse un nuevo dispositivo, cuyo tipo es `IoT Edge Devices`. Puede seleccionar el dispositivo de IoT Edge para conectarse a él. Una vez en la página **Información general** del dispositivo, puede ver los valores de **IoT Edge Module List** (lista de módulos de IoT Edge) y **IoT Edge Status** (estado de IoT Edge) del dispositivo.

---

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Inicie sesión en la instancia de IoT Edge para Linux en la máquina virtual Windows con el siguiente comando en la sesión de PowerShell:

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >La única cuenta que se permite a SSH en la máquina virtual es la del usuario que la creó.

1. Una vez que haya iniciado sesión, puede comprobar la lista de módulos de IoT Edge en ejecución con el comando de Linux siguiente:

   ```bash
   sudo iotedge list
   ```

1. Si tiene que solucionar problemas del servicio IoT Edge, use los siguientes comandos de Linux.

    1. Si necesita solucionar problemas del servicio, recupere los registros del servicio.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Use la herramienta `check` para comprobar el estado de la configuración y la conexión del dispositivo.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Seleccione el dispositivo de IoT Edge en la lista de dispositivos conectados de Windows Admin Center para conectarse a él.

1. En la página Información general del dispositivo se muestran algunos datos sobre el dispositivo:

   * En la sección **IoT Edge Module List** (lista de módulos de IoT Edge) se muestran los módulos en ejecución en el dispositivo. Cuando el servicio de IoT Edge se inicia por primera vez, solo verá la ejecución del módulo **edgeAgent**. El módulo edgeAgent se ejecuta de forma predeterminada y le ayuda a instalar e iniciar todos los módulos adicionales que implemente en el dispositivo.

   * En la sección **IoT Edge Status** (estado de IoT Edge) se muestra el estado del servicio y debe aparecer como **activo (en ejecución)** .

---

Al crear un nuevo dispositivo IoT Edge, se mostrará el código de estado `417 -- The device's deployment configuration is not set` en Azure Portal. Este estado es normal y significa que el dispositivo está listo para recibir una implementación de módulo.

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).

También puede:

* Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
* Obtenga información acerca de cómo [administrar certificados en IoT Edge para Linux en máquinas virtuales de Windows](how-to-manage-device-certificates.md) y transferir archivos desde el sistema operativo host a la máquina virtual de Linux.
* Aprenda a [configurar los dispositivos de IoT Edge para que se comuniquen a través de un servidor proxy](how-to-configure-proxy-support.md).
