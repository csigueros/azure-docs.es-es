---
external help file: SAPDeploymentUtilities-help.xml
Module Name: SAPDeploymentUtilities
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: New-SAPSystem
description: Implementación de un nuevo sistema SAP
ms.openlocfilehash: 888fb0f1ee736dcb495117da8c931c76cf7d75a6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725376"
---
# New-SAPSystem

## Sinopsis
Puede usar el comando `New-SAPSystem` para implementar un nuevo sistema SAP.

## Sintaxis

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPSystem [-Parameterfile] <String> [-Type] <SAP_Types> [[-DeployerStateFileKeyName] <String>]
 [[-LandscapeStateFileKeyName] <String>] [[-StorageAccountName] <String>] [-Force] [-Silent] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

## Descripción
El comando `New-SAPSystem` implementa un nuevo sistema SAP.

## Ejemplos

### Ejemplo 1

Implementa o actualiza un sistema SAP.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPSystem -Parameterfile .\DEV-WEEU-SAP00-X00.tfvars -Type sap_system
```

### Ejemplo 2

Implementa o actualiza un sistema SAP.


```powershell
Import-Module "SAPDeploymentUtilities.psd1"


New-SAPSystem -Parameterfile .\DEV-WEEU-SAP00-X00.tfvars -Type sap_system -DeployerStateFileKeyName MGMT-WEEU-DEP00-INFRASTRUCTURE.terraform.tfstate -LandscapeStateFileKeyName DEV-WEEU-SAP01-INFRASTRUCTURE.terraform.tfstate
```

### Ejemplo 3
```powershell
Import-Module "SAPDeploymentUtilities.psd1"
New-SAPSystem -Parameterfile .\MGMT-WEEU-SAP_LIBRARY.tfvars -Type sap_library
```

## Parámetros

### `-Parameterfile`
Establece el archivo de parámetros para el sistema SAP.

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

### `-Type`
Establece el tipo de sistema SAP que se va a implementar. Los valores válidos son `sap_deployer`, `sap_library`, `sap_landscape` y `sap_system`.

```yaml
Type: SAP_Types
Parameter Sets: (All)
Aliases:
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -DeployerStateFileKeyName
Establece el nombre del archivo de estados para la implementación del implementador.

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

### -LandscapeStateFileKeyName
Establece el nombre del archivo de estados para la implementación de la zona de cargas de trabajo.


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

### -StorageAccountName
Establece el nombre de la cuenta de almacenamiento del archivo de estados de Terraform opcional.

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

### -Force
Limpia la configuración local.

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

### -Silent
Implementación sin preguntar

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

### -WhatIf
Muestra lo que sucede si se ejecuta el cmdlet. Sin embargo, el cmdlet no realiza ningún cambio.

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

### -Confirm
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

### CommonParameters
Este cmdlet admite los parámetros comunes: `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-InformationAction`, `-InformationVariable`, `-OutVariable`, `-OutBuffer`, `-PipelineVariable`, `-Verbose`, `-WarningAction` y `-WarningVariable`. Para obtener más información, consulta [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

## Notas
v0.9: Versión inicial

Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
