---
title: Instalación del agente de aprovisionamiento en la nube de Azure AD Connect mediante una interfaz de la línea de comandos (CLI) y PowerShell
description: Aprenda a instalar el agente de aprovisionamiento en la nube de Azure AD Connect con cmdlets de PowerShell.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c150f4bf18f9c4f5b9215122a45df1de32f48c32
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506248"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-by-using-a-cli-and-powershell"></a>Instalación del agente de aprovisionamiento de Azure AD Connect con la CLI y PowerShell
En este artículo se muestra cómo instalar el agente de aprovisionamiento de Azure Active Directory (Azure AD) mediante cmdlets de PowerShell.
 
>[!NOTE]
>En este artículo se describe la instalación del agente de aprovisionamiento mediante la interfaz de la línea de comandos (CLI). Para información sobre la instalación del agente de aprovisionamiento de Azure AD Connect mediante el asistente, consulte [Instalación del agente de aprovisionamiento de Azure AD Connect](how-to-install.md).

## <a name="prerequisite"></a>Requisito previo

Windows Server debe tener TLS 1.2 habilitado para poder instalar el agente de aprovisionamiento de Azure AD Connect con cmdlets de PowerShell. Para habilitar TLS 1.2, siga los pasos descritos en [Requisitos previos para Azure AD Connect Cloud Sync](how-to-prerequisites.md#tls-requirements).

>[!IMPORTANT]
>En las siguientes instrucciones de instalación se supone que se han cumplido todos los [requisitos previos](how-to-prerequisites.md).

## <a name="install-the-azure-ad-connect-provisioning-agent-by-using-powershell-cmdlets"></a>Instalación del agente de aprovisionamiento de Azure AD Connect con cmdlets de PowerShell 

 1. Inicie sesión en Azure Portal y, a continuación, vaya a **Azure Active Directory**.
 1. En el menú de la izquierda, seleccione **Azure AD Connect**.
 1. Seleccione **Administrar el aprovisionamiento (versión preliminar)**  > **Revisar todos los agentes**.
 1. Descargue el agente de aprovisionamiento de Azure AD Connect en Azure Portal.

    ![Captura de pantalla que muestra la descarga del agente local.](media/how-to-install/install-9.png)</br>

 1. Para los fines de estas instrucciones, el agente se descargó en la siguiente carpeta: "C:\ProvisioningSetup". 
 1. Instale ProvisioningAgent en modo silencioso.

       ```
       $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
       $installerProcess.WaitForExit()  
       ```
 1. Importe el módulo PS del agente de aprovisionamiento.

       ```
       Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
       ```
 1. Conéctese a Azure AD con las credenciales de administrador global. Puede personalizar esta sección para capturar la contraseña desde un almacén seguro. 

       ```
       $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 
    
       $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
       
       Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 
       ```
 1. Agregue la cuenta de gMSA y proporcione las credenciales del administrador de dominio para crear la cuenta de gMSA predeterminada.
 
       ```
       $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 
    
       Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
       ```
 1. O bien, use el cmdlet anterior para proporcionar una cuenta de gMSA creada previamente.
 
       ```
       Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
       ```
 1. Agregue el dominio.

       ```
       $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
       ```
 1. O bien, use el cmdlet anterior para configurar los controladores de dominio preferidos.

       ```
       $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
       ```
 1. Repita el paso anterior para agregar más dominios. Proporcione los nombres de cuenta y de dominio de los dominios respectivos.
 
 1. Reinicie el servicio.
 
       ```
       Restart-Service -Name AADConnectProvisioningAgent  
       ```
 1. Vaya a Azure Portal para crear la configuración de sincronización en la nube.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Cmdlets de PowerShell de gMSA del agente de aprovisionamiento
Ahora que ha instalado el agente, puede aplicar permisos más granulares a gMSA. Para información e instrucciones paso a paso sobre cómo configurar los permisos, consulte [Cmdlets de PowerShell de gMSA del agente de aprovisionamiento en la nube de Azure AD Connect](how-to-gmsa-cmdlets.md).

## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [Cmdlets de PowerShell de gMSA del agente de aprovisionamiento en la nube de Azure AD](how-to-gmsa-cmdlets.md)
- [¿Qué es la sincronización en la nube de Azure AD Connect?](what-is-cloud-sync.md)