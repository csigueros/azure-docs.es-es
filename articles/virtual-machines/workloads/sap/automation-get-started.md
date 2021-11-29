---
title: Introducción al marco de automatización de implementaciones de SAP en Azure
description: Comience rápidamente con el marco de automatización de implementaciones de SAP en Azure. Implemente una configuración de ejemplo mediante archivos de parámetros de ejemplo.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 29c7b229020306b085000ed0596814f1cb8e818e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725347"
---
# <a name="get-started-with-sap-automation-framework-on-azure"></a>Introducción al marco de automatización de SAP en Azure

Comience rápidamente con el [marco de automatización de implementaciones de SAP en Azure](automation-deployment-framework.md).

## <a name="prerequisites"></a>Requisitos previos


- Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una [descarga del software de SAP](automation-software.md) en el entorno de Azure.
- Una instalación de [Terraform](https://www.terraform.io/). Para obtener más información, consulte también la [documentación de Terraform en Azure](/azure/developer/terraform/).
- Una instalación de la [interfaz de la línea de comandos de Azure (CLI de Azure)](/cli/azure/install-azure-cli) en el equipo local.
- Opcionalmente, si desea usar PowerShell:
    - Una instalación de [Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module) en el equipo local.
    - Los módulos de PowerShell más recientes. [Actualización del módulo de PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module), si es necesario.

Es posible que algunos de los requisitos previos ya estén instalados en el entorno de implementación. Tanto Cloud Shell como el implementador tienen instalado Terraform y la CLI de Azure.
## <a name="clone-the-repository"></a>Clonación del repositorio

Clone el repositorio y prepare el entorno de ejecución mediante los pasos siguientes:

1. Cree un directorio llamado `Azure_SAP_Automated_Deployment` para la implementación del marco de automatización. 

# <a name="linux"></a>[Linux](#tab/linux)

```bash
mkdir ~/Azure_SAP_Automated_Deployment; cd $_
git clone https://github.com/Azure/sap-hana.git 
```

Prepare el entorno mediante los pasos siguientes:

```bash
export DEPLOYMENT_REPO_PATH=~/Azure_SAP_Automated_Deployment/sap-hana
export ARM_SUBSCRIPTION_ID=<subscriptionID>
```
> [!NOTE]
> Asegúrese de reemplazar el valor de ejemplo `<subscriptionID>` por su información.

Puede copiar los archivos de configuración de ejemplo para empezar a probar el marco de automatización de la implementación.

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-hana/deploy/samples/WORKSPACES WORKSPACES

```


# <a name="windows"></a>[Windows](#tab/windows)

```powershell
mkdir C:\Azure_SAP_Automated_Deployment
    
cd Azure_SAP_Automated_Deployment
    
git clone https://github.com/Azure/sap-hana.git 
```

Importación del módulo de PowerShell

```powershell
Import-Module             C:\Azure_SAP_Automated_Deployment\sap-hana\deploy\scripts\pwsh\SAPDeploymentUtilities\Output\SAPDeploymentUtilities\SAPDeploymentUtilitiespsd1
```

---

> [!TIP]
> El implementador ya clona el [repositorio del marco de automatización de implementaciones de SAP](https://github.com/Azure/sap-hana). 

## <a name="copy-the-samples"></a>Copia de las muestras

Puede copiar los archivos de configuración de ejemplo para empezar a probar el marco de automatización de la implementación.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-hana/deploy/samples/WORKSPACES WORKSPACES
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell
cd C:\Azure_SAP_Automated_Deployment
mkdir WORKSPACES

xcopy sap-hana\deploy\samples\WORKSPACES WORKSPACES
```

---


## <a name="next-step"></a>Paso siguiente

> [!div class="nextstepaction"]
> [Planear la implementación](automation-plan-deployment.md)
