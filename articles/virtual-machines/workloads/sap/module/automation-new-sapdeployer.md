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
title: New-SAPDeployer
description: Arranque de un nuevo implementador en el plano de control.
ms.openlocfilehash: 26c2510726f8d24337d92546c06ec33accfb056e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725388"
---
# New-SAPDeployer

## Sinopsis
Puede usar el comando `New-SAPDeployer` para configurar una nueva máquina virtual del implementador en el plano de control.

## Sintaxis

```powershell
New-SAPDeployer [-Parameterfile] <String> [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Descripción
El comando `New-SAPDeployer` permite configurar un nuevo implementador en el plano de control.

La máquina virtual del implementador tiene instalaciones de Ansible y Terraform. La máquina virtual del implementador se usa para implementar los artefactos de SAP.


## Ejemplos

### Ejemplo 1

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPDeployer -Parameterfile .\MGMT-WEEU-MGMT00-INFRASTRUCTURE.tfvars
```

## Parámetros

### `-Parameterfile`
Establece el archivo de parámetros para la máquina virtual del implementador. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#deployer).

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
El cmdlet `CommonParameters` admite los siguientes parámetros comunes: `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-InformationAction`, `-InformationVariable`, `-OutVariable`, `-OutBuffer`, `-PipelineVariable`, `-Verbose`, `-WarningAction` y `-WarningVariable`. Para obtener más información, consulta [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

## Notas
v0.9: Versión inicial

Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
