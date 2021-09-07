---
title: 'Azure AD Connect: Referencia de PowerShell ADSyncTools | Microsoft Docs'
description: En este documento se proporciona información de referencia para el módulo de PowerShell ADSyncTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 768b037573d1114141ca9722feb6a9ecd510dd5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734525"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  Referencia de PowerShell ADSyncTools
En la siguiente documentación se proporciona información de referencia para el módulo de PowerShell ADSyncTools.psm1 que se incluye con Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Instalación del módulo de PowerShell ADSyncTools
Para instalar el módulo de PowerShell ADSyncTools, haga lo siguiente:

1.  Abra Windows PowerShell con privilegios de administrador.
2.  Escriba o copie y pegue lo siguiente: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Presione Entrar.
4.  Para comprobar que se ha instalado el módulo, escriba o copie y pegue lo siguiente:
    ```powershell
    Get-module AdSyncTools
    ```
5.  Ahora debería ver información sobre el módulo.


## <a name="clear-adsynctoolsmsdsconsistencyguid"></a>Clear-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Borrado de un objeto de Active Directory mS-DS-ConsistencyGuid
### SYNTAX
```
Clear-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Borra el valor de mS-DS-ConsistencyGuid para el objeto de Active Directory destino.
Admite objetos de Active Directory en bosques de varios dominios.

### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>EJEMPLO 3
```
'User1@Contoso.com' | Clear-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Objeto de destino de AD para borrar mS-DS-ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="connect-adsynctoolssqldatabase"></a>Connect-ADSyncToolsSqlDatabase
### SYNOPSIS
Conexión a una base de datos SQL con fines de prueba
### SYNTAX
```
Connect-ADSyncToolsSqlDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>]
 [[-Port] <String>] [[-UserName] <String>] [[-Password] <String>] [<CommonParameters>]
```
### DESCRIPTION
Funciones y utilidades relacionadas con diagnósticos de SQL
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Database 'ADSync'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Instance 'INTANCE01' -Database 'ADSync'
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nombre del servidor SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-instance"></a>-Instance
Nombre de una instancia de SQL
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-database"></a>-Database
Nombre de base de datos de SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Puerto de SQL Server (por ejemplo,
49823)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-username"></a>-UserName
Nombre de usuario de inicio de sesión de SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-password"></a>-Password
Contraseña de inicio de sesión de SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertfrom-adsynctoolsaaddistinguishedname"></a>ConvertFrom-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Conversión del conector DistinguishedName de Azure AD en ImmutableId
### SYNTAX
```
ConvertFrom-ADSyncToolsAadDistinguishedName [-DistinguishedName] <String> [<CommonParameters>]
```
### DESCRIPTION
Toma un conector DistinguishedName de Azure AD, como CN={514635484D4B376E38307176645973555049486139513D3D}, y lo convierte en el valor ImmutableID de Base64 correspondiente, como MUT5IQK7n80qvdYsUPIHa9Q==.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
ConvertFrom-ADSyncToolsAadDistinguishedName 'CN={514635484D4B376E38307176645973555049486139513D3D}'
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
Espacio conector DistinguishedName de Azure AD
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertfrom-adsynctoolsimmutableid"></a>ConvertFrom-ADSyncToolsImmutableID
### SYNOPSIS
Conversión de ImmutableId (SourceAnchor) de Base64 en un valor GUID
### SYNTAX
```
ConvertFrom-ADSyncToolsImmutableID [-Value] <String> [<CommonParameters>]
```
### DESCRIPTION
Convierte el valor de ImmutableID de la cadena Base64 y devuelve un valor GUID. En caso de que la cadena Base64 no se pueda convertir en GUID, devuelve una matriz de bytes.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
ConvertFrom-ADSyncToolsImmutableID 'iGhmiAEBERG7uxI0VniQqw=='
```
#### <a name="example-2"></a>EJEMPLO 2
```
'iGhmiAEBERG7uxI0VniQqw==' | ConvertFrom-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
ImmutableId en formato Base64
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolsaaddistinguishedname"></a>ConvertTo-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Conversión de ImmutableId en el conector DistinguishedName de Azure AD
### SYNTAX
```
ConvertTo-ADSyncToolsAadDistinguishedName [-ImmutableId] <String> [<CommonParameters>]
```
### DESCRIPTION
Toma un valor ImmutableId (SourceAnchor), como OXI5HMK7n80qvdYsUPIHa9Q==, y lo convierte en el conector DistinguishedName de Azure AD correspondiente, como CN={514635484D4B376E38307176645973555049486139513D3D}.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
ConvertTo-ADSyncToolsAadDistinguishedName 'QF5HMK7n80qvdYsUPIHa9Q=='
```
### PARAMETERS
#### <a name="-immutableid"></a>-ImmutableId
ImmutableId (SourceAnchor)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolscloudanchor"></a>ConvertTo-ADSyncToolsCloudAnchor
### SYNOPSIS
Conversión del objeto Anchor de Base64 en CloudAnchor
### SYNTAX
```
ConvertTo-ADSyncToolsCloudAnchor [-Anchor] <String> [<CommonParameters>]
```
### DESCRIPTION
Toma un objeto Anchor de Base64 como VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAAWAzADMAZAAxADcAMQBkADcANwAAAA== y lo convierte en el valor de CloudAnchor correspondiente, como User_abc12345-1234-abcd-9876-ab0123456789.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
ConvertTo-ADSyncToolsCloudAnchor "VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA=="
```
#### <a name="example-2"></a>EJEMPLO 2
```
"VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==" | ConvertTo-ADSyncToolsCloudAnchor
```
### PARAMETERS
#### <a name="-anchor"></a>-Anchor
Anchor de Base64
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolsimmutableid"></a>ConvertTo-ADSyncToolsImmutableID
### SYNOPSIS
Conversión de GUID (ObjectGUID/ms-Ds-Consistency-Guid) en una cadena Base64
### SYNTAX
```
ConvertTo-ADSyncToolsImmutableID [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
Convierte un valor en formato de GUID, cadena GUID o matriz de bytes en una cadena Base64.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
ConvertTo-ADSyncToolsImmutableID '88888888-0101-3333-cccc-1234567890cd'
```
#### <a name="example-2"></a>EJEMPLO 2
```
'88888888-0101-3333-cccc-1234567890cd' | ConvertTo-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
GUID, cadena GUID o matriz de bytes
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolsaaddisconnectors"></a>Export-ADSyncToolsAadDisconnectors
### SYNOPSIS
Exportación de objetos Disconnector de Azure AD
### SYNTAX
```
Export-ADSyncToolsAadDisconnectors [[-SyncObjectType] <Object>] [<CommonParameters>]
```
### DESCRIPTION
Ejecuta la herramienta CSExport para exportar todos los objetos Disconnector a XML y, a continuación, toma esta salida XML y la convierte en un archivo CSV con: UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId y CloudAnchor.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Export-ADSyncToolsDisconnectors -SyncObjectType 'PublicFolder'
```
Exporta a CSV todos los objetos del Disconnector de PublicFolder.
#### <a name="example-2"></a>EJEMPLO 2
```
Export-ADSyncToolsDisconnectors
```
Exporta a CSV todos los objetos Disconnector.
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
Objecto ObjectType que se incluirá en la salida.
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
Use el argumento ObjectType si desea exportar objetos Disconnector solo de un tipo de objeto determinado.
### OUTPUTS
Exporta un archivo CSV con objetos Disconnector que contienen: 

UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId y CloudAnchor

## <a name="export-adsynctoolshybridaadjoinreport"></a>Export-ADSyncToolsHybridAadJoinReport
### SYNOPSIS
Genera un informe de los certificados almacenados en objetos del equipo de Active Directory; en concreto, certificados emitidos por la característica de unión a Azure AD híbrido.
### SYNTAX
#### <a name="singleobject"></a>SingleObject
```
Export-ADSyncToolsHybridAadJoinReport [-DN] <String> [[-Filename] <String>] [<CommonParameters>]
```
#### <a name="multipleobjects"></a>MultipleObjects
```
Export-ADSyncToolsHybridAadJoinReport [-OU] <String> [[-Filename] <String>] [<CommonParameters>]
```
### DESCRIPTION
Esta herramienta comprueba todos los certificados presentes en la propiedad UserCertificate de un objeto de equipo en AD y, para cada certificado no expirado presente, valida si el certificado se emitió para la característica de unión a Azure AD híbrido (es decir, el nombre del asunto es CN={ObjectGUID}).
Antes de la versión 1.4, Azure AD Connect sincronizaría con Azure AD cualquier equipo que incluyera al menos un certificado, pero en la versión 1.4 y posteriores de Azure AD Connect, el motor de ADSync puede identificar certificados de unión a Azure AD híbrido y "filtrará en la nube" (excluirá) el objeto de equipo resultante de la sincronización con Azure AD a menos que haya un certificado de unión a Azure AD híbrido válido.
Azure AD Connect eliminará de Azure AD (CloudFiltered=TRUE) los objetos de dispositivo de Azure AD que ya se sincronizaron con AD, pero no tienen un certificado de unión a Azure AD híbrido válido.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -DN 'CN=Computer1,OU=SYNC,DC=Fabrikam,DC=com'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -OU 'OU=SYNC,DC=Fabrikam,DC=com' -Filename "MyHybridAzureADjoinReport.csv" -Verbose
```
### PARAMETERS
#### <a name="-dn"></a>-DN
DistinguishedName del equipo
```yaml
Type: String
Parameter Sets: SingleObject
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-ou"></a>-OU
OrganizationalUnit de AD
```yaml
Type: String
Parameter Sets: MultipleObjects
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filename"></a>-Filename
Nombre de archivo CSV de salida (opcional)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Más información: [Descripción de Azure AD Connect 1.4.xx.x y desaparición del dispositivo](/troubleshoot/azure/active-directory/reference-connect-device-disappearance).

## <a name="export-adsynctoolsobjects"></a>Export-ADSyncToolsObjects
### SYNOPSIS
Exportación de objetos de Azure AD Connect a archivos XML
### SYNTAX
#### <a name="objectid"></a>ObjectId
```
Export-ADSyncToolsObjects [-ObjectId] <Object> [-Source] <Object> [-ExportSerialized] [<CommonParameters>]
```
#### <a name="distinguishedname"></a>DistinguishedName
```
Export-ADSyncToolsObjects [-DistinguishedName] <Object> [-ConnectorName] <Object> [-ExportSerialized]
 [<CommonParameters>]
```
### DESCRIPTION
Exporta objetos de ADSync internos desde el metaverso y objetos conectados asociados desde espacios conectores.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Export-ADSyncToolsObjects -ObjectId '9D220D58-0700-E911-80C8-000D3A3614C0' -Source Metaverse
```
#### <a name="example-2"></a>EJEMPLO 2
```
Export-ADSyncToolsObjects -ObjectId '9e220d58-0700-e911-80c8-000d3a3614c0' -Source ConnectorSpace
```
#### <a name="example-3"></a>EJEMPLO 3
```
Export-ADSyncToolsObjects -DistinguishedName 'CN=User1,OU=ADSync,DC=Contoso,DC=com' -ConnectorName 'Contoso.com'
```
### PARAMETERS
#### <a name="-objectid"></a>-ObjectId
ObjectId es el identificador único del objeto en el espacio conector o el metaverso respectivo.
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-source"></a>-Source
Source es la tabla donde reside el objeto que puede ser ConnectorSpace o Metaverse.
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName es el identificador del objeto en el espacio conector correspondiente.
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-connectorname"></a>-ConnectorName
ConnectorName es el nombre del espacio conector donde reside el objeto.
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-exportserialized"></a>-ExportSerialized
ExportSerialized exporta archivos XML adicionales.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolsrunhistory"></a>Export-ADSyncToolsRunHistory
### SYNOPSIS
Exportación del historial de ejecución de Azure AD Connect
### SYNTAX
```
Export-ADSyncToolsRunHistory [-TargetName] <String> [<CommonParameters>]
```
### DESCRIPTION
Función para exportar los resultados de perfil de ejecución y paso de ejecución de Azure AD Connect al formato CSV y XML, respectivamente.
El archivo CSV del perfil de ejecución resultante se puede importar en una hoja de cálculo y el archivo XML del paso de ejecución se puede importar con Import-Clixml.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Export-ADSyncToolsRunHistory -TargetName MyADSyncHistory
```
### PARAMETERS
#### <a name="-targetname"></a>-TargetName
Nombre del archivo de salida.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolssourceanchorreport"></a>Export-ADSyncToolsSourceAnchorReport
### SYNOPSIS
Exportación del informe ms-ds-Consistency-Guid
### SYNTAX
```
Export-ADSyncToolsSourceAnchorReport [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```
### DESCRIPTION
Genera un informe ms-ds-Consistency-Guid basado en un archivo CSV de importación desde Import-ADSyncToolsSourceAnchor.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsSourceAnchorReport -Output ".\AllSyncUsers-Report"
```
#### <a name="example-2"></a>EJEMPLO 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Usar un id. de inicio de sesión alternativo (correo)
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
Nombre de archivo de salida para archivos CSV y LOG
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsaadobject"></a>Get-ADSyncToolsAadObject
### SYNOPSIS
Obtención de objetos sincronizados de un objeto SyncObjectType determinado
### SYNTAX
```
Get-ADSyncToolsAadObject [-SyncObjectType] <Object> [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
Lee desde Azure AD todos los objetos sincronizados de una clase de objeto determinada (SyncObjectType).
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsAadObject -SyncObjectType 'publicFolder' -Credential $(Get-Credential)
```
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
Tipo de objeto
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Credenciales de administrador global de Azure AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

### OUTPUTS

Este cmdlet devuelve las propiedades "Shadow" sincronizadas por el cliente de sincronización, que pueden ser diferentes del valor real almacenado en la propiedad respectiva de Azure AD. Por ejemplo, el UPN de un usuario sincronizado con un sufijo de dominio no comprobado "user@nonverified.domain" tendrá el sufijo UPN en Azure AD convertido al dominio predeterminado del inquilino, "user@tenantname.onmicrosoft.com". En este caso, Get-ADSyncToolsAadObject devolverá el valor "Shadow" de "user@nonverified.domain", y no el valor real de Azure AD "user@tenantname.onmicrosoft.com".

## <a name="get-adsynctoolsmsdsconsistencyguid"></a>Get-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Obtención de un objeto de Active Directory ms-ds-ConsistencyGuid
### SYNTAX
```
Get-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Devuelve el valor del atributo mS-DS-ConsistencyGuid del objeto de Active Directory de destino en formato de GUID. Admite objetos de Active Directory en bosques de varios dominios.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>EJEMPLO 3
```
'User1@Contoso.com' | Get-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Objeto de destino en AD que se va a obtener.
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory
### SYNOPSIS
Obtención del historial de ejecución de Azure AD Connect
### SYNTAX
```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Función que devuelve el historial de ejecución de Azure AD Connect en formato XML
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>EJEMPLO 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
Número de días atrás para recopilar el historial (valor predeterminado = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsrunhistorylegacywmi"></a>Get-ADSyncToolsRunHistoryLegacyWmi
### SYNOPSIS
Obtención del historial de ejecución de Azure AD Connect de versiones anteriores de Azure AD Connect (WMI)
### SYNTAX
```
Get-ADSyncToolsRunHistoryLegacyWmi [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Función que devuelve el historial de ejecución de Azure AD Connect en formato XML
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>EJEMPLO 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
Número de días atrás para recopilar el historial (valor predeterminado = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolssqlbrowserinstances"></a>Get-ADSyncToolsSqlBrowserInstances
### SYNOPSIS
Obtención de instancias de SQL Server del servicio SQL Browser
### SYNTAX
```
Get-ADSyncToolsSqlBrowserInstances [[-Server] <String>]
```
### DESCRIPTION
Funciones y utilidades relacionadas con diagnósticos de SQL
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsSqlBrowserInstances -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nombre del servidor SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="get-adsynctoolstenantazureenvironment"></a>Get-ADSyncToolsTenantAzureEnvironment
### SYNOPSIS
Función auxiliar para obtener el entorno de Azure al que pertenece el usuario.
### SYNTAX
```
Get-ADSyncToolsTenantAzureEnvironment [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
Esta función llamará al punto de conexión de detección de Oauth para obtener CloudInstance y tenant_region_scope para determinar el entorno de Azure.
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration

### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsTenantAzureEnvironment -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Objeto de credenciales de PowerShell del usuario:

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
Objeto de credenciales de PowerShell del usuario

### OUTPUTS
Entorno de Azure (cadena)
## <a name="get-adsynctoolstls12"></a>Set-ADSyncToolsTls12
### SYNOPSIS
Obtiene la configuración de cliente\servidor de TLS 1.2 para .NET Framework.
### SYNTAX
```
Get-ADSyncToolsTls12 [<CommonParameters>]
```
### DESCRIPTION
Lee información del Registro sobre TLS 1.2 para .NET Framework:

| Ruta de acceso                                                         | Nombre                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | habilitado                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | habilitado                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Get-ADSyncToolsTls12
```
### PARAMETERS
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Para obtener más información: [Aplicar TLS 1.2 para Azure AD Connect](reference-connect-tls-enforcement.md).

## <a name="import-adsynctoolsobjects"></a>Import-ADSyncToolsObjects
### SYNOPSIS
Importación del objeto de Azure AD Connect de un archivo XML
### SYNTAX
```
Import-ADSyncToolsObjects [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Importa un objeto ADSync interno del archivo XML que se exportó mediante Export-ADSyncToolsObjects.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Import-ADSyncToolsObjects -Path .\20210224-003104_81275a23-0168-eb11-80de-00155d188c11_MV.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
Ruta de acceso del archivo XML que se importará.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="import-adsynctoolsrunhistory"></a>Import-ADSyncToolsRunHistory
### SYNOPSIS
Importación del historial de ejecución de Azure AD Connect
### SYNTAX
```
Import-ADSyncToolsRunHistory [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Función para importar los resultados del paso de ejecución de Azure AD Connect del archivo XML creado mediante Export-ADSyncToolsRunHistory.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Export-ADSyncToolsRunHistory -Path .\RunHistory-RunStep.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
Ruta de acceso del archivo XML que se importará.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="import-adsynctoolssourceanchor"></a>Import-ADSyncToolsSourceAnchor
### SYNOPSIS
Importar ImmutableID de Azure AD
### SYNTAX
```
Import-ADSyncToolsSourceAnchor [-Output] <String> [-IncludeSyncUsersFromRecycleBin] [<CommonParameters>]
```
### DESCRIPTION
Genera un archivo con todos los usuarios sincronizados de Azure AD que contiene el valor de ImmutableID en los requisitos de formato GUID: módulo de PowerShell MSOnline
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Import-ADSyncToolsSourceAnchor -OutputFile '.\AllSyncUsers.csv'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-output"></a>-Output
Archivo CSV de salida
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Obtener usuarios sincronizados de la papelera de reciclaje de Azure AD
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="invoke-adsynctoolssqlquery"></a>Invoke-ADSyncToolsSqlQuery
### SYNOPSIS
Invocación de una consulta SQL en una base de datos con fines de prueba
### SYNTAX
```
Invoke-ADSyncToolsSqlQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```
### DESCRIPTION
Funciones y utilidades relacionadas con diagnósticos de SQL
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823 | Invoke-ADSyncToolsSqlQuery
```
#### <a name="example-2"></a>EJEMPLO 2
```
$sqlConn = New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823
```
Invoke-ADSyncToolsSqlQuery -SqlConnection $sqlConn -Query 'SELECT *, database_id FROM sys.databases'
### PARAMETERS
#### <a name="-sqlconnection"></a>-SqlConnection
Conexión SQL
```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-query"></a>-Query
Consulta SQL
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: SELECT name, database_id FROM sys.databases
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

## <a name="remove-adsynctoolsaadobject"></a>Remove-ADSyncToolsAadObject
### SYNOPSIS
Eliminación del objeto sincronizado huérfano de Azure AD
### SYNTAX
#### <a name="csvinput"></a>CsvInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-InputCsvFilename] <Object> [-WhatIf] [-Confirm]
 [<CommonParameters>]
```
#### <a name="objectinput"></a>ObjectInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-SourceAnchor] <Object> [-SyncObjectType] <Object>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
Elimina de Azure AD los objetos sincronizados basados en SourceAnchor y ObjectType en lotes de 10 objetos. El archivo CSV se puede generar mediante Export-ADSyncToolsAadDisconnectors.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Remove-ADSyncToolsAadObject -InputCsvFilename .\DeleteObjects.csv -Credential (Get-Credential)
```
#### <a name="example-2"></a>EJEMPLO 2
```
Remove-ADSyncToolsAadObject -SourceAnchor '2epFRNMCPUqhysJL3SWL1A==' -SyncObjectType 'publicFolder' -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Credenciales de administrador global de Azure AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-inputcsvfilename"></a>-InputCsvFilename
Nombre de archivo de entrada CSV
```yaml
Type: Object
Parameter Sets: CsvInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-sourceanchor"></a>-SourceAnchor
SourceAnchor del objeto
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-syncobjecttype"></a>-SyncObjectType
Tipo de objeto
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
Muestra lo que sucedería si se ejecutara el cmdlet.
El cmdlet no se ejecuta.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
Le solicita su confirmación antes de ejecutar el cmdlet.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
InputCsvFilename debe apuntar a un archivo CSV con al menos 2 columnas: SourceAnchor y SyncObjectType.
### OUTPUTS
Muestra los resultados de la operación ExportDeletions. EXCLUSIÓN: los objetos distintos de User que tienen una papelera de reciclaje y cualquier otro tipo de objeto eliminado con esta función no se pueden recuperar.
## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates
### SYNOPSIS
Script para quitar los certificados caducados del atributo UserCertificate
### SYNTAX
```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```
### DESCRIPTION
Este script toma todos los objetos de un unidad organizativa de destino en su dominio de Active Directory, filtrado por clase de objeto (usuario o equipo), y elimina todos los certificados caducados presentes en el atributo UserCertificate.
De forma predeterminada (modo BackupOnly) solo realizará la copia de seguridad de los certificados caducados en un archivo y no realizará ningún cambio en AD.
Si usa -BackupOnly $false, cualquier certificado expirado presente en el atributo UserCertificate para estos objetos se quitará de Active Directory después de copiarse en el archivo.
La copia de seguridad de cada certificado se realizará con un nombre de archivo diferente: ObjectClass_ObjectGUID_CertThumprint.cer. El script también creará un archivo de registro en formato CSV que mostrará todos los usuarios con certificados válidos o caducados, incluida la acción real realizada (Omitido/Exportado/Eliminado).
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user
#### <a name="example-2"></a>EJEMPLO 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false
### PARAMETERS
#### <a name="-targetou"></a>-TargetOU
Unidad organizativa de destino para objetos de AD
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-backuponly"></a>-BackupOnly
BackupOnly no eliminará ningún certificado de AD
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-objectclass"></a>-ObjectClass
Filtro de clases de objetos
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState
### SYNOPSIS
Reparación del estado AutoUpgrade de Azure AD Connect
### SYNTAX
```
Repair-ADSyncToolsAutoUpgradeState
```
### DESCRIPTION
Corrige un problema del estado AutoUpgrade introducido en la compilación 1.1.524 (mayo de 2017) que deshabilita la búsqueda en línea de nuevas versiones cuando la opción AutoUpgrade está habilitada.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Repair-ADSyncToolsAutoUpgradeState
```
## <a name="resolve-adsynctoolssqlhostaddress"></a>Resolve-ADSyncToolsSqlHostAddress
### SYNOPSIS
Resolución de un nombre de servidor SQL
### SYNTAX
```
Resolve-ADSyncToolsSqlHostAddress [-Server] <String> [<CommonParameters>]
```
### DESCRIPTION
Funciones y utilidades relacionadas con diagnósticos de SQL
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Resolve-ADSyncToolsSqlHostAddress -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nombre del servidor SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="search-adsynctoolsadobject"></a>Search-ADSyncToolsADobject
### SYNOPSIS
Búsqueda de un objeto de Active Directory en el bosque de Active Directory por su UserPrincipalName, sAMAccountName o DistinguishedName.
### SYNTAX
```
Search-ADSyncToolsADobject [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Admite consultas de varios dominios y devuelve todas las propiedades necesarias, incluido mS-DS-ConsistencyGuid.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Search-ADSyncToolsADobject 'CN=user1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Search-ADSyncToolsADobject -Identity "user1@Contoso.com"
```
#### <a name="example-3"></a>EJEMPLO 3
```
Get-ADUser 'CN=user1,OU=Sync,DC=Contoso,DC=com' | Search-ADSyncToolsADobject
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Usuario de destino de AD para establecer ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="set-adsynctoolsmsdsconsistencyguid"></a>Set-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Establecimiento de un objeto de Active Directory ms-ds-ConsistencyGuid
### SYNTAX
```
Set-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
Establece un valor en el atributo mS-DS-ConsistencyGuid para el usuario de Active Directory de destino.
Admite objetos de Active Directory en bosques de varios dominios.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value '88666888-0101-1111-bbbb-1234567890ab'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-3"></a>EJEMPLO 3
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' '8d6c6818-018c-4f11-9bb8-1b04e2caa1b6'
```
#### <a name="example-4"></a>EJEMPLO 4
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-5"></a>EJEMPLO 5
```
'88666888-0101-1111-bbbb-1234567890ab' | Set-ADSyncToolsMsDsConsistencyGuid -Identity User1
```
#### <a name="example-6"></a>EJEMPLO 6
```
'GGhsjYwBEU+buBsE4sqhtg==' | Set-ADSyncToolsMsDsConsistencyGuid User1
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Objeto de destino de AD para establecer mS-DS-ConsistencyGuid.
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-value"></a>-Value
Valor que se va a establecer (ImmutableId, matriz de bytes, GUID, cadena GUID o cadena Base64).
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="set-adsynctoolstls12"></a>Set-ADSyncToolsTls12
### SYNOPSIS
Establece la configuración de cliente\servidor de TLS 1.2 para .NET Framework.
### SYNTAX
```
Set-ADSyncToolsTls12 [[-Enabled] <Boolean>] [<CommonParameters>]
```
### DESCRIPTION
Establece las entradas del Registro para habilitar o deshabilitar TLS 1.2 para .NET Framework:

| Ruta de acceso                                                         | Nombre                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | habilitado                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | habilitado                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

La ejecución del cmdlet sin parámetros habilitará TLS 1.2 para .NET Framework.

### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Set-ADSyncToolsTls12
```
#### <a name="example-2"></a>EJEMPLO 2
```
Set-ADSyncToolsTls12 -Enabled $true
```
### PARAMETERS
#### <a name="-enabled"></a>-Enabled
TLS 1.2 habilitado
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: True
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Para obtener más información: [Aplicar TLS 1.2 para Azure AD Connect](reference-connect-tls-enforcement.md).

## <a name="test-adsynctoolssqlnetworkport"></a>Test-ADSyncToolsSqlNetworkPort
### SYNOPSIS
Prueba del puerto de red de SQL Server
### SYNTAX
```
Test-ADSyncToolsSqlNetworkPort [[-Server] <String>] [[-Port] <String>]
```
### DESCRIPTION
Funciones y utilidades relacionadas con diagnósticos de SQL
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01'
```
#### <a name="example-2"></a>EJEMPLO 2
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01' -Port 1433
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nombre del servidor SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Puerto de SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport
### SYNOPSIS
Crea un archivo de seguimiento a partir de un paso de importación de Active Directory.
### SYNTAX
#### <a name="adconnectorxml"></a>ADConnectorXML
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADConnectorXML] <String> [<CommonParameters>]
```
#### <a name="adwatermarkinput"></a>ADwatermarkInput
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADwatermark] <String> [<CommonParameters>]
```
### DESCRIPTION
Hace un seguimiento de todas las consultas LDAP de una ejecución de importación de Active Directory desde un punto de control de marca de agua de Active Directory determinado (también denominado "cookie de partición"). Crea un archivo de seguimiento “.\ADimportTrace_aaaaMMddHHmmss.log” en la carpeta actual.
Para usar -ADConnectorXML, vaya a Synchronization Service Manager, haga clic con el botón derecho en el conector de AD y seleccione "Exportar conector".
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Trace Active Directory Import for user objects by providing an AD Connector XML file
```
Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Filter '(&(objectClass=user))' -ADConnectorXML .\ADConnector.xml
#### <a name="example-2"></a>EJEMPLO 2
```
Trace Active Directory Import for all objects by providing the Active Directory watermark (cookie) and AD Connector credential
```
$creds = Get-Credential Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Credential $creds -ADwatermark "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
### PARAMETERS
#### <a name="-dc"></a>-DC
Controlador de dominio de destino
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-rootdn"></a>-RootDN
DN raíz del bosque
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
Tipo de objetos de AD de los que se realizará el seguimiento.
Use "(&(objectClass=*))" para todos los tipos de objeto.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Proporcione las credenciales para ejecutar la consulta LDAP en AD.
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-ssl"></a>-SSL
Conexión SSL
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adconnectorxml"></a>-ADConnectorXML
Archivo XML de exportación del conector de AD: haga clic con el botón derecho en el conector de AD y seleccione "Exportar conector".
```yaml
Type: String
Parameter Sets: ADConnectorXML
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adwatermark"></a>-ADwatermark
La entrada manual de la marca de agua, en lugar del archivo XML como por ejemplo $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
```yaml
Type: String
Parameter Sets: ADwatermarkInput
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery
### SYNOPSIS
Seguimiento de consultas LDAP
### SYNTAX
```
Trace-ADSyncToolsLdapQuery [-RootDN] <String> [-Credential] <PSCredential> [[-Server] <String>]
 [[-Port] <Int32>] [-Filter <String>] [<CommonParameters>]
```
### DESCRIPTION
{{Indicar descripción}}
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Trace-ADSyncToolsLdapQuery -RootDN "DC=Contoso,DC=com" -Credential $Credential
```
### PARAMETERS
#### <a name="-rootdn"></a>-RootDN
DistinguishedName de bosque o dominio
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Credenciales de AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-server"></a>-Server
Nombre del controlador de dominio (opcional)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Puerto del controlador de dominio (predeterminado: 389)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: 389
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
Filtro LDAP (valor predeterminado: objectClass=*)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="update-adsynctoolssourceanchor"></a>Update-ADSyncToolsSourceAnchor
### SYNOPSIS
Actualiza los usuarios con el nuevo ConsistencyGuid (ImmutableId)
### SYNTAX
```
Update-ADSyncToolsSourceAnchor [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String> [-Action] <String>
 [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
Actualiza los usuarios con el nuevo valor de ConsistencyGuid (ImmutableId) tomado del informe ConsistencyGuid. Esta función admite el modificador WhatIf. Nota: El informe ConsistencyGuid debe importarse con el delimitador de tabulador.
### EXAMPLES
#### <a name="example-1"></a>EJEMPLO 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2 -WhatIf
```
#### <a name="example-2"></a>EJEMPLO 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-action"></a>-Action
Acción
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
Nombre de archivo de salida para archivos LOG
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
Muestra lo que sucedería si se ejecutara el cmdlet.
El cmdlet no se ejecuta.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
Le solicita su confirmación antes de ejecutar el cmdlet.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).