---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3fae7fc37b010a754b7fe1dd9b6e7b092ae534e8
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576852"
---
### <a name="iot-edge-for-linux-on-windows-installation"></a>Instalación de IoT Edge para Linux en Windows

Un dispositivo Windows con los siguientes requisitos mínimos:

* Requisitos del sistema
   * Windows 10¹/11 (Pro, Enterprise, IoT Enterprise)
   * Windows Server 2019¹/2022  
   <sub>¹ Windows 10 y Windows Server 2019 (compilación mínima 17763) con todas las actualizaciones acumulativas actuales instaladas</sub>.

* Requisitos de hardware
  * Memoria libre mínima: 1 GB
  * Espacio libre en disco mínimo: 10 GB

* Compatibilidad con la virtualización
  * En Windows 10, habilite Hyper-V. Para obtener más información, vea [Información de Hyper-V en Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
  * En Windows Server, instale el rol de Hyper-V y cree un conmutador de red predeterminado. Para más información, consulte [Virtualización anidada en Azure IoT Edge para Linux en Windows](../articles/iot-edge/nested-virtualization.md).
  * En una máquina virtual, configure la virtualización anidada. Para obtener más información, consulte [Virtualización anidada](../articles/iot-edge/nested-virtualization.md).

* Soporte de red
  * Windows Server no incluye un conmutador predeterminado. Para poder implementar EFLOW en un dispositivo Windows Server, debe crear un conmutador virtual.  Para obtener más información, consulte [Creación de un conmutador virtual para Linux en Windows](../articles/iot-edge/how-to-create-virtual-switch.md).
  * Las versiones de escritorio de Windows incluyen un conmutador predeterminado que se puede usar para la instalación de EFLOW. Si es necesario, puede crear su propio conmutador virtual personalizado.

> [!TIP]
> Si quiere usar **módulos de Linux con aceleración por GPU** en la implementación de Azure IoT Edge para Linux en Windows, debe tener en cuenta varias opciones de configuración.
>
> Tendrá que instalar los controladores correctos en función de la arquitectura de GPU y es posible que necesite acceder a una compilación del programa Windows Insider. Para determinar sus necesidades de configuración y cumplir estos requisitos previos, consulte [Aceleración por GPU para Azure IoT Edge para Linux en Windows](../articles/iot-edge/gpu-acceleration.md).
>
> Asegúrese de que se toma el tiempo necesario para cumplir los requisitos previos para la aceleración de GPU. Deberá reiniciar el proceso de instalación si decide realizar aceleración de GPU durante la instalación.

Puede usar tanto **PowerShell** como **Windows Admin Center** para gestionar el dispositivo IoT Edge. Cada utilidad tiene sus propios requisitos previos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si desea usar PowerShell, siga estos pasos para preparar el dispositivo de destino para la instalación de Azure IoT Edge para Linux en Windows y la implementación de la máquina virtual Linux:

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned`. Puede comprobar la directiva de ejecución actual en un símbolo del sistema de PowerShell con privilegios elevados con el siguiente comando:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la directiva de ejecución de `local machine` no es `AllSigned`, puede establecer la directiva de ejecución con el siguiente comando:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Para obtener más información sobre el módulo de PowerShell de Azure IoT Edge para Linux en Windows, consulte la [referencia de funciones de PowerShell](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si desea usar Windows Admin Center, siga estos pasos para descargar e instalar Windows Admin Center e instalar la extensión Windows Admin Center Azure IoT Edge:

   1. Descargue y ejecute el [instalador de Windows Admin Center](https://aka.ms/wacdownload). Siga las indicaciones del Asistente para la instalación a fin de instalar Windows Admin Center.

   1. Una vez instalado, use un explorador compatible para abrir Windows Admin Center. Entre los exploradores admitidos se incluyen Microsoft Edge (Windows 10, versión 1709 o posterior), Google Chrome y Microsoft Edge Insider.

   1. Al usar Windows Admin Center por primera vez, se le pedirá que seleccione un certificado para usarlo. Seleccione **Cliente de Windows Admin Center** como certificado.

   1. Instale la extensión de Azure IoT Edge. Seleccione el icono de engranaje en la parte superior derecha del panel de Windows Admin Center.

      ![Seleccione el icono de engranaje en la parte superior derecha del panel para acceder a la configuración, PNG.](../articles/iot-edge/media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. En el menú **Configuración**, en **Puerta de enlace**, seleccione **Extensiones**.

   1. En la pestaña **Extensiones disponibles**, busque **Azure IoT Edge** en la lista de extensiones. Esta dicha opción y seleccione la indicación **Instalar** sobre la lista de extensiones.

   1. Una vez finalizada la instalación, debería ver Azure IoT Edge en la lista de extensiones instaladas en la pestaña **Extensiones instaladas**.

---
