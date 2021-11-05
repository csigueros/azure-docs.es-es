---
title: 'Inicio rápido: Creación de un recurso de dispositivo para Azure Network Function Manager'
description: En este inicio rápido, aprenderá a crear un recurso de dispositivo para Azure Network Function Manager.
author: prmitt
ms.service: network-function-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2ab440166ba7e5b27f07880c5ac0cb7fef27466a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070265"
---
# <a name="quickstart-create-a-network-function-manager-device-resource"></a>Inicio rápido: Creación de un recurso de dispositivo para Network Function Manager

En este inicio rápido, aprenderá a crear un recurso de **dispositivo** para Azure Network Function Manager (NFM). El recurso de dispositivo de Network Function Manager está vinculado al recurso de Azure Stack Edge. El recurso de dispositivo agrega todas las funciones de red implementadas en un dispositivo Azure Stack Edge y proporciona servicios comunes para la implementación, supervisión, solución de problemas y operaciones de administración coherentes para todas las funciones de red implementadas en Azure Stack Edge. Debe crear el recurso de dispositivo de Network Function Manager para poder implementar funciones de red en el dispositivo Azure Stack Edge.

## <a name="prerequisites"></a><a name="pre"></a>Requisitos previos

Verifique los requisitos previos siguientes:

* Compruebe que tiene una cuenta de Azure con una suscripción activa. Si no tiene una cuenta, puede [crearla de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Cumple todos los requisitos previos enumerados en el artículo sobre [requisitos previos y otras exigencias](requirements.md).
* Tiene asignados los permisos adecuados. Para más información, consulte [Permisos y registro del proveedor de recursos](resources-permissions.md).
* Revise la sección  [Disponibilidad de la región](overview.md#regions)  antes de crear un recurso de dispositivo.
* Compruebe que puede conectarse de forma remota desde un cliente Windows al dispositivo GPU de Azure Stack Edge Pro a través de PowerShell. Para más información, consulte el artículo [Conexión a la interfaz de PowerShell](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="create-a-device-resource"></a><a name="create"></a>Creación de un recurso de dispositivo

Si tiene un recurso de dispositivo existente de Azure Network Function Manager, no es necesario crear uno. Omita esta sección y vaya a la sección de [clave de registro](#key).

Para crear un recurso de **dispositivo**, siga estos pasos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con las credenciales de Microsoft Azure.

1. En la pestaña **Aspectos básicos**, configure **Detalles del proyecto** y **Detalles de la instancia** en las opciones de configuración del dispositivo.
   :::image type="content" source="./media/create-device/device-settings.png" alt-text="Captura de pantalla de la configuración del dispositivo.":::

   Cuando rellene los campos, aparecerá una marca de verificación verde cuando se validen los caracteres que agregue. Algunos detalles se rellenan automáticamente, mientras que otros son campos personalizables:

   * **Suscripción**: compruebe que la suscripción que aparece en la lista es la correcta. Puede cambiar las suscripciones mediante la lista desplegable.
   * **Grupo de recursos:** seleccione uno existente o haga clic en **Crear** para crear uno nuevo.
   * **Región**: seleccione la ubicación del dispositivo. Debe ser una región admitida. Para obtener más información, consulte [Disponibilidad en regiones](overview.md#regions).
   * **Nombre:** escriba el nombre del dispositivo.
   * **Azure Stack Edge:** seleccione el Azure Stack Edge que desea registrar como dispositivo para Azure Network Function Manager. El recurso de ASE debe tener el estado **En línea** para que un recurso de dispositivo se cree correctamente. Para comprobar el estado del recurso de ASE, vaya a la sección **Propiedades** de la página del recurso de Azure Stack Edge.
1. Seleccione **Revisar y crear** para validar la configuración del dispositivo.
1. Una vez validada la configuración, seleccione **Crear**.
   
   >[!NOTE]
   >El recurso de dispositivo de Network Function Manager puede estar vinculado a un solo recurso de Azure Stack Edge. Tendrá que crear un recurso de dispositivo NFM independiente para cada recurso de Azure Stack Edge.
   >

## <a name="get-the-registration-key"></a><a name="key"></a>Obtención de la clave de registro

1. Una vez que el dispositivo se haya aprovisionado correctamente, vaya al grupo de recursos en el que se implementa el recurso del dispositivo.
1. Haga clic en el recurso del **dispositivo**. Para obtener la clave de registro, haga clic en **Obtener clave de registro**. Asegúrese de que tiene los permisos adecuados para generar una clave de registro. Para más información, consulte el artículo [Permisos y registro del proveedor de recursos](resources-permissions.md).

   :::image type="content" source="./media/create-device/register-device.png" alt-text="Captura de pantalla de la clave de registro." lightbox="./media/create-device/register-device.png":::
1. Anote la clave de registro del dispositivo, que se usará en los pasos siguientes.

   > [!IMPORTANT]
   > La clave de registro caduca 24 horas después de generarse. Si la clave de registro ya no está activa, puede generar una nueva clave de registro si repite los pasos de esta sección.
   >

## <a name="register-the-device"></a><a name="registration"></a>Registro del dispositivo

Siga estos pasos para registrar el recurso de dispositivo con Azure Stack Edge.

1. Para registrar el dispositivo con la clave de registro obtenida en el paso anterior, [conéctese al dispositivo Azure Stack Edge a través de Windows PowerShell](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Una vez que tenga una conexión de PowerShell (minishell) al dispositivo, escriba el siguiente comando con la clave de registro del dispositivo que obtuvo en el paso anterior como parámetro:
   ```powershell
   Invoke-MecRegister <device-registration-key>
   ```

1. Compruebe que el recurso de dispositivo tiene **Estado del dispositivo = registrado**.

   :::image type="content" source="./media/create-device/device-registered.png" alt-text="Captura de pantalla del dispositivo registrado." lightbox="./media/create-device/device-registered.png":::
 
## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de una función de red](deploy-functions.md)
