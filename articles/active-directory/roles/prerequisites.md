---
title: 'Requisitos previos para el uso de PowerShell o Probador de Graph para roles de Azure AD: Azure Active Directory'
description: Requisitos previos para el uso de PowerShell o Probador de Graph para roles de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef457948a28d9d07464b96bfaea4df6a6f2a8723
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747856"
---
# <a name="prerequisites-to-use-powershell-or-graph-explorer-for-azure-ad-roles"></a>Requisitos previos para el uso de PowerShell o Probador de Graph para roles de Azure AD

Si desea administrar roles de Azure Active Directory (Azure AD) mediante PowerShell o Probador de Graph, debe tener los requisitos previos necesarios. En este artículo se describen los requisitos previos de PowerShell y Graph Explorer para diferentes características de roles de Azure AD.

## <a name="azuread-module"></a>Módulo AzureAD

Para usar comandos de PowerShell que realicen las siguientes acciones:

- Lista de asignaciones de roles
- Creación de un grupo al que se pueden asignar roles
- Administración de unidades administrativas

Debe tener instalado el siguiente módulo:

- Versión 2.0.2.137 o posterior de [AzureAD](https://www.powershellgallery.com/packages/AzureAD)


#### <a name="check-azuread-version"></a>Comprobación de la versión de AzureAD

Para comprobar qué versión de AzureAD ha instalado, use [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule).

```powershell
Get-InstalledModule -Name AzureAD
```

Debería ver un resultado similar al siguiente:

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.137  AzureAD                             PSGallery            Azure Active Directory V2 General Availability M...
```

#### <a name="install-azuread"></a>Instalación de AzureAD

Si no tiene instalado AzureAD, use [Install-Module](/powershell/module/powershellget/install-module) para instalarlo.

```powershell
Install-Module -Name AzureAD
```

#### <a name="update-azuread"></a>Actualización de AzureAD

Para actualizar AzureAD a la versión más reciente, vuelva a ejecutar [Install-Module](/powershell/module/powershellget/install-module).

```powershell
Install-Module -Name AzureAD
```

#### <a name="use-azuread"></a>Uso de AzureAD

Para usar AzureAD, siga estos pasos para asegurarse de que se importa en la sesión actual.

1. Use [Get-Module](/powershell/module/microsoft.powershell.core/get-module) para comprobar si AzureAD está cargado en la memoria.

    ```powershell
    Get-Module -Name AzureAD
    ```

1. Si no ve ninguna salida en el paso anterior, use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar AzureAD. El parámetro `-Force` elimina el módulo cargado y, a continuación, lo importa de nuevo.

    ```powershell
    Import-Module -Name AzureAD -Force
    ```

1. Vuelva a ejecutar [Get-Module](/powershell/module/microsoft.powershell.core/get-module).

    ```powershell
    Get-Module -Name AzureAD
    ```

    Debería ver un resultado similar al siguiente:
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.137  AzureAD                             {Add-AzureADApplicationOwner, Add-AzureADDeviceRegisteredO...
    ```

## <a name="azureadpreview-module"></a>Módulo AzureADPreview

Para usar comandos de PowerShell que realicen las siguientes acciones:

- Asignación de roles a usuarios o grupos
- Eliminación de una asignación de rol
- Hacer que un grupo sea apto para un rol mediante Privileged Identity Management
- Creación de roles personalizados

Debe tener instalado el siguiente módulo:

- Versión 2.0.2.138 o posterior de [AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)


#### <a name="check-azureadpreview-version"></a>Comprobación de la versión de AzureADPreview

Para comprobar qué versión de AzureADPreview ha instalado, use [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule).

```powershell
Get-InstalledModule -Name AzureADPreview
```

Debería ver un resultado similar al siguiente:

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.138  AzureADPreview                      PSGallery            Azure Active Directory V2 Preview Module. ...
```

#### <a name="install-azureadpreview"></a>Instalación de AzureADPreview

Si no tiene instalado AzureADPreview, use [Install-Module](/powershell/module/powershellget/install-module) para instalarlo.

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="update-azureadpreview"></a>Actualización de AzureADPreview

Para actualizar AzureADPreview a la versión más reciente, vuelva a ejecutar [Install-Module](/powershell/module/powershellget/install-module).

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="use-azureadpreview"></a>Uso de AzureADPreview

Para usar AzureADPreview, siga estos pasos para asegurarse de que se importa en la sesión actual.

1. Use [Get-Module](/powershell/module/microsoft.powershell.core/get-module) para comprobar si AzureADPreview está cargado en la memoria.

    ```powershell
    Get-Module -Name AzureADPreview
    ```

1. Si no ve ninguna salida en el paso anterior, use [Import-Module](/powershell/module/microsoft.powershell.core/import-module) para importar AzureADPreview. El parámetro `-Force` elimina el módulo cargado y, a continuación, lo importa de nuevo.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Vuelva a ejecutar [Get-Module](/powershell/module/microsoft.powershell.core/get-module).

    ```powershell
    Get-Module -Name AzureADPreview
    ```

    Debería ver un resultado similar al siguiente:
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.138  AzureADPreview                      {Add-AzureADAdministrativeUnitMember, Add-AzureADApplicati...
    ```

## <a name="graph-explorer"></a>Explorador de gráfico

Para administrar roles de Azure AD mediante [Microsoft Graph API](/graph/overview) y [Probador de Graph](/graph/graph-explorer/graph-explorer-overview), debe hacer lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [Centro de administración de Azure AD](https://aad.portal.azure.com).

1. Seleccione **Azure Active Directory** > **Aplicaciones empresariales**.

1. En la lista de aplicaciones, busque y seleccione **Probador de Graph**.

1. Seleccione **Permisos**.

1. Seleccione **Grant admin consent for Graph explorer** (Conceder consentimiento de administrador para Probador de Graph).

    ![Captura de pantalla en la que se muestra el vínculo "Conceder consentimiento de administrador para Probador de Graph".](./media/prerequisites/select-graph-explorer.png)

1. Use la [herramienta Probador de Graph](https://aka.ms/ge).

## <a name="next-steps"></a>Pasos siguientes

- [Instalación de PowerShell de Azure Active Directory para Graph](/powershell/azure/active-directory/install-adv2)
- [Documentación del módulo AzureAD](/powershell/module/azuread/)
- [Explorador de gráfico](/graph/graph-explorer/graph-explorer-overview)
