---
title: 'Aprovisionamiento automático de dispositivos Windows con DPS y TPM: Azure IoT Edge | Microsoft Docs'
description: Uso del aprovisionamiento automático de dispositivos con IoT Edge para Linux en Windows con Device Provisioning Service y atestación de TPM
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 03ed23fbc22c632e3bc9a0e5afeb1163e7f99c80
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278507"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-with-tpm-attestation"></a>Creación y aprovisionamiento de un dispositivo IoT Edge para Linux en Windows con atestación de TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Los dispositivos Azure IoT Edge se pueden aprovisionar con [Device Provisioning Service](../iot-dps/index.yml), exactamente del mismo modo que los dispositivos que no están habilitados para Edge. Si no está familiarizado con el proceso de aprovisionamiento automático, revise la información general sobre el [aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

DPS admite atestación de módulo de plataforma segura (TPM) con dispositivos IoT Edge solo en el caso de inscripción individual, no de inscripción de grupo.

En este artículo se muestra cómo usar el aprovisionamiento automático en un dispositivo que ejecuta IoT Edge para Linux en Windows mediante los siguientes pasos:

* Instalación de IoT Edge para Linux en Windows
* Recuperación de la información de TPM del dispositivo
* Cree una inscripción individual para el dispositivo.
* Aprovisionamiento del dispositivo con su información de TPM

## <a name="prerequisites"></a>Requisitos previos

* Un dispositivo Windows. Para obtener información sobre las versiones de Windows compatibles, consulte [Sistemas operativos](support.md#operating-systems).
* Un centro de IoT activo.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, siga las instrucciones de [Configuración de Azure IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando aprovisione el dispositivo IoT Edge.

> [!NOTE]
> Se necesita la versión TPM 2.0 al usar la atestación de TPM con DPS y solo se puede usar para crear inscripciones individuales, no de grupo.

## <a name="install-iot-edge-for-linux-on-windows"></a>Instalación de IoT Edge para Linux en Windows

Los pasos de instalación incluidos en esta sección se han abreviado para resaltar los específicos del escenario de aprovisionamiento de TPM. Para obtener instrucciones más detalladas, incluidos los requisitos previos y los pasos de instalación remota, consulte [Instalación y aprovisionamiento de un dispositivo remoto Azure IoT Edge para Linux en Windows](how-to-install-iot-edge-on-windows.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Abra una sesión de PowerShell con privilegios elevados en el dispositivo Windows.

1. Descargue IoT Edge for Linux en Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Instale IoT Edge para Linux en Windows en el dispositivo.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. Para que la implementación se ejecute correctamente, debe establecer la directiva de ejecución en el dispositivo en `AllSigned` si aún no está establecida de esta manera.

   1. Consulte la directiva de ejecución actual.

      ```powershell
      Get-ExecutionPolicy -List
      ```

   1. Si la directiva de ejecución de `local machine` no es `AllSigned`, actualícela.

      ```powershell
      Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
      ```

1. Implemente IoT Edge para Linux en Windows.

   ```powershell
   Deploy-Eflow
   ```

1. Escriba `Y` para aceptar los términos de licencia.

1. Escriba `O` o `R` para activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. La salida mostrará **Implementación correcta** una vez que IoT Edge para Linux en Windows se haya implementado correctamente en el dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>La extensión de Azure IoT Edge para Windows Admin Center está actualmente en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Los procesos de instalación y administración pueden ser diferentes de los de las características disponibles con carácter general.

1. Tenga Windows Admin Center configurado con la extensión **Azure IoT Edge**.

1. En la página de conexiones de Windows Admin Center, seleccione **Agregar**.

1. En el panel **Agregar o crear recursos**, busque el icono **Azure IoT Edge**. Seleccione **Crear nuevo** para instalar una nueva instancia de Azure IoT Edge para Linux en Windows en un dispositivo.

1. Siga los pasos del Asistente para la implementación para instalar y configurar IoT Edge para Linux en Windows.

   1. En los pasos de **Introducción**, revise los requisitos previos, acepte los términos de licencia y elija si desea enviar o no datos de diagnóstico.

   1. En los pasos de **Implementación**, elija el dispositivo y las opciones de configuración. A continuación, observe el progreso de la implementación de IoT Edge en el dispositivo.

   1. Seleccione **Siguiente** para avanzar al paso **Conexión**, donde proporcionará la información de aprovisionamiento con relación al dispositivo.

1. Deje de seguir el Asistente para la implementación en la página **Aprovisionamiento**, ya que, antes de continuar, debe seguir varios pasos con el fin de preparar el dispositivo para el aprovisionamiento con el TPM. Deje abierta la ventana de Windows Admin Center, ya que volverá a ella al final de este artículo para completar los pasos de aprovisionamiento.

---

## <a name="enable-tpm-passthrough"></a>Habilitación del acceso directo del TPM

La máquina virtual IoT Edge para Linux en Windows tiene una característica de TPM que se puede habilitar o deshabilitar. (Está deshabilitada de manera predeterminada). Cuando esta característica está habilitada, la máquina virtual puede acceder al TPM de la máquina host.

1. Abra PowerShell en una sesión con privilegios elevados.

1. Si aún no lo ha hecho, establezca la directiva de ejecución de su dispositivo en `AllSigned` para que pueda ejecutar las funciones de PowerShell de IoT Edge para Linux en Windows.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Active la característica de TPM.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>Recuperación de la información de TPM del dispositivo

Para aprovisionar su dispositivo, debe recopilar información del chip de TPM y proporcionarla a su instancia de Device Provisioning Service (DPS) para que el servicio pueda reconocer el dispositivo cuando este intente conectarse.

Primero, debe determinar la **clave de aprobación**, que es única para cada chip del TPM y se obtiene del fabricante asociado a este. A continuación, debe proporcionar un **Id. de registro** único para el dispositivo. Puede derivar un Id. de registro único para el dispositivo del TPM, por ejemplo, creando un código hash SHA-256 de la clave de aprobación.

IoT Edge para Linux en Windows proporciona una función de PowerShell para ayudar a recuperar esta información del TPM. Para usar el script, siga estos pasos en el dispositivo:

1. Abra PowerShell en una sesión con privilegios elevados.

1. Ejecute el comando.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

### <a name="simulate-a-tpm-optional"></a>Simulación de un TPM (opcional)

Si no dispone de un TPM físico y desea probar este método de aprovisionamiento, puede simular uno en su dispositivo.

IoT Hub Device Provisioning Service proporciona ejemplos que simulan un TPM y le devuelven la clave de aprobación y el Id. de registro.

1. Elija uno de los ejemplos de la siguiente lista en función del idioma que prefiera.
1. Deje de seguir los pasos de ejemplo de DPS una vez que el TPM simulado esté en ejecución y usted haya recopilado la clave de aprobación y el Id. de registro. No presione *Entrar* para ejecutar el registro en la aplicación de ejemplo.
1. Mantenga en ejecución la ventana que hospeda el TPM simulado hasta que haya terminado de probar este escenario.
1. Vuelva a este artículo para crear una inscripción de DPS y configurar su dispositivo.

Para aprender a crear una inscripción individual para un dispositivo TPM simulado, consulte la [guía de inicio rápido de aprovisionamiento de un dispositivo TPM simulado](../iot-dps/quick-create-simulated-device-tpm.md).


## <a name="create-a-device-enrollment-entry"></a>Creación de una entrada de inscripción de dispositivo

Device Provisioning Service admite dos tipos de inscripciones: Los grupos de inscripción se usan para inscribir varios dispositivos relacionados. Las inscripciones individuales se usan para inscribir un único dispositivo. La atestación de TPM solo admite inscripciones individuales porque un grupo de dispositivos no puede compartir información de TPM. Por lo tanto, en este artículo se muestran las inscripciones individuales.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a su instancia de Device Provisioning Service.

1. En el menú de Device Provisioning Service, seleccione **Administrar inscripciones**. Seleccione el botón **Agregar inscripción individual** situado en la parte superior.

1. En el panel **Agregar inscripción**, escriba la siguiente información:

   * Seleccione **TPM** como mecanismo de atestación de identidad.
   * Indique el Id. de registro y la clave de aprobación del dispositivo de TPM que ha anotado anteriormente.
   * Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
   * Si lo desea, escriba un Id. de dispositivo único. Si decide escribir un nombre, se usará en su lugar el identificador de registro para identificar el dispositivo.
   * Seleccione **True** para declarar que la inscripción es para un dispositivo IoT Edge.

1. Seleccione **Guardar** para guardar la inscripción individual.

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Abra una sesión de PowerShell con privilegios elevados en el dispositivo Windows.

1. Aprovisione el dispositivo usando el **Id. de ámbito** que ha recopilado de su instancia de Device Provisioning Service.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. En el paso **Conexión**, aprovisione el dispositivo.

   1. Seleccione el método de aprovisionamiento **DpsTpm**.
   1. Proporcione el **Id. de ámbito** que recupera de su instancia de Device Provisioning Service.

   ![Aprovisionamiento de su dispositivo con DPS y atestación de TPM](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Seleccione **Aprovisionamiento con el método seleccionado**.

1. Una vez que IoT Edge se haya instalado y aprovisionado correctamente en el dispositivo, seleccione **Finalizar** para salir del Asistente para la implementación.

---

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

1. Inicie sesión en la máquina virtual IoT Edge para Linux en Windows usando el siguiente comando en la sesión de PowerShell:

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

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Obtenga información sobre la [Implementación y supervisión de módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o [mediante la CLI de Azure](how-to-deploy-cli-at-scale.md)
