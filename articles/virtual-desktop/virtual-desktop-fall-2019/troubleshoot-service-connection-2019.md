---
title: 'Solución de problemas con la conexión al servicio de Azure Virtual Desktop (clásico): Azure'
description: Cómo resolver problemas al configurar conexiones de cliente en un entorno de inquilinos de Azure Virtual Desktop (clásico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 856d6e65273b15c843bffe40c818b07c60f00fb0
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753942"
---
# <a name="azure-virtual-desktop-classic-service-connections"></a>Conexiones al servicio de Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../troubleshoot-service-connection.md).

Use este artículo para resolver problemas con conexiones de cliente de Azure Virtual Desktop.

## <a name="provide-feedback"></a>Envío de comentarios

Puede proporcionarnos comentarios y hablar sobre el servicio Azure Virtual Desktop con el equipo del producto y otros miembros activos de la comunidad en la [comunidad técnica de Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>El usuario se conecta, pero no se muestra nada (ninguna fuente)

Un usuario puede iniciar clientes de Escritorio remoto y es capaz de realizar la autenticación, pero no ve ningún icono en la fuente de detección de web.

Confirme que el usuario que notificó los problemas se ha asignado a grupos de aplicaciones mediante el uso de esta línea de comandos:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme que el usuario ha iniciado sesión con las credenciales correctas.

Si se usa el cliente web, confirme que no hay ningún problema de credenciales almacenadas en caché.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre cómo solucionar problemas de Azure Virtual Desktop y las pistas de escalación, consulte [Introducción, comentarios y soporte técnico para solucionar problemas](troubleshoot-set-up-overview-2019.md).
- Para solucionar problemas durante la creación de un grupo de inquilinos y de hosts en un entorno de Azure Virtual Desktop, consulte [Creación de los grupos de inquilinos y de host](troubleshoot-set-up-issues-2019.md).
- Para solucionar problemas al configurar una máquina virtual (VM) en Azure Virtual Desktop, consulte [Configuración de la máquina virtual del host de sesión](troubleshoot-vm-configuration-2019.md).
- Para solucionar problemas al usar PowerShell con Azure Virtual Desktop, consulte [PowerShell para Azure Virtual Desktop](troubleshoot-powershell-2019.md).
- Para realizar un tutorial de solución de problemas, consulte [Tutorial: Solución de problemas de las implementaciones de plantillas de Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
