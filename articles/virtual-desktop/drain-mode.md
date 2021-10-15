---
title: 'Uso del modo de purga para aislar hosts de sesión: Azure Virtual Desktop'
description: Uso del modo de purga para aislar hosts de sesión para llevar a cabo tareas de mantenimiento en Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 4bacff4ecef6762a4e08760c463531392a71245f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352003"
---
# <a name="use-drain-mode-to-isolate-session-hosts-and-apply-patches"></a>Uso del modo de purga para aislar los hosts de sesión y aplicar revisiones

El modo de purga aísla un host de sesión cuando desea aplicar revisiones y realizar tareas de mantenimiento sin interrumpir las sesiones de usuario. Cuando está aislado, el host de sesión no acepta nuevas sesiones de usuario. Las nuevas conexiones se redirigirán al siguiente host de sesión disponible. Las conexiones existentes en el host de sesión seguirán funcionando hasta que el usuario cierre la sesión o el administrador la finalice. Cuando el host de sesión está en modo de purga, los administradores también pueden conectarse de forma remota al servidor sin necesidad del servicio Azure Virtual Desktop. Puede aplicar esta configuración a escritorios tanto agrupados como personales.

## <a name="set-drain-mode-using-the-azure-portal"></a>Establecimiento del modo de purga mediante Azure Portal

Para activar el modo de purga en Azure Portal:

1. Abra Azure Portal y vaya al grupo de hosts que desea aislar.

2. En el menú de navegación, seleccione **Hosts de sesión**.

3. A continuación, seleccione los hosts para los que desea activar el modo de purga y, después, seleccione **Turn drain mode on** (Activar modo de purga).

4. Para desactivar el modo de purga, seleccione los grupos de hosts que tienen activado el modo de purga y, a continuación, seleccione **Turn drain mode off** (Desactivar modo de purga).

## <a name="set-drain-mode-using-powershell"></a>Establecimiento del modo de purga mediante PowerShell

Puede establecer el modo de purga en PowerShell con el parámetro *AllowNewSessions*, que forma parte del comando [Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true).

Ejecute este cmdlet para habilitar el modo de purga:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Ejecute este cmdlet para deshabilitar el modo de purga:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Deberá ejecutar este comando para cada host de sesión al que aplique la configuración.

## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información sobre Azure Portal para Azure Virtual Desktop, consulte [nuestros tutoriales](create-host-pools-azure-marketplace.md). Si ya está familiarizado con los conceptos básicos, consulte algunas de las demás características que puede usar con Azure Portal, como la [asociación de aplicaciones en formato .MSIX](app-attach-azure-portal.md) y [Azure Advisor](azure-advisor.md).

Si usa el método de PowerShell y quiere ver qué más puede hacer el módulo, consulte [Configuración del módulo de PowerShell para Azure Virtual Desktop](powershell-module.md) y la [referencia de PowerShell](/powershell/module/az.desktopvirtualization/).
