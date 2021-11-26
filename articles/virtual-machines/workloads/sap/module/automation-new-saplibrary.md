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
title: New-SAPLibrary
description: Arranque de una nueva biblioteca SAP en el plano de control.
ms.openlocfilehash: 0f060d042aeb86c13f4820baad5832d3ea6c1720
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725383"
---
# New-SAPLibrary

## Sinopsis
El comando `New-SAPLibrary` configura una nueva biblioteca SAP.

## Sintaxis

```powershell
New-SAPLibrary [-Parameterfile] <String> [[-DeployerFolderRelativePath] <String>] [-Silent] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

## Descripción
La biblioteca SAP proporciona el almacenamiento para los archivos de estado de Terraform y los soportes de instalación de SAP.

## Ejemplos

### Ejemplo 1
```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPLibrary -Parameterfile .\MGMT-WEEU-SAP_LIBRARY.tfvars -DeployerFolderRelativePath ..\..\DEPLOYER\MGMT-WEEU-DEP00-INFRASTRUCTURE\
```

## Parámetros

### `-Parameterfile`
Establece el archivo de parámetros para la biblioteca SAP. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#sap-library).

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

### -DeployerFolderRelativePath
Establece la ruta de acceso de carpeta relativa a la carpeta que contiene el archivo de parámetros de la máquina virtual del implementador, denominado `terraform.tfstate `.

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

### -Silent
Habilita la implementación silenciosa.

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
El cmdlet `CommonParameters` admite los parámetros comunes: `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-InformationAction`, `-InformationVariable`, `-OutVariable`, `-OutBuffer`, `-PipelineVariable`, `-Verbose`, `-WarningAction` y `-WarningVariable`. Para obtener más información, consulta [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).


## Notas
v0.9: Versión inicial

Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)

