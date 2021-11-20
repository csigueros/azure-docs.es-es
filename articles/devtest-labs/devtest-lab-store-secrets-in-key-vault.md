---
title: Almacenamiento de secretos en un almacén de claves
description: Aprenda a almacenar secretos en Azure Key Vault y a usarlos al crear una máquina virtual, una fórmula o un entorno.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2c1849b6bf2ab5876fcbe0960df9bb8a7004bb78
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398105"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Almacenamiento de secretos en un almacén de claves en Azure DevTest Labs
Es posible que tenga que escribir un secreto complejo cuando use Azure DevTest Labs. Los secretos incluyen contraseñas para máquinas virtuales de Windows, claves SSH públicas para las máquinas virtuales Linux o token de acceso personal para clonar el repositorio de Git a través de un artefacto. Los secretos son a menudo largos y tienen caracteres aleatorios. Escribirlos puede ser complicado y supone un inconveniente, especialmente si se usa el mismo secreto varias veces.

Para resolver este problema y mantener los secretos en un lugar seguro, DevTest Labs admite el almacenamiento de secretos en un [almacén de claves de Azure](../key-vault/general/overview.md). La primera vez que un usuario guarda un secreto, DevTest Labs crea automáticamente un almacén de claves en el mismo grupo de recursos que el laboratorio y almacena el secreto. DevTest Labs crea un almacén de claves independiente para cada usuario. 

Los usuarios del laboratorio deben crear una máquina virtual de laboratorio para poder crear un secreto en el almacén de claves. Este requisito se debe a que DevTest Labs debe asociar usuarios de laboratorio a documentos de usuario válidos. DevTest Labs permite a los usuarios crear y almacenar secretos en sus almacenes de claves.


## <a name="save-a-secret-in-azure-key-vault"></a>Guardar un secreto en Azure Key Vault
Para guardar el secreto en Azure Key Vault, siga los pasos siguientes:

1. Seleccione **My secrets** (Mis secretos) en el menú izquierdo.
2. Escriba un **nombre** para el secreto. Este nombre lo verá en la lista desplegable al crear una máquina virtual, una fórmula o un entorno. 
3. Escriba el secreto como **valor**.

    ![Captura de pantalla en la que se muestra el almacenamiento de un secreto.](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Usar un secreto de Azure Key Vault
Cuando especifique un secreto para crear una máquina virtual, una fórmula o entorno, puede escribir manualmente el secreto o seleccionar un secreto guardado en el almacén de claves. Para usar un secreto almacenado en el almacén de claves, realice las siguientes acciones:

1. Seleccione **Use a saved secret** (Usar un secreto guardado). 
2. Seleccione el secreto de la lista desplegable en **Pick a secret** (Seleccionar un secreto). 

    ![Captura de pantalla en la que se muestra el uso de un secreto en la creación de máquinas virtuales.](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Usar el secreto en una plantilla de Azure Resource Manager
Puede especificar el nombre del secreto en una plantilla de Azure Resource Manager que se usa para crear una máquina virtual, tal como se muestra en el ejemplo siguiente:

![Captura de pantalla en la que se muestra el uso de un secreto en una fórmula o entorno.](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una máquina virtual mediante el secreto](devtest-lab-add-vm.md) 
- [Creación de una fórmula mediante el secreto](devtest-lab-manage-formulas.md)
- [Creación de un entorno mediante el secreto](devtest-lab-create-environment-from-arm.md)
