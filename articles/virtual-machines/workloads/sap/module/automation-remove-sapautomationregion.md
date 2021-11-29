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
title: Remove-SAPAutomationRegion
description: Quita el plano de control de SAP (implementador, biblioteca).
ms.openlocfilehash: 3de052a6ef1ce5e3bacf82cfa7439af49547735a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725374"
---
# Remove-SAPAutomationRegion

## Sinopsis

Quita el plano de control, incluida la máquina virtual del implementador y la biblioteca SAP.
## Sintaxis

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPAutomationRegion [-DeployerParameterfile] <String> [-LibraryParameterfile] <String>
 [<CommonParameters>]
```

## Descripción
Quita el plano de control de SAP, incluida la máquina virtual del implementador y la biblioteca SAP.

## Ejemplos

### Ejemplo 1
```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-SAP01-INFRASTRUCTURE\MGMT-WEEU-SAP01-INFRASTRUCTURE.tfvars 
 -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars

```

## Parámetros

### `-DeployerParameterfile`
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

### `-LibraryParameterfile`
Establece el archivo de parámetros para la biblioteca SAP. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md#sap-library).

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

### CommonParameters
Este cmdlet admite los parámetros comunes: `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-InformationAction`, `-InformationVariable`, `-OutVariable`, `-OutBuffer`, `-PipelineVariable`, `-Verbose`, `-WarningAction` y `-WarningVariable`. Para obtener más información, consulta [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

## Notas
v0.9: Versión inicial


Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
