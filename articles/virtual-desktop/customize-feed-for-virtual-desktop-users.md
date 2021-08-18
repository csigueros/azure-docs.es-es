---
title: 'Personalización de la fuente para usuarios de Azure Virtual Desktop: Azure'
description: Personalización de la fuente para los usuarios de Azure Virtual Desktop con cmdlets de PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: d2a1e8df23bca2e57f2d26bd83fc06ad93847903
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113032946"
---
# <a name="customize-the-feed-for-azure-virtual-desktop-users"></a>Personalización de la fuente para usuarios de Azure Virtual Desktop

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

Puede personalizar la fuente para que RemoteApp y los recursos de escritorio remoto se muestren de manera reconocible para los usuarios.

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se da por supuesto que ya ha descargado e instalado el módulo de PowerShell para Azure Virtual Desktop. Si no lo ha hecho, siga las instrucciones que se indican en [Configuración del módulo de PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar el nombre para mostrar de RemoteApp

Para cambiar el nombre para mostrar de una instancia de RemoteApp publicada, defina un nombre descriptivo. De forma predeterminada, el nombre descriptivo es el mismo que el nombre del programa RemoteApp.

Para recuperar una lista de instancias de RemoteApp publicadas para un grupo de aplicaciones, ejecute el siguiente cmdlet de PowerShell:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Para asignar un nombre descriptivo a RemoteApp, ejecute el siguiente cmdlet con los parámetros necesarios:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Por ejemplo, supongamos que ha recuperado las aplicaciones actuales con el siguiente cmdlet de ejemplo:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

La salida debe ser similar a la siguiente:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Para actualizar el nombre descriptivo, ejecute este cmdlet:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Para confirmar que ha actualizado correctamente el nombre descriptivo, ejecute este cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

El cmdlet debe proporcionar la siguiente salida:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar el nombre para mostrar de Escritorio remoto

Para cambiar el nombre para mostrar de un escritorio remoto publicado, defina un nombre descriptivo. Si ha creado manualmente un grupo de hosts y un grupo de aplicaciones de escritorio mediante PowerShell, el nombre descriptivo predeterminado es "Session Desktop". Si ha creado un grupo de hosts y un grupo de aplicaciones de escritorio mediante la plantilla de Azure Resource Manager de GitHub o la oferta de Azure Marketplace, el nombre descriptivo predeterminado coincide con el nombre del grupo de hosts.

Para recuperar el recurso de escritorio remoto, ejecute el siguiente cmdlet de PowerShell:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Para asignar un nombre descriptivo al recurso de escritorio remoto, ejecute el siguiente cmdlet de PowerShell:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Personalización de un nombre para mostrar en Azure Portal

Para cambiar el nombre para mostrar de un escritorio remoto publicado, establezca un nombre descriptivo mediante Azure Portal.

1. Inicie sesión en Azure Portal en <https://portal.azure.com>.

2. Busque **Azure Virtual Desktop**.

3. En Servicios, seleccione **Azure Virtual Desktop**.

4. En la página Azure Virtual Desktop, seleccione **Grupos de aplicaciones** en el lado izquierdo de la pantalla y, a continuación, seleccione el nombre del grupo de aplicaciones que desea editar. (por ejemplo, si desea editar el nombre para mostrar del grupo de aplicaciones de escritorio, seleccione el grupo de aplicaciones denominado **Escritorio**.)

5. Seleccione **Aplicaciones** en el menú del lado izquierdo de la pantalla.

6. Seleccione la aplicación que desea actualizar y, a continuación, escriba un nuevo **nombre para mostrar**.

7. Seleccione **Guardar**. La aplicación que ha editado debe aparecer ahora con el nombre actualizado.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha personalizado la fuente para los usuarios, puede iniciar sesión en un cliente de Azure Virtual Desktop para probarlo. Para ello, continúe con los procedimientos para conectarse a Azure Virtual Desktop:

 * [Conexión con Windows 10 o Windows 7](./user-documentation/connect-windows-7-10.md)
 * [Conexión con el cliente web](./user-documentation/connect-web.md)
 * [Conexión con el cliente de Android](./user-documentation/connect-android.md)
 * [Conexión con el cliente iOS](./user-documentation/connect-ios.md)
 * [Conexión con el cliente macOS](./user-documentation/connect-macos.md)
