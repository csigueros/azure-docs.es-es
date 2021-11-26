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
title: Update-TFState
description: Actualiza el archivo de estados de Terraform.
ms.openlocfilehash: f20c04a79364230f8918a08b97e58e4a881b2a0f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725371"
---
# Update-TFState

## Sinopsis
Actualiza el archivo de estados de Terraform.

## Sintaxis

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Update-TFState [-Parameterfile] <String> [-Type] <SAP_Types> [-TerraformStateFileName] <String>
 [-Subscription] <String> [-StorageAccountName] <String> [-TerraformResourceName] <String>
 [-AzureResourceID] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Descripción
Puede usar este cmdlet para agregar recursos que faltan o modificados al archivo de estados de Terraform.

## Ejemplos

### Ejemplo 1

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Update-TFState -Parameterfile .\DEV-WEEU-SAP01-X00.tfvars -Type sap_system
 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
 -StorageAccountName devweeutfstate### 
 -TerraformStateFileName DEV-WEEU-SAP01-X00.terraform.tfstate 
 -TerraformResourceName module.sap_system.azurerm_resource_group.deployer[0] 
 -AzureResourceID /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/DEV-WEEU-SAP01-X00
```

## Parámetros

### `-Parameterfile`
Establece el archivo de parámetros del sistema.

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
Establece el tipo de sistema. Los valores válidos son `sap_deployer`, `sap_library`, `sap_landscape` y `sap_system`.

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

### `-TerraformStateFileName`
Establece el nombre del archivo de estados de Terraform.

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

### `-Subscription`
Establece la suscripción de Azure que contiene el estado de Terraform.

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

### `-StorageAccountName`
Establece el nombre de la cuenta de almacenamiento del archivo de estados de Terraform.

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

### `-TerraformResourceName`
Establece el nombre del recurso en el archivo de estados de Terraform.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-AzureResourceID`
Establece el identificador de recurso del recurso de Azure que se importará.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 7
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-WhatIf`
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

### `-Confirm`
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
