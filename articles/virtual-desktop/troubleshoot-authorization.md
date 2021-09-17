---
title: 'Solución de problemas de Azure Files en Virtual Desktop: Azure'
description: Cómo solucionar problemas con Azure Files en Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 31fe9f55252c00b5475bbb96cef646d0906a05f7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446648"
---
# <a name="troubleshoot-azure-files-authorization"></a>Solución de problemas de autorización de Azure Files

En este artículo se describen problemas comunes relacionados con la autenticación de Azure Files con Azure Active Directory (AD) y sugerencias sobre cómo corregirlos.

## <a name="my-group-membership-isnt-working"></a>La pertenencia a mi grupo no funciona

Al agregar una máquina virtual (VM) a un grupo de Active Directory Domain Services (AD DS), debe reiniciar esa máquina virtual para activar su pertenencia al servicio.

## <a name="i-cant-add-my-storage-account-to-my-ad-ds"></a>No puedo agregar mi cuenta de almacenamiento a mi AD DS

En primer lugar, active [No se puede montar Azure Files con credenciales de AD](../storage/files/storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) para ver si el problema aparece allí.

Estas son las razones más comunes por las que los usuarios pueden encontrarse con problemas:

- Omisión de los mensajes de advertencia que aparecen al crear la cuenta en PowerShell. Omitir las advertencias puede hacer que la nueva cuenta tenga una configuración incorrecta. Para corregir este problema, debe eliminar la cuenta de dominio que representa la cuenta de almacenamiento e intentarlo de nuevo.

- La cuenta usa una unidad organizativa (UO) incorrecta. Para corregir este problema, vuelva a escribir la información de unidad organizativa con la sintaxis siguiente:
    
    ```powershell
    DC=ouname,DC=domainprefix,DC=topleveldomain
    ```

    Por ejemplo:

    ```powershell
    DC=storageAccounts,DC=wvdcontoso,DC=com
    ```

- Si la cuenta de almacenamiento no aparece al instante en Azure AD, no se preocupe. Normalmente, una nueva cuenta de almacenamiento tarda 30 minutos en sincronizarse con Azure AD, por lo que debe tener paciencia. Si la sincronización no se realiza después de 30 minutos, consulte [la sección siguiente](#my-ad-ds-group-wont-sync-to-azure-ad).

## <a name="my-ad-ds-group-wont-sync-to-azure-ad"></a>Mi grupo de AD DS no se sincroniza con Azure AD

Si la cuenta de almacenamiento no se sincroniza automáticamente con Azure AD después de 30 minutos, deberá forzar la sincronización mediante [este script](https://github.com/stgeorgi/msixappattach/blob/master/force%20AD%20DS%20to%20Azure%20AD%20sync/force%20sync.ps1).

## <a name="my-storage-account-says-it-needs-additional-permissions"></a>Mi cuenta de almacenamiento indica que necesita permisos adicionales

Si la cuenta de almacenamiento necesita permisos adicionales, es posible que no tenga permiso para acceder a la conexión de aplicaciones MSIX y FSLogix. Para corregir este problema, asegúrese de que ha asignado uno de estos permisos a su cuenta:

- Permiso de RBAC de **Colaborador de recursos compartidos de SMB de datos de archivos de Storage**.

- Permisos de NTFS de **Leer y ejecutar** y **Enumerar el contenido de la carpeta**.

## <a name="next-steps"></a>Pasos siguientes

Si necesita actualizar la memoria sobre el proceso de configuración de Azure Files, consulte [Autorización de una cuenta para Azure Files](azure-files-authorization.md).