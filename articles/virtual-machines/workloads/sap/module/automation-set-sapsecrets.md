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
title: Set-SAPSecrets
description: Establece los secretos de SPN en Azure Key Vault
ms.openlocfilehash: 5bf2d85e57827227a3ab81a0658a64eee668a097
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725372"
---
# Set-SAPSecrets

## Sinopsis
Establece los secretos de la entidad de servicio en Azure Key Vault.

## Sintaxis

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Set-SAPSecrets [-Region] <String> [-Environment] <String> [-VaultName] <String> [-SPN_id] <String>
 [-SPN_password] <String> [-Tenant_id] <String> [-Workload] [<CommonParameters>]
```

## Descripción
Establece los secretos de Key Vault que requiere la automatización de la implementación.

## EXAMPLES

### EJEMPLO 1

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Set-SAPSecrets -Environment PROD -VaultName <vaultname> -SPN_id <appId> -SPN_password <clientsecret> -Tenant_id <Tenant_idID>
```

## Parámetros

### `-Region`
Establece el nombre de la región de Azure para la implementación.

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

### `-Environment`
Establece el nombre del entorno de implementación.

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

### `-VaultName`
Establece el nombre del almacén de claves.

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

### `-SPN_id`
Establece el identificador de aplicación de la entidad de servicio.

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

### `-SPN_password`
Establece la contraseña de la entidad de servicio.

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

### `-Tenant_id`
Establece el identificador del inquilino.

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

### `-Workload`
Establece la carga de trabajo.

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

### CommonParameters
Este cmdlet admite los parámetros comunes: `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-InformationAction`, `-InformationVariable`, `-OutVariable`, `-OutBuffer`, `-PipelineVariable`, `-Verbose`, `-WarningAction` y `-WarningVariable`. Para obtener más información, consulta [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

## Notas
v0.9: Versión inicial


Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.
## Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
