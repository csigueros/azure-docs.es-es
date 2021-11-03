---
title: Privacidad del usuario e inicio de sesión único de conexión directa de Azure AD | Microsoft Docs
description: Este artículo trata sobre el SSO de conexión directa de Azure Active Directory (Azure AD) y el cumplimiento del RGPD.
services: active-directory
keywords: qué es Azure AD Connect, RGPD, componentes necesarios para Azure AD, SSO, inicio de sesión único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21d4253da1d3e4b190cfad92349ee885541766c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067993"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Privacidad del usuario e inicio de sesión único de conexión directa de Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Información general


SSO de conexión directa de Azure AD crea el tipo de registro siguiente, que puede contener datos personales: 

- Archivos de registro de seguimiento de Azure AD Connect.

Dispone de dos métodos para mejorar la privacidad del usuario para SSO de conexión directa:

1.  Previa solicitud, extraer los datos de una persona y quitarlos de las instalaciones.
2.  Asegurarse de que ningún dato se conserva más de 48 horas.

Se recomienda encarecidamente la segunda opción, porque es más simple de implementar y mantener. A continuación, consulte las instrucciones para cada tipo de registro:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminación de archivos de registro de seguimiento de Azure AD Connect

Revise el contenido de la carpeta **%ProgramData%\AADConnect** y elimine el contenido de registro de seguimiento (archivos **trace-\*.log**) de esta carpeta en un plazo de 48 horas a contar de la instalación o la instalación de Azure AD Connect o de la modificación de la configuración del SSO de conexión directa, porque esta acción podría crear los datos que cubre el RGPD.

>[!IMPORTANT]
>No elimine el archivo **PersistedState.xml** de esta carpeta, porque este archivo se usa para mantener el estado de la instalación anterior de Azure AD Connect y se usa cuando se realiza una instalación de actualización. Este archivo nunca contendrá ningún dato sobre una persona y nunca deberá eliminarse.

Puede revisar y eliminar estos archivos de registro de seguimiento con el Explorador de Windows, o bien puede usar el script de PowerShell siguiente para realizar las acciones necesarias:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde el script en un archivo con la extensión ".PS1". Ejecute este script según sea necesario.

Para más información sobre los requisitos relacionados del RGPD de Azure AD Connect, consulte [este artículo](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Nota sobre los registros de controlador de dominio

Si el registro de auditoría está habilitado, este producto puede generar registros de seguridad para los controladores de dominio. Para más información sobre cómo configurar las directivas de auditoría, lea este [artículo](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Pasos siguientes

* [Revisar la directiva de privacidad de Microsoft en Trust Center](https://www.microsoft.com/trustcenter)
  - [**Solución de problemas**](tshoot-connect-sso.md): información para resolver problemas habituales de esta característica.
  - [**UserVoice**](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789): para la tramitación de solicitudes de nuevas características.