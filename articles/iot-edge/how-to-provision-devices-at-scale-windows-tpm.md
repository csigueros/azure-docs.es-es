---
title: 'Aprovisionamiento de dispositivos con un TPM virtual en Windows: Azure IoT Edge | Microsoft Docs'
description: Utilice un TPM simulado en un dispositivo Windows para probar Azure Device Provisioning Service para Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f130adffa4f5d542082b3ab6271952a867c30cdb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661682"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM en Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo, se proporcionan instrucciones completas para el aprovisionamiento automático de un dispositivo IoT Edge con Windows mediante un Módulo de plataforma segura (TPM). Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

En este artículo, se describen dos metodologías. Seleccione sus preferencias en función de la arquitectura de la solución:

1. Aprovisionamiento automático de un dispositivo Windows con hardware de TPM físico.
1. Aprovisione automáticamente un dispositivo Windows que ejecute un TPM simulado. Esta metodología solo se recomienda como escenario de prueba, porque un TPM simulado no ofrece la misma seguridad que un TPM físico.

Las instrucciones difieren en función de la metodología, por lo que debe asegurarse de que se encuentra en la pestaña correcta en adelante.

Las tareas son las siguientes:

# <a name="physical-tpm"></a>[TPM físico](#tab/physical-tpm)

* Recupere la información de aprovisionamiento del dispositivo.
* Cree una inscripción individual para el dispositivo.
* Instale el entorno de ejecución de IoT Edge y conecte el dispositivo a IoT Hub.

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

* Configure el TPM simulado y recupere su información de aprovisionamiento.
* Cree una inscripción individual para el dispositivo.
* Instale el entorno de ejecución de IoT Edge y conecte el dispositivo a IoT Hub.

---

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos son los mismos para las soluciones de TPM físico y TPM virtual.

* Una máquina de desarrollo Windows. En este artículo se usa Windows 10.
* Una instancia de IoT Hub activa.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de una instancia de IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y a Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) del inicio rápido de IoT Hub Device Provisioning Service.
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

> [!NOTE]
> Es necesario TPM 2.0 cuando se usa la atestación de TPM con DPS.
>
> Solo puede crear inscripciones de DPS individuales, no de grupo, cuando se usa un TPM.

## <a name="set-up-your-tpm"></a>Configuración de TPM

# <a name="physical-tpm"></a>[TPM físico](#tab/physical-tpm)

En la máquina virtual, compile una herramienta que podrá utilizar para recuperar el **identificador de registro** y la **clave de aprobación** del TPM.

1. Siga los pasos de [Configuración de un entorno de desarrollo en Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) para instalar y compilar el SDK de dispositivo IoT de Azure para C.

1. Ejecute los comandos siguientes en una sesión de PowerShell con privilegios elevados para compilar la herramienta del SDK que recupera la información de aprovisionamiento de dispositivos desde el TPM.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. En la ventana de salida se muestra el **Id. de registro** y la **Clave de aprobación**. Copie estos valores para usarlos más tarde al crear una inscripción individual para el dispositivo en DPS.

> [!TIP]
> Si no desea usar la herramienta del SDK para recuperar la información, debe encontrar otra manera de obtener la información de aprovisionamiento. La **clave de aprobación**, que es única para cada chip de TPM y se obtiene del fabricante asociado a este. Puede derivar un único **identificador de registro** para el dispositivo TPM, por ejemplo, al aplicar un algoritmo hash SHA-256 a la clave de aprobación.

Una vez que tenga el identificador de registro y la clave de aprobación, estará listo para continuar.

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

Si no dispone de un TPM físico y desea probar este método de aprovisionamiento, puede simular uno en su dispositivo.

IoT Hub Device Provisioning Service proporciona ejemplos que simulan un TPM y devuelven la **clave de aprobación** y el **Id. de registro**.

1. Elija uno de los ejemplos de la siguiente lista en función del idioma que prefiera.
1. Deje de seguir los pasos de ejemplo de DPS una vez que el TPM simulado esté en ejecución y usted haya recopilado la **clave de aprobación** y el **Id. de registro**. No presione *Entrar* para ejecutar el registro en la aplicación de ejemplo.
1. Mantenga en ejecución la ventana que hospeda el TPM simulado hasta que haya terminado de probar este escenario.
1. Vuelva a este artículo para crear una inscripción de DPS y configurar su dispositivo.

Ejemplos de TPM simulado:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Recupere la información de aprovisionamiento del TPM y úsela para crear una inscripción individual en Device Provisioning Service.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), vaya a la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Agregar inscripción individual** y, a continuación, complete los pasos siguientes para configurar la inscripción:

   1. En **Mecanismo**, seleccione **TPM**.

   1. Proporcione la **clave de aprobación** y el **identificador de registro** que ha copiado de la máquina virtual o el dispositivo físico.

   1. Si lo desea, proporcione un identificador para el dispositivo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que esta máquina virtual o el dispositivo físico es un dispositivo IoT Edge.

   1. Elija el centro de IoT vinculado al que quiere conectar el dispositivo o seleccione **Link to new IoT Hub** (Vincular a un nuevo centro de IoT). Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   1. Agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Para más información, consulte [Implementación de módulos IoT Edge a escala](how-to-deploy-at-scale.md).

   1. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

En esta sección, preparará su máquina virtual Windows o dispositivo físico para IoT Edge. A continuación, instalará IoT Edge.

Hay un paso que debe completar en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita un motor de contenedor instalado.

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

1. Conozca el **Ámbito de identificador** de DPS y el **Identificador de registro** del dispositivo que se recopilaron en las secciones anteriores.

1. Abra una ventana de Azure PowerShell en modo de administrador. Asegúrese de usar una sesión de AMD64 de PowerShell para instalar IoT Edge, no PowerShell (x86).

1. El comando **Initialize-IoTEdge** configura el entorno de ejecución de Azure IoT Edge en el equipo. El comportamiento predeterminado del comando es el aprovisionamiento manual con contenedores de Windows. Utilice la marca `-Dps` para usar Device Provisioning Service, en lugar del aprovisionamiento manual.

   Reemplace los valores de marcador de posición para `{scope_id}` y `{registration_id}` con los datos que ha recopilado antes.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo. Use los siguientes comandos en el dispositivo para comprobar que el entorno de ejecución está instalado e iniciado correctamente.  

Compruebe el estado del servicio IoT Edge.

```powershell
Get-Service iotedge
```

Examine los registros de servicio de los últimos 5 minutos.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Enumere los módulos en ejecución.

```powershell
iotedge list
```

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Obtenga información sobre la [Implementación y supervisión de módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o [mediante la CLI de Azure](how-to-deploy-cli-at-scale.md)
