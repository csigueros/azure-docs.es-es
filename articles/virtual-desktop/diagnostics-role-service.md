---
title: 'Problemas de diagnóstico de Azure Virtual Desktop: Azure'
description: Procedimientos para usar la característica de diagnóstico de Azure Virtual Desktop para diagnosticar problemas.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0154bb61dcfbcf8024f09052d7a199ac63f71d8e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412548"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>Identificación y diagnóstico de problemas de Azure Virtual Desktop

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Azure Virtual Desktop ofrece una característica de diagnóstico que permite al administrador detectar problemas a través de una única interfaz. Para obtener más información sobre las funcionalidades de diagnóstico de Azure Virtual Desktop, consulte [Uso de Log Analytics para la característica de diagnóstico](diagnostics-log-analytics.md).

Las conexiones que no lleguen a Azure Virtual Desktop no aparecerán en los resultados de diagnóstico, ya que el propio servicio de rol de diagnóstico forma parte de Azure Virtual Desktop. Se pueden producir problemas de conexión a Azure Virtual Desktop si el usuario final está experimentando problemas de conectividad de red.

## <a name="common-error-scenarios"></a>Escenarios de error habituales

La tabla WVDErrors realiza un seguimiento de los errores en todos los tipos de actividad. La columna llamada "ServiceError" proporciona una marca adicional que indica "true" o "false". Esta marca le indicará si el error está relacionado con el servicio.

* Si el valor es "true", es posible que el equipo de servicio ya haya investigado este problema. Si este afecta a la experiencia del usuario y aparece muchas veces, le recomendamos que envíe una incidencia de soporte técnico para Azure Virtual Desktop.
* Si el valor es "False", puede ser un error de configuración que puede corregir por su cuenta. El mensaje de error puede proporcionarle una pista acerca de por dónde empezar.

En la siguiente tabla se enumeran los errores comunes que los administradores pueden encontrarse.

>[!NOTE]
>Esta lista incluye los errores más comunes y se actualiza de manera periódica. Para estar seguro de que dispone de la información más actualizada, procure consultar en este artículo al menos una vez al mes.

### <a name="connection-error-codes"></a>Códigos de error de conexión

|Código numérico|Código de error|Solución propuesta|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|El host de sesión no está unido correctamente a Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Las conexiones no se pudieron establecer porque el host de sesión no está disponible. Compruebe el estado del host de sesión.|
|-2146233088|ConnectionFailedClientDisconnect|Si ve este error con frecuencia, asegúrese de que el equipo del usuario está conectado a la red.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sesión a la que el usuario de host ha intentado conectarse no está en un estado adecuado. Depure la máquina virtual.|
|-2146233088|ConnectionFailedUserNotAuthorized|El usuario no tiene permiso para acceder al escritorio o la aplicación publicada. Este error puede aparecer después de que el administrador quite recursos publicados. Pida al usuario que actualice la fuente en la aplicación de Escritorio remoto.|
|2|FileNotFound|La aplicación a la que el usuario intentó tener acceso incorrectamente está instalada o establecida en una ruta de acceso incorrecta.<br><br>Al publicar nuevas aplicaciones mientras el usuario tiene una sesión activa, este no podrá acceder a esta aplicación. La sesión debe cerrarse y reiniciarse para que el usuario pueda acceder a la aplicación. |
|3|InvalidCredentials|El nombre de usuario o contraseña especificados por el usuario no coincide con el nombre de usuario o contraseña existentes. Revise las credenciales para ver si hay errores tipográficos e inténtelo de nuevo.|
|8|ConnectionBroken|La conexión entre el cliente y la puerta de enlace o servidor se ha interrumpido. No es necesario hacer nada a menos que esto ocurra de forma inesperada.|
|14|UnexpectedNetworkDisconnect|La conexión a la red se ha interrumpido. Pida al usuario que vuelva a conectarse.|
|24|ReverseConnectFailed|La máquina virtual host no tiene línea de visión directa a la puerta de enlace de Escritorio remoto. Asegúrese de que la dirección IP de la puerta de enlace se puede resolver.|

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los roles de Azure Virtual Desktop, consulte [Entorno de Azure Virtual Desktop](environment-setup.md).

Para ver una lista de cmdlets de PowerShell disponibles para Azure Virtual Desktop, consulte la [referencia de PowerShell](/powershell/windows-virtual-desktop/overview).
