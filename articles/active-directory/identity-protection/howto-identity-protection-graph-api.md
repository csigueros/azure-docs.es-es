---
title: SDK de PowerShell de Microsoft Graph y Azure Active Directory Identity Protection
description: Obtenga información sobre cómo realizar una consulta a Microsoft Graph para obtener una lista de detecciones de riesgo e información asociada desde Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21274636a38cb37ac62f2b73112d9346482fe573
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739961"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection y el SDK de PowerShell de Microsoft Graph

Microsoft Graph es el punto de conexión de API unificada de Microsoft y donde se encuentran las API de [Azure Active Directory Identity Protection](./overview-identity-protection.md). En este artículo se muestra cómo usar el [SDK de PowerShell de Microsoft Graph](/graph/powershell/get-started) para obtener detalles de un usuario de riesgo mediante PowerShell. Las organizaciones que deseen consultar directamente las API de Microsoft Graph pueden usar el artículo [Tutorial: identificar y corregir los riesgos mediante las API de Microsoft Graph](/graph/tutorial-riskdetection-api) para comenzar ese viaje.


## <a name="connect-to-microsoft-graph"></a>Conexión con Microsoft Graph

Hay cuatro pasos para acceder a los datos de Identity Protection a través de Microsoft Graph:

- [Crear un certificado](#create-a-certificate)
- [Creación de un nuevo registro de aplicaciones](#create-a-new-app-registration)
- [Configuración de permisos de API](#configure-api-permissions)
- [Configuración de una credencial válida](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Crear un certificado

En un entorno de producción, usaría un certificado de la entidad de certificación de producción, pero en este ejemplo usaremos un certificado autofirmado. Cree y exporte el certificado con los siguientes comandos de PowerShell.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Creación de un registro de aplicaciones

1. En Azure Portal, vaya a **Azure Active Directory** > **Registros de aplicaciones**.
1. Seleccione **Nuevo registro**.
1. En la página **Crear**, realice los pasos siguientes:
   1. En el cuadro de texto **Nombre**, escriba el nombre de la aplicación (por ejemplo: API Risk Detection de Azure AD).
   1. En **Tipos de cuenta compatibles**, seleccione el tipo de cuentas que utilizarán las API.
   1. Seleccione **Registrar**.
1. Anote el **identificador de aplicación (cliente)** y el **identificador de directorio (inquilino)** , porque los necesitará más adelante.

### <a name="configure-api-permissions"></a>Configuración de permisos de API

En este ejemplo, se configuran los permisos de la aplicación para que este ejemplo se use en modo desatendido. Si concede permisos a un usuario que va a iniciar sesión, elija permisos delegados en su lugar. Puede encontrar más información sobre los distintos tipos de permisos en el artículo [Permisos y consentimiento en la plataforma de identidad de Microsoft](../develop/v2-permissions-and-consent.md#permission-types).

1. Desde la **Aplicación** que creó, seleccione **Permisos de API**.
1. En la página **Permisos configurados**, en la barra de herramientas de la parte superior, haga clic en **Agregar un permiso**.
1. En la página **Agregar acceso de API**, haga clic en **Seleccionar una API**.
1. En la página **Seleccionar una API**, seleccione **Microsoft Graph** y, a continuación, haga clic en **Seleccionar**.
1. En la página **Solicitud de permisos de API**. 
   1. Seleccione **Permisos de aplicación**.
   1. Active las casillas junto a `IdentityRiskEvent.Read.All` y `IdentityRiskyUser.Read.All`.
   1. Seleccione **Agregar permisos**.
1. Seleccione **Conceder consentimiento del administrador para el dominio**. 

### <a name="configure-a-valid-credential"></a>Configuración de una credencial válida

1. Desde la **Aplicación** que creó, seleccione **Certificados y secretos**.
1. En **Certificados**, seleccione **Cargar certificado**.
   1. Seleccione el certificado exportado anteriormente en la ventana que se abre.
   1. Seleccione **Agregar**.
1. Tome nota de la **huella digital** del certificado, ya que necesitará esta información en el paso siguiente.

## <a name="list-risky-users-using-powershell"></a>Enumeración de usuarios de riesgo con PowerShell

Para habilitar la capacidad de consultar Microsoft Graph, es necesario instalar el módulo `Microsoft.Graph` en nuestra ventana de PowerShell mediante el comando `Install-Module Microsoft.Graph`.

Modifique las siguientes variables para incluir la información generada en los pasos anteriores y, a continuación, ejecútelas como un todo para obtener los detalles del usuario de riesgo mediante PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al SDK de PowerShell de Microsoft Graph](/graph/powershell/get-started)
- [Tutorial: identificar y corregir los riesgos mediante las API de Microsoft Graph](/graph/tutorial-riskdetection-api)
- [Overview of Microsoft Graph (Información general de Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Obtener acceso sin un usuario](/graph/auth-v2-service)
- [Azure AD Identity Protection Service Root (Raíz del servicio de Azure AD Identity Protection)](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
