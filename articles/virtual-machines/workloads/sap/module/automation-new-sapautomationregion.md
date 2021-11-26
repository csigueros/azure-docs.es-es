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
title: New-SAPAutomationRegion
description: Implementa el plano de control (implementador, biblioteca de SAP)
ms.openlocfilehash: 9f39ae06acb0aa90e780faf6f14d2dea2ff6cd72
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725391"
---
# New-SAPAutomationRegion

## Sinopsis
El comando de PowerShell `New-SAPAutomationRegion` implementa el plano de control, incluida la máquina virtual del implementador, Azure Key Vault y la biblioteca SAP.

La máquina virtual del implementador tiene instalaciones de Ansible y Terraform. Esta máquina virtual implementa los artefactos de SAP.

## Sintaxis

```
New-SAPAutomationRegion [-DeployerParameterfile] <String> [-LibraryParameterfile] <String>
 [[-Subscription] <String>] [[-SPN_id] <String>] [[-SPN_password] <String>] [[-Tenant_id] <String>]
 [[-Vault] <String>] [[-StorageAccountName] <String>] [-Force] [-Silent] [<CommonParameters>]
```

## Descripción
Implementa el plano de control, que incluye la máquina virtual del implementador y la biblioteca SAP. Para más información, consulte [Configuración del plano de control](../automation-configure-control-plane.md) e [Implementación del plano de control](../automation-deploy-control-plane.md).

## Ejemplos

### Ejemplo 1

En este ejemplo se implementa el plano de control, tal como se define en los archivos de parámetros. El proceso le pide los detalles del SPN.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
 -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
```

### Ejemplo 2

En este ejemplo se implementa el plano de control, tal como se define en los archivos de parámetros. El proceso agrega las credenciales de implementación al almacén de claves de la implementación.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

$Subscription=<subscriptionID>
$SPN_id=<appID>
$SPN_password=<password>
$Tenant_id=<tenant>

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
-LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
-Subscription $Subscription
-SPN_id $SPN_id
-SPN_password $SPN_password
-Tenant_id $Tenant_id
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

### `-Subscription`
Establece la suscripción de Azure de destino.

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

### `-SPN_id`
Establece el identificador de aplicación de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-control-plane.md#prepare-the-deployment-credentials).

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

### `-SPN_password`
Establece la contraseña de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-control-plane.md#prepare-the-deployment-credentials). 

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

### `-Tenant_id`
Establece el identificador del inquilino de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-control-plane.md#prepare-the-deployment-credentials). 

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

### `-Vault`
Establece el nombre del almacén de claves de la implementación.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 7
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-StorageAccountName`
Establece el nombre de la cuenta de almacenamiento que contiene los archivos de estados de Terraform.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 8
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Force`
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

### `-Silent`
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

### CommonParameters
Este cmdlet admite los parámetros comunes: `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-InformationAction`, `-InformationVariable`, `-OutVariable`, `-OutBuffer`, `-PipelineVariable`, `-Verbose`, `-WarningAction` y `-WarningVariable`. Para obtener más información, consulta [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).
## Notas
v0.9: Versión inicial

Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.

## Vínculos relacionados

+[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
