---
title: 'Tutorial: Creación de un recurso de dispositivo para Azure Network Function Manager'
description: En este tutorial, aprenderá cómo crear un recurso de dispositivo para Azure Network Function Manager.
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 048b086c407f5fbdc6239aec9dc509aceda7c8c2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468195"
---
# <a name="tutorial-create-a-network-function-manager-device-resource-preview"></a>Tutorial: Creación de un recurso de dispositivo para Network Function Manager (versión preliminar)

En este tutorial, aprenderá cómo crear un recurso de **dispositivo** para Azure Network Function Manager (NFM). El recurso de dispositivo de Network Function Manager está vinculado al recurso de Azure Stack Edge. El recurso de dispositivo agrega todas las funciones de red implementadas en un dispositivo Azure Stack Edge y proporciona servicios comunes para la implementación, supervisión, solución de problemas y operaciones de administración coherentes para todas las funciones de red implementadas en Azure Stack Edge. Debe crear el recurso de dispositivo de Network Function Manager para poder implementar funciones de red en el dispositivo Azure Stack Edge.

En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Comprobar los requisitos previos
> * Creación de un recurso de dispositivo
> * Obtener una clave de registro
> * Registro del dispositivo

## <a name="prerequisites"></a><a name="pre"></a>Requisitos previos

Verifique los requisitos previos siguientes:

* Ha completado todos los requisitos previos enumerados en el artículo [Información general](overview.md#prereq).
* Tiene asignados los permisos adecuados. Para más información, consulte [Permisos y registro del proveedor de recursos](overview.md#permissions).
* Revise la sección  [Disponibilidad de la región](overview.md#regions)  antes de crear un recurso de dispositivo.
* Compruebe que puede conectarse de forma remota desde un cliente Windows al dispositivo GPU de Azure Stack Edge Pro a través de PowerShell. Para más información, consulte el artículo [Conexión a la interfaz de PowerShell](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="create-a-device-resource"></a><a name="create"></a>Creación de un recurso de dispositivo

Si tiene un recurso de dispositivo existente de Azure Network Function Manager, no es necesario crear uno. Omita esta sección y vaya a la sección de [clave de registro](#key).

Para crear un recurso de **dispositivo**, siga estos pasos.

1. Inicie sesión en el [Portal Azure de versión preliminar](https://aka.ms/AzureNetworkFunctionManager) con sus credenciales de Microsoft Azure.

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
1. Haga clic en el recurso del **dispositivo**. Para obtener la clave de registro, haga clic en **Obtener clave de registro**. Asegúrese de que tiene los permisos adecuados para generar una clave de registro. Para más información, consulte [Permisos](overview.md#permissions).

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
