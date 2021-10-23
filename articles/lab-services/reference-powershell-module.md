---
title: Módulo de PowerShell para Azure Lab Services | Microsoft Docs
description: En este artículo se proporciona información sobre un módulo de PowerShell que ayuda a administrar artefactos en Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 81ed7d689105aa459151d698466b98f71f7b352e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177695"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo Az.LabServices de PowerShell (versión preliminar)
Az.LabServices es un módulo de PowerShell que simplifica la administración de Azure Lab Services. Proporciona funciones que admiten composición para crear, consultar, actualizar y eliminar cuentas de laboratorio, laboratorios, máquinas virtuales e imágenes. Para obtener más información sobre este módulo, vea la [Página principal de Az.LabServices en GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo se encuentra en **versión preliminar**. 

## <a name="example-command"></a>Comando de ejemplo
Este es un ejemplo del uso de un comando de PowerShell que sirve para detener todas las máquinas virtuales en ejecución en todos los laboratorios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introducción
1. Instale [Azure PowerShell](/powershell/azure/) si no existe en la máquina. 
2. Descargue [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) en la máquina.
3. Importe el módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Ejecute el comando siguiente para enumerar los laboratorios de la suscripción.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Pasos siguientes
Vea la [Página principal de Az.LabServices en GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).