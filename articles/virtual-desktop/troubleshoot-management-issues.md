---
title: 'Problemas de administración de Azure Virtual Desktop: Azure'
description: Problemas comunes de administración en Azure Virtual Desktop y cómo resolverlos.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 529cf643cb68bc806aef991142e68f42fccc0d42
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112420703"
---
# <a name="management-issues"></a>Problemas de administración

En este artículo se describen los errores de administración comunes y se ofrece sugerencias sobre cómo resolverlos.

## <a name="common-management-errors"></a>Errores de administración comunes

En la tabla siguiente se enumeran los mensajes de error que aparecen debido a problemas relacionados con la administración y sugerencias sobre cómo resolverlos.

|Mensaje de error|Solución propuesta|
|---|---|
|Failed to create registration key. (No se pudo crear la clave de registro). |No se pudo crear el token de registro. Pruebe a crearlo de nuevo con un tiempo de expiración más breve (entre 1 hora y 1 mes). |
|Failed to delete registration key. (No se pudo eliminar la clave de registro).|No se pudo eliminar el token de registro. Vuelva a intentar eliminarlo. Si sigue sin funcionar, use PowerShell para comprobar si el token sigue ahí. Si es así, elimínelo con PowerShell.|
|Failed to change session host drain mode. (No se pudo cambiar el modo de purga del host de sesión). |No se pudo cambiar el modo de purga en la VM. Compruebe el estado de la VM. Si la máquina virtual no está disponible, no puede cambiar el modo de purga.|
|Failed to disconnect user sessions. (No se pudieron desconectar las sesiones del usuario). |No se pudo desconectar el usuario de la VM. Compruebe el estado de la VM. Si la máquina virtual no está disponible, no puede desconectar la sesión de usuario. Si está disponible, compruebe el estado de la sesión de usuario para saber si está desconectado. |
|Failed to log off all user(s) within the session host. (No se pudo cerrar la sesión de todos los usuarios en el host de sesión). |No se pudo cerrar la sesión de los usuarios en la VM. Compruebe el estado de la VM. Si no está disponible, no se puede cerrar la sesión de los usuarios. Compruebe el estado de la sesión del usuario para averiguar si ya ha cerrado la sesión. Puede forzar el cierre de sesión con PowerShell. |
|Failed to unassign user from application group. (No se pudo cancelar la asignación del usuario del grupo de aplicaciones).|No se pudo anular la publicación de un grupo de aplicaciones para un usuario. Compruebe si el usuario está disponible en Azure AD. Consulte si el usuario forma parte de un grupo de usuarios en que está publicado el grupo de aplicaciones. |
|There was an error retrieving the available locations. (Error al recuperar las ubicaciones disponibles). |Compruebe la ubicación de la VM usada en el asistente para crear grupos de hosts. Si la imagen no está disponible en esa ubicación, agregue la imagen en la ubicación o elija otra ubicación de VM. |

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Error: No se pueden agregar asignaciones de usuario a un grupo de aplicaciones

Después de asignar un usuario a un grupo de aplicaciones, Azure Portal muestra una advertencia que indica "La sesión está finalizando" o "Experimentando problemas de autenticación: extensión Microsoft_Azure_WVD". Seguidamente, la página de asignación no se carga y, después de eso, las páginas dejan de cargarse en Azure Portal (por ejemplo, Azure Monitor, Log Analytics, Service Health, etc.).

Este problema suele aparecer porque hay un problema con la directiva de acceso condicional. Azure Portal está intentando obtener un token para Microsoft Graph, que depende de SharePoint Online. El cliente tiene una directiva de acceso condicional denominada "Términos de uso de almacenamiento de datos de Microsoft Office 365" que requiere que los usuarios acepten los términos de uso para acceder al almacenamiento de datos. Sin embargo, aún no han iniciado sesión, por lo que Azure Portal no puede obtener el token.

Para resolver este problema, antes de iniciar sesión en Azure Portal, el administrador debe iniciar sesión en SharePoint y aceptar los términos de uso. Después, debería poder iniciar sesión en Azure Portal con normalidad.

## <a name="next-steps"></a>Pasos siguientes

Para revisar los escenarios de error comunes que la característica de diagnóstico puede identificar automáticamente, consulte [Identificación y diagnóstico de problemas](diagnostics-role-service.md#common-error-scenarios).
