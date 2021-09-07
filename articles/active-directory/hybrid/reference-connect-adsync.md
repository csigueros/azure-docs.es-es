---
title: 'Azure AD Connect: Referencia para ADSync de PowerShell | Microsoft Docs'
description: En este documento se proporciona información de referencia para el módulo de PowerShell ADSync.psm1.
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
ms.openlocfilehash: 19d3b2dc72e31cef290bd5253e7b173d624488af
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228745"
---
# <a name="azure-ad-connect--adsync-powershell-reference"></a>Azure AD Connect: Referencia para ADSync de PowerShell
En la siguiente documentación se proporciona información de referencia para el módulo de PowerShell ADSync.psm1 que se incluye con Azure AD Connect.


## <a name="add-adsyncaddsconnectoraccount"></a>Add-ADSyncADDSConnectorAccount

 ### <a name="synopsis"></a>SINOPSIS
 Este cmdlet restablece la contraseña de la cuenta de servicio y la actualiza en Azure AD y en el motor de sincronización.

 ### <a name="syntax"></a>SINTAXIS
  #### <a name="byidentifier"></a>byIdentifier
   ```
     Add-ADSyncADDSConnectorAccount [-Identifier] <Guid> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 #### <a name="byname"></a>byName
   ```
     Add-ADSyncADDSConnectorAccount [-Name] <String> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 ### <a name="description"></a>DESCRIPTION
 Este cmdlet restablece la contraseña de la cuenta de servicio y la actualiza en Azure AD y en el motor de sincronización.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
   ```powershell
     PS C:\> Add-ADSyncADDSConnectorAccount -Name contoso.com -EACredential $EAcredentials
   ```

 Restablece la contraseña de la cuenta de servicio conectada a contoso.com.

 ### <a name="parameters"></a>PARAMETERS

   #### <a name="-eacredential"></a>-EACredential
   Credenciales de una cuenta de administrador de la empresa en Active Directory.

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
   ```

  #### <a name="-identifier"></a>-Identifier
  Identificador del conector cuya contraseña de cuenta de servicio debe restablecerse.

   ```yaml
     Type: Guid
     Parameter Sets: byIdentifier
     Aliases:

     Required: True
     Position: 0
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="-name"></a>-Name
  Nombre del conector.

   ```yaml
     Type: String
     Parameter Sets: byName
     Aliases:
 
     Required: True
     Position: 1
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="commonparameters"></a>CommonParameters
  Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

  #### <a name="systemguid"></a>System.Guid

  #### <a name="systemstring"></a>System.String

 ### <a name="outputs"></a>SALIDAS

  #### <a name="systemobject"></a>System.Object



## <a name="disable-adsyncexportdeletionthreshold"></a>Disable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>SINOPSIS
 Deshabilita la característica para el umbral de eliminación en la fase de exportación.

 ### <a name="syntax"></a>SINTAXIS
   
   ```
     Disable-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm]
     [<CommonParameters>]
   ```

 ### <a name="description"></a>DESCRIPTION
 Deshabilita la característica para el umbral de eliminación en la fase de exportación.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
   ```powershell
     PS C:\> Disable-ADSyncExportDeletionThreshold -AADCredential $aadCreds
   ```

 Usa las credenciales de AAD proporcionadas para deshabilitar la característica para el umbral de eliminación de exportación.

 ### <a name="parameters"></a>PARAMETERS

  #### <a name="-aadcredential"></a>-AADCredential
  Credencial de AAD.

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: 1
     Default value: None
     Accept pipeline input: True (ByPropertyName)
     Accept wildcard characters: False
  ```

  #### <a name="-confirm"></a>-Confirm
  Conmutador de parámetro para solicitar confirmación.

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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

  #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>SALIDAS

  #### <a name="systemobject"></a>System.Object

## <a name="enable-adsyncexportdeletionthreshold"></a>Enable-ADSyncExportDeletionThreshold para habilitar el umbral.

 ### <a name="synopsis"></a>SINOPSIS
 Habilita la característica de umbral de eliminación de exportación y establece un valor para el umbral.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Enable-ADSyncExportDeletionThreshold [-DeletionThreshold] <UInt32> [[-AADCredential] <PSCredential>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Habilita la característica de umbral de eliminación de exportación y establece un valor para el umbral.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Enable-ADSyncExportDeletionThreshold -DeletionThreshold 777 -AADCredential $aadCreds
 ```

 Habilita la característica de umbral de eliminación de exportación y establece el umbral de eliminación en 777.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 Credencial de AAD.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="-deletionthreshold"></a>-DeletionThreshold
 Umbral de eliminación.

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemuint32"></a>System.UInt32
 
 #### <a name="sytemmanagementautomationpscredential"></a>Sytem.Management.Automation.PSCredential

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncautoupgrade"></a>Get-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene el estado de AutoUpgrade en la instalación.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncAutoUpgrade [-Detail] [<CommonParameters>]
 ``` 

 ### <a name="description"></a>DESCRIPTION
 Obtiene el estado de AutoUpgrade en la instalación.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncAutoUpgrade -Detail
 ```

 Devuelve el estado AutoUpgrade de la instalación y muestra el motivo de la suspensión si se suspende AutoUpgrade.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-detail"></a>-Detail
 Si el estado de AutoUpgrade es suspendido, el uso de este parámetro muestra el motivo de la suspensión.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobject"></a>Get-ADSyncCSObject

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene el objeto de espacio de conector especificado.

 ### <a name="syntax"></a>SINTAXIS
 
 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Get-ADSyncCSObject [-Identifier] <Guid> [<CommonParameters>]
 ```
 
 #### <a name="searchbyconnectoridentifierdistinguishedname"></a>SearchByConnectorIdentifierDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectoridentifier"></a>SearchByConnectorIdentifier
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectornamedistinguishedname"></a>SearchByConnectorNameDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectorname"></a>SearchByConnectorName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Obtiene el objeto de espacio de conector especificado.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 Obtiene el objeto CS para el usuario fabrikam en el dominio contoso.com.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Identificador del conector.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier 
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 Nombre distintivo del objeto de espacio de conector.

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Identificador del objeto de espacio de conector.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-maxresultcount"></a>-MaxResultCount
 Recuento máximo del conjunto de resultados.

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-skipdnvalidation"></a>-SkipDNValidation
 Conmutador de parámetro para omitir la validación de DN.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-startindex"></a>-StartIndex
 Índice de inicio desde el que se devuelve el recuento.

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-transient"></a>-Transient
 Conmutador de parámetro para obtener objetos CS transitorios.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier, SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobjectlog"></a>Get-ADSyncCSObjectLog

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene las entradas del registro de objetos del espacio de conector.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncCSObjectLog [-Identifier] <Guid> [-Count] <UInt32> [<CommonParameters>]
 ```
 
 ### <a name="description"></a>DESCRIPTION
 Obtiene las entradas del registro de objetos del espacio de conector.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncCSObjectLog -Identifier "00000000-0000-0000-0000-000000000000" -Count 1
 ```

 Devuelve un objeto con el identificador especificado.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-count"></a>-Count
 Número máximo esperado de entradas del registro de objetos de espacio de conector que se van a recuperar.

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Identificador del objeto de espacio de conector.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncdatabaseconfiguration"></a>Get-ADSyncDatabaseConfiguration

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene la configuración de la base datos ADSync.
 
 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncDatabaseConfiguration [<CommonParameters>]
 ```
 
 ### <a name="description"></a>DESCRIPTION
 Obtiene la configuración de la base datos ADSync.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncDatabaseConfiguration
 ```

 Obtiene la configuración de la base datos ADSync.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncexportdeletionthreshold"></a>Get-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene el umbral de eliminación de exportación de AAD.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Obtiene el umbral de eliminación de exportación de AAD.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncExportDeletionThreshold -AADCredential $aadCreds
 ```

 Obtiene el umbral de eliminación de exportación de AAD con las credenciales de AAD especificadas.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 Credencial de AAD.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncmvobject"></a>Get-ADSyncMVObject

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene un objeto de metaverso.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncMVObject -Identifier <Guid> [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Obtiene un objeto de metaverso.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncMVObject -Identifier "00000000-0000-0000-0000-000000000000"
 ```

 Obtiene el objeto de metaverso con el identificador especificado.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-identifier"></a>-Identifier
 Identificador del objeto de metaverso.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncrunprofileresult"></a>Get-ADSyncRunProfileResult

 ### <a name="synopsis"></a>SINOPSIS
 Procesa las entradas del cliente y recupera los resultados del perfil de ejecución.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncRunProfileResult [-RunHistoryId <Guid>] [-ConnectorId <Guid>] [-RunProfileId <Guid>]
 [-RunNumber <Int32>] [-NumberRequested <Int32>] [-RunStepDetails] [-StepNumber <Int32>] [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Procesa las entradas del cliente y recupera los resultados del perfil de ejecución.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncRunProfileResult -ConnectorId "00000000-0000-0000-0000-000000000000"
 ```

 Recupera todos los resultados del perfil de ejecución de sincronización para el conector especificado.

 ### <a name="parameters"></a>PARAMETERS

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

 #### <a name="-connectorid"></a>-ConnectorId
 Identificador de conector.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-numberrequested"></a>-NumberRequested
 Número máximo de devoluciones.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 Identificador de una ejecución específica.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runnumber"></a>-RunNumber
 Número de ejecución de una ejecución específica.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofileid"></a>-RunProfileId
 Identificador de perfil de ejecución de una ejecución específica.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runstepdetails"></a>-RunStepDetails
 Conmutador de parámetro para los detalles del paso de ejecución.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 Filtra por el número de paso.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncrunstepresult"></a>Get-ADSyncRunStepResult

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene el resultado del paso de ejecución de la Sincronización de AD.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncRunStepResult [-RunHistoryId <Guid>] [-StepHistoryId <Guid>] [-First] [-StepNumber <Int32>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Obtiene el resultado del paso de ejecución de la Sincronización de AD.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncRunStepResult -RunHistoryId "00000000-0000-0000-0000-000000000000"
 ```

 Obtiene el resultado del paso de ejecución de la Sincronización de AD de la ejecución especificada.

 ### <a name="parameters"></a>PARAMETERS

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

 #### <a name="-first"></a>-First
 Conmutador de parámetro para obtener solo el primer objeto.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 Id. de una ejecución específica.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stephistoryid"></a>-StepHistoryId
 Id. de un paso de ejecución específico.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 Número del paso.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object



## <a name="get-adsyncscheduler"></a>Get-ADSyncScheduler

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncScheduler [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Obtiene la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Get-ADSyncScheduler
 ```

 Obtiene la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncschedulerconnectoroverride"></a>Get-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>SINOPSIS
 Obtiene los valores de reemplazo del programador de Sincronización de AD para los conectores especificados.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Get-ADSyncSchedulerConnectorOverride [-ConnectorIdentifier <Guid>] [-ConnectorName <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Obtiene los valores de reemplazo del programador de Sincronización de AD para los conectores especificados.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1 
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride -ConnectorName "contoso.com"
 ```

 Obtiene los valores de reemplazo del programador de Sincronización de AD para el conector "contoso.com".

 #### <a name="example-2"></a>Ejemplo 2
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride
 ```

 Obtiene todos los valores de reemplazo del programador de Sincronización de AD.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Identificador de conector.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsynccsobjectpasswordhashsync"></a>Invoke-ADSyncCSObjectPasswordHashSync

 ### <a name="synopsis"></a>SINOPSIS
 Sincronice el hash de contraseña para el objeto de espacio de conector de AD especificado.

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="searchbydistinguishedname"></a>SearchByDistinguishedName
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-ConnectorName] <String> [-DistinguishedName] <String>
 [<CommonParameters>]
 ```

 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-Identifier] <Guid> [<CommonParameters>]
 ```

 #### <a name="csobject"></a>CSObject
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-CsObject] <CsObject> [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Sincronice el hash de contraseña para el objeto de espacio de conector de AD especificado.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Invoke-ADSyncCSObjectPasswordHashSync -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DN=contoso,DN=com"
 ```

 Sincroniza el hash de contraseña para el objeto especificado.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-csobject"></a>-CsObject
 Objeto de espacio de conector.

 ```yaml
 Type: CsObject
 Parameter Sets: CSObject
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 Nombre distintivo del objeto de espacio de conector.

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Identificador del objeto de espacio de conector.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile

 ### <a name="synopsis"></a>SINOPSIS
 Invoca un perfil de ejecución específico.

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="connectorname"></a>ConnectorName
 ```
 Invoke-ADSyncRunProfile -ConnectorName <String> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Invoke-ADSyncRunProfile -ConnectorIdentifier <Guid> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Invoca un perfil de ejecución específico.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Invoke-ADSyncRunProfile -ConnectorName "contoso.com" -RunProfileName Export
 ```

 Invoca una exportación en el conector "contoso.com".

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Identificador del conector.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-resume"></a>-Resume
 Conmutador de parámetro para intentar reanudar un perfil de ejecución detenido o semiacabado previamente.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofilename"></a>-RunProfileName
 Nombre del perfil de ejecución que se invocará en el conector seleccionado.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="remove-adsyncaadserviceaccount"></a>Remove-ADSyncAADServiceAccount

 ### <a name="synopsis"></a>SINOPSIS
 Elimina una o todas las cuentas de servicio de AAD existentes en el inquilino de AAD (asociadas a las credenciales especificadas).

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="serviceaccount"></a>ServiceAccount
 ```
 Remove-ADSyncAADServiceAccount [-AADCredential] <PSCredential> [-Name] <String> [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 #### <a name="serviceprincipal"></a>ServicePrincipal
 ```
 Remove-ADSyncAADServiceAccount [-ServicePrincipal] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Elimina una o todas las cuentas de servicio de AAD existentes en el inquilino de AAD (asociadas a las credenciales especificadas).

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Remove-ADSyncAADServiceAccount -AADCredential $aadcreds -Name contoso.com
 ```

 Elimina todas las cuentas de servicio de AAD existentes en contoso.com.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 Credencial de AAD.

 ```yaml
 Type: PSCredential
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="-name"></a>-Name
 Nombre de la cuenta.

 ```yaml
 Type: String
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-serviceprincipal"></a>-ServicePrincipal
 Entidad de servicio de la cuenta.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: ServicePrincipal
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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 #### <a name="systemstring"></a>System.String

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncautoupgrade"></a>Set-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>SINOPSIS
 Cambia el estado de AutoUpgrade de la instalación entre habilitado y deshabilitado.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Set-ADSyncAutoUpgrade [-AutoUpgradeState] <AutoUpgradeConfigurationState> [[-SuspensionReason] <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Establece el estado de AutoUpgrade en la instalación. Este cmdlet solo debe usarse para cambiar el estado de AutoUpgrade entre habilitado y deshabilitado. Solo el sistema debe establecer el estado en Suspendido.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Set-ADSyncAutoUpgrade -AutoUpgradeState Enabled
 ```

 Establece el estado de AutoUpgrade en habilitado.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-autoupgradestate"></a>-AutoUpgradeState
 Estado de AutoUpgrade. Valores aceptados: Suspended, Enabled, Disabled.

 ```yaml
 Type: AutoUpgradeConfigurationState
 Parameter Sets: (All)
 Aliases:
 Accepted values: Suspended, Enabled, Disabled

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-suspensionreason"></a>-SuspensionReason
 Motivo de la suspensión. Solo el sistema debe establecer el estado de AutoUpgrade en suspendido.

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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncscheduler"></a>Set-ADSyncScheduler

 ### <a name="synopsis"></a>SINOPSIS
 Establece la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Set-ADSyncScheduler [[-CustomizedSyncCycleInterval] <TimeSpan>] [[-SyncCycleEnabled] <Boolean>]
 [[-NextSyncCyclePolicyType] <SynchronizationPolicyType>] [[-PurgeRunHistoryInterval] <TimeSpan>]
 [[-MaintenanceEnabled] <Boolean>] [[-SchedulerSuspended] <Boolean>] [-Force] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Establece la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Set-ADSyncScheduler -SyncCycleEnabled $true
 ```

 Establece la configuración del ciclo de sincronización actual para SyncCycleEnabled en True.
 
 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-customizedsynccycleinterval"></a>-CustomizedSyncCycleInterval
 Especifique el valor de intervalo de tiempo para el intervalo de sincronización personalizado que quiere establecer.
Si quiere ejecutar con la configuración más baja permitida, establezca este parámetro en null.

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-force"></a>-Force
 Conmutador de parámetro para forzar la configuración de un valor.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 6
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-maintenanceenabled"></a>-MaintenanceEnabled
 Parámetro para establecer MaintenanceEnabled.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 4
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-nextsynccyclepolicytype"></a>-NextSyncCyclePolicyType
 Parámetro para establecer NextSyncCyclePolicyType. Valores aceptados: Unspecified, Delta, Initial.

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 Parámetro para establecer PurgeRunHistoryInterval.

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-schedulersuspended"></a>-SchedulerSuspended
 Parámetro para establecer SchedulerSuspended.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 5
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-synccycleenabled"></a>-SyncCycleEnabled
 Parámetro para establecer SyncCycleEnabled.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemnullable1systemtimespan-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.TimeSpan, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncschedulerconnectoroverride"></a>Set-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>SINOPSIS
 Establece la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 #### <a name="connectorname"></a>ConnectorName
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorName <String> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Establece la configuración actual del ciclo de sincronización para el programador de sincronización.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Set-ADSyncSchedulerConnectorOverride -Connectorname "contoso.com" -FullImportRequired $true
 -FullSyncRequired $false
 ```

 Establece la configuración del ciclo de sincronización para que el conector "contoso.com" requiera una importación completa y no requiera una sincronización completa.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Identificador de conector.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullimportrequired"></a>-FullImportRequired
 Se establece como verdadero para requerir la importación completa en el siguiente ciclo.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullsyncrequired"></a>-FullSyncRequired
 Se establece como verdadero para requerir la sincronización completa en el siguiente ciclo.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemguid"></a>System.Guid

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="start-adsyncpurgerunhistory"></a>Start-ADSyncPurgeRunHistory

 ### <a name="synopsis"></a>SINOPSIS
 Cmdlet para purgar el historial de ejecución anterior al intervalo de tiempo especificado.

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="online"></a>online (en línea)
 ```
 Start-ADSyncPurgeRunHistory [[-PurgeRunHistoryInterval]  <TimeSpan>] [<CommonParameters>]
 ```

 #### <a name="offline"></a>sin conexión
 ```
 Start-ADSyncPurgeRunHistory [-Offline] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Cmdlet para purgar el historial de ejecución anterior al intervalo de tiempo especificado.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Start-ADSyncPurgeRunHistory -PurgeRunHistoryInterval (New-Timespan -Hours 5)
 ```

 Purga todo el historial de ejecución con una antigüedad superior a 5 horas.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-offline"></a>-Offline
 Purga todo el historial de ejecución de la base de datos mientras el servicio está sin conexión.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: offline
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 Intervalo para el que se va a conservar el historial.

 ```yaml
 Type: TimeSpan
 Parameter Sets: online
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemtimespan"></a>System.TimeSpan

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="start-adsyncsynccycle"></a>Start-ADSyncSyncCycle

 ### <a name="synopsis"></a>SINOPSIS
 Desencadena un ciclo de sincronización.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Start-ADSyncSyncCycle [[-PolicyType] <SynchronizationPolicyType>] [[-InteractiveMode] <Boolean>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Desencadena un ciclo de sincronización.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Start-ADSyncSyncCycle -PolicyType Initial
 ```

 Desencadena un ciclo de sincronización con un tipo de directiva inicial.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-interactivemode"></a>-InteractiveMode
 Diferencia entre el modo interactivo (línea de comandos) y el modo de script o código (llamadas desde otro código).

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-policytype"></a>-PolicyType
 Tipo de directiva que se ejecutará. Valores aceptados: Unspecified, Delta, Initial.

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemboolean"></a>System.Boolean

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncrunprofile"></a>Stop-ADSyncRunProfile

 ### <a name="synopsis"></a>SINOPSIS
 Busca y detiene todos los conectores ocupados o especificados.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Stop-ADSyncRunProfile [[-ConnectorName] <String>] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Busca y detiene todos los conectores ocupados o especificados.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Stop-ADSyncRunProfile -ConnectorName "contoso.com"
 ```

 Detiene cualquier sincronización en ejecución en "contoso.com".

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.
Si no se proporciona, se detendrán todos los conectores ocupados.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncsynccycle"></a>Stop-ADSyncSyncCycle

 ### <a name="synopsis"></a>SINOPSIS
 Indica al servidor que detenga el ciclo de sincronización que se está ejecutando actualmente.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Stop-ADSyncSyncCycle [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Indica al servidor que detenga el ciclo de sincronización que se está ejecutando actualmente.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Stop-ADSyncSyncCycle
 ```

 Indica al servidor que detenga el ciclo de sincronización que se está ejecutando actualmente.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="sync-adsynccsobject"></a>Sync-ADSyncCSObject

 ### <a name="synopsis"></a>SINOPSIS
 Ejecuta la versión preliminar de sincronización en el objeto de espacio de conector.

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="connectorname_objectdn"></a>ConnectorName_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorName <String> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier_objectdn"></a>ConnectorIdentifier_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorIdentifier <Guid> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="objectidentifier"></a>ObjectIdentifier
 ```
 Sync-ADSyncCSObject -Identifier <Guid> [-Commit] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Ejecuta la versión preliminar de sincronización en el objeto de espacio de conector.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Sync-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 Devuelve una vista previa de la sincronización para el objeto especificado.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-commit"></a>-Commit
 Conmutador de parámetro para confirmación.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 Identificador del conector.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 Nombre del conector.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 Nombre distintivo del objeto de espacio de conector.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN, ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 Identificador del objeto de espacio de conector.

 ```yaml
 Type: Guid
 Parameter Sets: ObjectIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="none"></a>Ninguno

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncazureserviceconnectivity"></a>Test-AdSyncAzureServiceConnectivity

 ### <a name="synopsis"></a>SINOPSIS
 Investiga e identifica problemas de conectividad para Azure AD.

 ### <a name="syntax"></a>SINTAXIS

 #### <a name="byenvironment"></a>ByEnvironment
 ```
 Test-AdSyncAzureServiceConnectivity [-AzureEnvironment] <Identifier> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 #### <a name="bytenantname"></a>ByTenantName
 ```
 Test-AdSyncAzureServiceConnectivity [-Domain] <String> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Investiga e identifica problemas de conectividad para Azure AD.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Test-AdSyncAzureServiceConnectivity -AzureEnvironment Worldwide -Service SecurityTokenService -CurrentUser
 ```

 Devuelve "True" si no hay problemas de conectividad.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-azureenvironment"></a>-AzureEnvironment
 Entorno de Azure para probar. Valores aceptados: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default.

 ```yaml
 Type: Identifier
 Parameter Sets: ByEnvironment
 Aliases:
 Accepted values: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-currentuser"></a>-CurrentUser
 Usuario que ejecuta el cmdlet.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-domain"></a>-Domain
 Dominio cuya conectividad se está probando.

 ```yaml
 Type: String
 Parameter Sets: ByTenantName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-service"></a>-Service
 Servicio cuya conectividad se está probando.

 ```yaml
 Type: AzureService
 Parameter Sets: (All)
 Aliases:
 Accepted values: SecurityTokenService, AdminWebService

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="microsoftonlinedeploymentclientframeworkmicrosoftonlineinstanceidentifier"></a>Microsoft.Online.Deployment.Client.Framework.MicrosoftOnlineInstance+Identifier

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1microsoftonlinedeploymentclientframeworkazureservice-microsoftonlinedeploymentclientframework-version1600-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.Online.Deployment.Client.Framework.AzureService, Microsoft.Online.Deployment.Client.Framework, Version=1.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncuserhaspermissions"></a>Test-AdSyncUserHasPermissions

 ### <a name="synopsis"></a>SINOPSIS
 Cmdlet para comprobar si el usuario de ADMA tiene los permisos necesarios.

 ### <a name="syntax"></a>SINTAXIS

 ```
 Test-AdSyncUserHasPermissions [-ForestFqdn] <String> [-AdConnectorId] <Guid>
 [-AdConnectorCredential] <PSCredential> [-BaseDn] <String> [-PropertyType] <String> [-PropertyValue] <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Cmdlet para comprobar si el usuario de ADMA tiene los permisos necesarios.

 ### <a name="examples"></a>EJEMPLOS

 #### <a name="example-1"></a>Ejemplo 1
 ```powershell
 PS C:\> Test-AdSyncUserHasPermissions -ForestFqdn "contoso.com" -AdConnectorId "00000000-0000-0000-000000000000"
 -AdConnectorCredential $connectorAcctCreds -BaseDn "CN=fabrikam,CN=Users,DC=contoso,DC=com" -PropertyType "Allowed-Attributes" -PropertyValue "name"
 ```

 Comprueba si el usuario de ADMA tiene permisos para acceder a la propiedad "name" del usuario "fabrikam".

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-adconnectorcredential"></a>-AdConnectorCredential
 Credenciales de cuenta de usuario de AD.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-adconnectorid"></a>-AdConnectorId
 Id de conector de AD.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-basedn"></a>-BaseDn
 DN base del objeto que se debe comprobar.

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

 #### <a name="-forestfqdn"></a>-ForestFqdn
 Nombre del bosque.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-propertytype"></a>-PropertyType
 Tipo de permiso que está buscando. Valores aceptados: Allowed-Attributes, Allowed-Attributes-Effective, Allowed-Child-Classes, Allowed-Child-Classes-Effective.

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

 #### <a name="-propertyvalue"></a>-PropertyValue
 Valor que busca en el atributo PropertyType.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 5
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

 #### <a name="commonparameters"></a>CommonParameters
 Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable. Para obtener más información, consulta [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

 ### <a name="inputs"></a>ENTRADAS

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>SALIDAS

 #### <a name="systemobject"></a>System.Object

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la identidad híbrida?](./whatis-hybrid-identity.md)
- [¿Qué es Azure AD Connect y Connect Health?](whatis-azure-ad-connect.md)