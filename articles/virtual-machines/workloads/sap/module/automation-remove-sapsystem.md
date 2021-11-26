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
title: Remove-SAPSystem
description: Elimina una implementación de SAP.
ms.openlocfilehash: dd0f86e35c0530103b7df882d0bb89008ccf3ae0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725373"
---
# Remove-SAPSystem

## Sinopsis
Elimina una implementación de SAP.

## Sintaxis

Elimina una implementación de SAP definida por el archivo de parámetros.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPSystem [-Parameterfile] <String> [-Type] <String> [<CommonParameters>]
```

## Descripción
Elimina una implementación de SAP definida por el archivo de parámetros.

## Ejemplos

### Ejemplo 1

Elimina un sistema SAP.

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Remove-System -Parameterfile .\PROD-WEEU-SAP00-X00.tfvars -Type sap_system
```

### Ejemplo 2

Elimina una biblioteca SAP.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-System -Parameterfile .\PROD-WEEU-SAP_LIBRARY.tfvars -Type sap_library
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
Establece el tipo de sistema SAP.

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
