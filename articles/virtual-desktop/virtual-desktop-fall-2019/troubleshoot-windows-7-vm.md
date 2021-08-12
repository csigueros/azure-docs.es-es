---
title: 'Máquinas virtuales con Windows 7 en Azure Virtual Desktop (clásico): Azure'
description: Procedimiento para resolver incidencias en máquinas virtuales con Windows 7 en un entorno de Azure Virtual Desktop (clásico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2d0ea39dc4a0b217c35322e0aefc477996402a0f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753870"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-azure-virtual-desktop-classic"></a>Solución de problemas de las máquinas virtuales con Windows 7 en Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager.

Use este artículo para solucionar problemas que tiene al configurar las máquinas virtuales del host de sesión de Azure Virtual Desktop.

## <a name="known-issues"></a>Problemas conocidos

Windows 7 en instancias de Azure Virtual Desktop no admite las características siguientes:

- Aplicaciones virtualizadas (RemoteApps)
- Redirección de zona horaria
- Escalado automático de PPP

Azure Virtual Desktop solo puede virtualizar escritorios completos para Windows 7.

Aunque no se admite el escalado automático de PPP, puede cambiar manualmente la resolución de la máquina virtual; para ello, haga clic con el botón derecho en el icono del cliente de Escritorio remoto y seleccione **Resolución**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Error: No se puede acceder al grupo Usuario de Escritorio remoto

Si Azure Virtual Desktop no puede encontrarle o no puede encontrar las credenciales de los usuarios en el grupo Usuario de Escritorio remoto, puede que vea uno de los siguientes mensajes de error:

- "This user is not a member of the Remote Desktop User group" (Este usuario no es miembro del grupo Usuario de Escritorio remoto)
- "You must be granted permissions to sign in through Remote Desktop Services" (Se le deben conceder permisos para iniciar sesión a través de Servicios de Escritorio remoto)

Para corregir este error, agregue el usuario al grupo Usuario de Escritorio remoto:

1. Abra Azure Portal.
2. Seleccione la máquina virtual en la que vio el mensaje de error.
3. Seleccione **Ejecutar un comando**.
4. Ejecute el siguiente comando, pero reemplace `<username>` por el nombre del usuario que desea agregar:

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```