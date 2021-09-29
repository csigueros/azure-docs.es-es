---
title: Diagnóstico de errores de artefactos en una máquina virtual de Azure DevTest Labs
description: Los laboratorios de DevTest Labs proporcionan información que puede utilizar para diagnosticar un error de artefacto. Este artículo le mostrará cómo solucionar problemas de artefactos.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e7c343eee8c3484e12139c2bef963fc463ffc28d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567456"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnóstico de errores de artefactos en el laboratorio 
Después de crear un artefacto, también puede comprobar si se realizó correctamente o no. Los registros de artefactos de Azure DevTest Labs proporcionan información que puede utilizar para diagnosticar un error de artefacto. Tiene dos opciones para ver la información de registro del artefacto de una máquina virtual de Windows:

* En Azure Portal
* En la máquina virtual

> [!NOTE]
> Para asegurarse de que los errores se han identificado y explicado correctamente, es importante que el artefacto esté bien estructurado. Para más información sobre cómo crear correctamente un artefacto, consulte el artículo sobre cómo [crear artefactos personalizados](devtest-lab-artifact-author.md). Para ver un ejemplo de artefacto estructurado correctamente, consulte el artefacto [tipos de parámetros de prueba](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Solución de problemas de errores de artefacto mediante Azure Portal

1. En Azure Portal, en la lista de recursos, seleccione el laboratorio.
2. Elija la máquina virtual de Windows que incluya el artefacto que desea investigar.
3. En el panel izquierdo bajo **GENERAL**, seleccione **Artefactos**. Aparece una lista de artefactos asociados a esa máquina virtual. Se indican el nombre y el estado del artefacto.

   ![Estado del artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Seleccione un artefacto que muestre un estado **Con error**. Se abre el artefacto. Se muestra un mensaje de extensión que incluye detalles sobre el error del artefacto.

   ![Mensaje de error del artefacto](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Solución de problemas de errores de artefactos en la máquina virtual

1. Inicie sesión en la máquina virtual que contenga el artefacto que desee diagnosticar.
2. Vaya a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, donde *1.9* es el número de versión de Custom Script Extension.

   ![Archivo Status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Abra el archivo **status**.

Para obtener instrucciones sobre cómo buscar los archivos de registro en una máquina virtual **Linux**, consulte el artículo [Uso de la extensión de script personalizado de Azure versión 2 con máquinas virtuales Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting).


## <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Join a VM to existing AD Domain using a resource manager template in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Unión de una máquina virtual al dominio de AD existente mediante la plantilla de Resource Manager en DevTest Labs)

## <a name="next-steps"></a>Pasos siguientes
* Aprenda cómo [agregar un repositorio Git a un laboratorio](devtest-lab-add-artifact-repo.md).
