---
title: 'Corrección de problemas de pertenencias dinámicas a grupos: Azure AD | Microsoft Docs'
description: Sugerencias de solución de problemas de pertenencias dinámicas a grupos en Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: troubleshooting
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787017bd4c783e28a07870f61d708686fe6313fb
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445251"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Solución de problemas de grupos

## <a name="troubleshooting-group-creation-issues"></a>Solución de problemas de creación de grupos

**He deshabilitado la creación de grupos de seguridad en Azure Portal, pero los grupos todavía se pueden crear a través de PowerShell**  
La opción **Los usuarios pueden crear grupos de seguridad en los portales de Azure** de Azure Portal controla si los usuarios que no son administradores pueden crear grupos de seguridad en el Panel de acceso o en Azure Portal. No controla la creación de grupos de seguridad mediante PowerShell.

Para deshabilitar la creación de grupos para usuarios no administradores en PowerShell:
1. Compruebe que los usuarios no administradores pueden crear grupos:

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

2. Si devuelve `UsersPermissionToCreateGroupsEnabled : True`, los usuarios no administrativos pueden crear grupos. Para deshabilitar esta característica:

   ```powershell
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

**He recibido un error de grupos máximos permitidos al intentar crear un grupo dinámico en PowerShell**  
Si recibe un mensaje en PowerShell que indica que _se ha alcanzado el número máximo de grupos permitidos de directivas de grupos dinámicos_, significa que se ha alcanzado el límite máximo de grupos dinámicos en la organización. El número máximo de grupos dinámicos por organización es 5000.

Para crear grupos dinámicos, primero debe eliminar algunos grupos dinámicos existentes. No hay ninguna forma de aumentar el límite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solución de problemas relacionados con las pertenencias dinámicas para grupos

**He configurado una regla en un grupo pero las pertenencias no se actualizan en el grupo**.  
1. Compruebe los valores de los atributos de usuario o dispositivo de la regla. Asegúrese de que haya usuarios que cumplen la regla.
En el caso de los dispositivos, compruebe las propiedades de estos para asegurarse de que los atributos sincronizados contienen los valores esperados.  
2. Compruebe el estado de procesamiento de la pertenencia para confirmar que está completa. Puede comprobar el [estado de procesamiento de la pertenencia](groups-create-rule.md#check-processing-status-for-a-rule) y la última fecha actualizada en la página **Información general** del grupo.

Si todo es correcto, el grupo tardará un poco en rellenarse. Dependiendo del tamaño de la organización de Azure AD, el grupo puede tardar hasta 24 horas en rellenarse por primera vez o después de un cambio de la regla.

**He configurado una regla, pero ahora se han quitado los miembros existentes de la regla**.  
Este es el comportamiento esperado. Los miembros existentes del grupo se quitan cuando una regla se habilita o se cambia. Los usuarios devueltos tras la evaluación de la regla se agregan como miembros al grupo.

**No veo los cambios en la pertenencia al instante cuando agrego o cambio una regla, ¿por qué pasa esto?**  
La evaluación de pertenencia dedicada se realiza periódicamente en un proceso asincrónico en segundo plano. La duración del proceso viene determinada por el número de usuarios del directorio y el tamaño del grupo creado como resultado de la regla. Normalmente, los directorios con un número pequeño de usuarios verán los cambios en la pertenencia al grupo en unos pocos minutos. Los directorios con un gran número de usuarios pueden tardar 30 minutos o más en completarse.

**¿Cómo se puede forzar al grupo a procesarse ahora?**  
Actualmente, no hay ninguna manera de desencadenar automáticamente el procesamiento del grupo a petición. Sin embargo, puede desencadenar manualmente el reprocesamiento si actualiza la regla de pertenencia para agregar un espacio en blanco al final.

**Se detectó una error de procesamiento de la regla**  
En la tabla siguiente se enumeran los errores de reglas de pertenencia dinámica más habituales y se indica cómo corregirlos.

| Error del analizador de reglas | Uso con error | Uso corregido |
| --- | --- | --- |
| Error: no se admite el atributo. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Asegúrese de que el atributo se encuentra en la [lista de propiedades admitidas](groups-dynamic-membership.md#supported-properties). |
| Error: no se admite el operador en el atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>El operador usado no se admite para el tipo de propiedad (en este ejemplo, -contains no se puede usar con el tipo boolean). Utilice los operadores correctos para el tipo de propiedad. |
| Error: error de compilación de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "\*@domain.ext") | 1. Falta el operador. Use -and u -or para predicados de combinación<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Error en la expresión regular usada con -match<br>(user.userPrincipalName -match ".\*@domain.ext")<br>o bien (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Pasos siguientes

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Administración de aplicaciones en Azure Active Directory](../manage-apps/what-is-application-management.md)
* [¿Qué es Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
