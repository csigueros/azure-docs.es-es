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
title: New-SAPWorkloadZone
description: Implementación de una nueva zona de cargas de trabajo de SAP
ms.openlocfilehash: 7bceaa49a4823eaa61f2dcb76e7ffbb426cd8243
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725375"
---
# New-SAPWorkloadZone

## Sinopsis
El comando `New-SAPWorkloadZone` se puede usar para implementar una nueva zona de cargas de trabajo de SAP.

## Sintaxis

```powershell
Import-Module "SAPDeploymentUtilities.psd1"


New-SAPWorkloadZone [-Parameterfile] <String> [[-Deployerstatefile] <String>] [[-Deployerenvironment] <String>]
 [[-State_subscription] <String>] [[-Vault] <String>] [[-StorageAccountName] <String>]
 [[-Subscription] <String>] [[-SPN_id] <String>] [[-SPN_password] <String>] [[-Tenant_id] <String>] [-Force]
 [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Descripción
El comando `New-SAPWorkloadZone` implementa una nueva zona de cargas de trabajo de SAP. La zona de cargas de trabajo contiene los recursos compartidos de todas las máquinas virtuales internas.

## Ejemplos

### Ejemplo 1

En este ejemplo se implementa la zona de cargas de trabajo, tal como se define en los archivos de parámetros. El proceso le pide los detalles del SPN.

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

New-SAPWorkloadZone -Parameterfile .\PROD-WEEU-SAP00-infrastructure.tfvars
```

### Ejemplo 2

En este ejemplo se implementa la zona de cargas de trabajo, tal como se define en los archivos de parámetros. El proceso agrega las credenciales de implementación al almacén de claves de la implementación.


```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPWorkloadZone -Parameterfile .\PROD-WEEU-SAP00-infrastructure.tfvars
 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
 -SPN_id yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
 -SPN_password ************************
 -Tenant_id zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz
```

## Parámetros

### `-Parameterfile`
Establece el archivo de parámetros para la zona de cargas de trabajo. Para más información, consulte [Configuración de la zona de cargas de trabajo](../automation-configure-workload-zone.md).

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

### `-Deployerstatefile`
Establece el nombre de archivo de estado de Terraform de la máquina virtual del implementador.

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

### `-Deployerenvironment`
Nombre del entorno del implementador

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

### `-State_subscription`
Establece el identificador de suscripción de la cuenta de almacenamiento de Terraform.

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

### `-Vault`
Establece el almacén de claves de las credenciales de implementación.

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

### `-StorageAccountName`
Nombre de la cuenta de almacenamiento

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

### `-Subscription`
Establece la suscripción de destino.

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

### `-SPN_id`
Establece el identificador de aplicación de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials).
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

### `-SPN_password`
Establece la contraseña de la entidad de servicio. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 9
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Tenant_id`
Establece el identificador del inquilino. Para más información, consulte [Preparación de las credenciales de implementación](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 10
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
Implementa sin preguntar.

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
