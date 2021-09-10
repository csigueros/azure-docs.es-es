---
title: 'Administración de grupos de aplicaciones para Azure Virtual Desktop: Azure'
description: Cómo administrar grupos de aplicaciones de Azure Virtual Desktop con PowerShell o la CLI de Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f323819492fe89f7742c6b218afa4d2e1bf1b6c0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103935"
---
# <a name="manage-app-groups-using-powershell-or-the-azure-cli"></a>Administración de grupos de aplicaciones mediante PowerShell o la CLI de Azure

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

El grupo de aplicaciones predeterminado creado para un nuevo grupo de hosts de Azure Virtual Desktop también publica el escritorio completo. Además, puede crear uno o varios grupos de aplicaciones de RemoteApp para el grupo host. Siga este tutorial para crear un grupo de aplicaciones de RemoteApp y publicar aplicaciones individualesl de menú **Inicio**.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de RemoteApp
> * Conceder acceso a programas de RemoteApp.

## <a name="prerequisites"></a>Prerrequisitos

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Este artículo supone que ha seguido las instrucciones que se indican en [Configuración del módulo de PowerShell](powershell-module.md) para configurar el módulo de PowerShell e iniciar sesión en su cuenta de Azure.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

En este artículo se da por supuesto que ya ha configurado el entorno para el CLI de Azure y que ha iniciado sesión en su cuenta de Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="create-a-remoteapp-group"></a>Creación de un grupo de RemoteApp

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para crear un grupo de RemoteApp con PowerShell:

1. Ejecute el siguiente cmdlet de PowerShell para crear un grupo de aplicaciones de RemoteApp vacío.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Opcional) Para comprobar que se creó el grupo de aplicaciones, puede ejecutar el siguiente cmdlet para ver una lista de todos los grupos de aplicaciones del grupo host.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Ejecute el siguiente cmdlet para obtener una lista de aplicaciones del menú **Inicio** en la imagen de máquina virtual del grupo host. Anote los valores de **FilePath**, **IconPath**, **IconIndex** y otra información importante para la aplicación que quiere publicar.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   La salida debería mostrar todos los elementos del menú Inicio en un formato similar al siguiente:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Ejecute el siguiente cmdlet para instalar la aplicación según `AppAlias`. `AppAlias` se vuelve visible cuando se ejecuta la salida del paso 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (Opcional) Ejecute el siguiente cmdlet para publicar un nuevo programa de RemoteApp en el grupo de aplicaciones creado en el paso 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Para comprobar que se publicó la aplicación, ejecute el siguiente cmdlet.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Repita los pasos del 1 al 5 para cada aplicación de este grupo de aplicaciones que quiera publicar.
8. Ejecute el siguiente cmdlet para conceder a los usuarios acceso a los programas de RemoteApp del grupo de aplicaciones.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

> [!NOTE]
> La CLI de Azure no proporciona comandos para obtener aplicaciones del menú Inicio, ni para crear un nuevo programa RemoteApp o publicarlo en el grupo de aplicaciones. Usar Azure PowerShell.

Para crear un grupo de RemoteApp con la CLI de Azure:

1. Use el comando [az desktopvirtualization applicationgroup create](/cli/azure/desktopvirtualization##az_desktopvirtualization_applicationgroup_create) para crear un grupo de aplicaciones remotas:

   ```azurecli
   az desktopvirtualization applicationgroup create --name "MyApplicationGroup" \
      --resource-group "MyResourceGroup" \
      --location "MyLocation" \
      --application-group-type "RemoteApp" \
      --host-pool-arm-path "/subscriptions/MySubscriptionGUID/resourceGroups/MyResourceGroup/providers/Microsoft.DesktopVirtualization/hostpools/MyHostPool"
      --tags tag1="value1" tag2="value2" \
      --friendly-name "Friendly name of this application group" \
      --description "Description of this application group" 
   ```
    
2. (Opcional) Para comprobar que se creó el grupo de aplicaciones, puede ejecutar el siguiente comando para ver una lista de todos los grupos de aplicaciones del grupo de hosts.

   ```azurecli
   az desktopvirtualization applicationgroup list \
      --resource-group "MyResourceGroup"
   ```
---

## <a name="next-steps"></a>Pasos siguientes

Si llegó a esta guía de procedimientos desde nuestros tutoriales, consulte [Creación de un grupo de hosts para validar las actualizaciones de servicio](create-validation-host-pool.md). Puede usar un grupo de hosts de validación para supervisar las actualizaciones del servicio antes de implementarlas en el entorno de producción.