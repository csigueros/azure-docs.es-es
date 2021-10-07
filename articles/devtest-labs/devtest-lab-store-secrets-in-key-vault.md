---
title: Almacenamiento de secretos en un almacén de claves
description: Aprenda a almacenar secretos en Azure Key Vault y a usarlos al crear una máquina virtual, una fórmula o un entorno.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2aaa177c895b57a07ed94de48081de69beedcda8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596030"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Almacenamiento de secretos en un almacén de claves en Azure DevTest Labs
Puede que al usar Azure DevTest Labs deba escribir un secreto complejo: una contraseña para la máquina virtual Windows, una clave SSH pública para la máquina virtual Linux o un token de acceso personal para clonar el repositorio de Git mediante un artefacto. Los secretos son normalmente largos y tienen caracteres aleatorios. Por lo tanto, escribirlos puede ser complicado y supone un inconveniente, especialmente si se usa el mismo secreto varias veces.

Para resolver este problema y mantener también los secretos en un lugar seguro, DevTest Labs admite el almacenamiento de secretos en un [almacén de claves de Azure](../key-vault/general/overview.md). Cuando un usuario guarda un secreto por primera vez, el servicio DevTest Labs crea automáticamente un almacén de claves en el mismo grupo de recursos que contiene el laboratorio y almacena el secreto en el almacén de claves. DevTest Labs crea un almacén de claves independiente para cada usuario. 

Tenga en cuenta que el usuario de laboratorio tendrá que crear, en primer lugar, una máquina virtual de laboratorio antes de poder crear un secreto en el almacén de claves. Esto se debe a que el servicio DevTest Lab tiene que asociar el usuario de laboratorio a un documento de usuario válido antes de que se le permita crear y almacenar secretos en su almacén de claves. 


## <a name="save-a-secret-in-azure-key-vault"></a>Guardar un secreto en Azure Key Vault
Para guardar el secreto en Azure Key Vault, siga los pasos siguientes:

1. Seleccione **My secrets** (Mis secretos) en el menú izquierdo.
2. Escriba un **nombre** para el secreto. Este nombre lo verá en la lista desplegable al crear una máquina virtual, una fórmula o un entorno. 
3. Escriba el secreto como **valor**.

    ![Almacenar el secreto](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Usar un secreto de Azure Key Vault
Cuando necesite especificar un secreto para crear una máquina virtual, una fórmula o un entorno, puede escribir manualmente un secreto o seleccionar un secreto guardado en el almacén de claves. Para usar un secreto almacenado en el almacén de claves, realice las siguientes acciones:

1. Seleccione **Use a saved secret** (Usar un secreto guardado). 
2. Seleccione el secreto de la lista desplegable en **Pick a secret** (Seleccionar un secreto). 

    ![Usar el secreto en una máquina virtual](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Usar el secreto en una plantilla de Azure Resource Manager
Puede especificar el nombre del secreto en una plantilla de Azure Resource Manager que se usa para crear una máquina virtual, tal como se muestra en el ejemplo siguiente:

![Usar el secreto en una fórmula o un entorno](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una máquina virtual mediante el secreto](devtest-lab-add-vm.md) 
- [Creación de una fórmula mediante el secreto](devtest-lab-manage-formulas.md)
- [Creación de un entorno mediante el secreto](devtest-lab-create-environment-from-arm.md)
