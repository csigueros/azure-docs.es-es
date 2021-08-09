---
title: Habilite las identidades administradas en las máquinas virtuales de laboratorio en Azure DevTest Labs
description: En este artículo se muestra cómo un propietario del laboratorio puede habilitar identidades administradas asignadas por el usuario en las máquinas virtuales de laboratorio.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0d2c7b944d37160df241e6ca4407c730593f1b62
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854043"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Habilitar identidades administradas asignadas por el usuario en máquinas virtuales de laboratorio en Azure DevTest Labs
Como propietario de un laboratorio, puede habilitar las identidades administradas asignadas por el usuario en las máquinas virtuales (VM) del laboratorio en Azure DevTest Labs.

Una identidad administrada se puede usar para autenticarse en cualquier servicio que admita la autenticación Azure Active Directory (AD), incluido Key Vault, sin pasar ninguna credencial en el código. Para más información sobre las identidades administradas, consulte [¿Qué son las identidades administradas para los recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Con esta característica, los usuarios de laboratorio pueden compartir recursos de Azure, como Azure SQL Database en el contexto del laboratorio. La autenticación en el recurso se encarga de la propia identidad. Una vez configurada, todas las máquinas virtuales de laboratorio existentes o recién creadas se habilitarán con esta identidad. Los usuarios de laboratorio pueden acceder a los recursos una vez que han iniciado sesión en sus equipos.

> [!NOTE]
> Puede agregar varias identidades administradas asignadas por el usuario para habilitarlas en las máquinas virtuales de laboratorio.

## <a name="use-azure-portal"></a>Usar Azure Portal
Para agregar una identidad administrada asignada por el usuario para las máquinas virtuales de laboratorio, siga estos pasos:

1. [Cree de una identidad administrada asignada por el usuario en la suscripción](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Vaya a la página **Configuración y directivas** del laboratorio.
1. Seleccione **Identidad (vista previa)** en el menú de la izquierda.
1. Seleccione la pestaña **Máquina virtual**.
1. Seleccione **Agregar** para seleccionar una identidad existente en una lista desplegable llenada previamente. 

    > [!div class="mx-imgBorder"]
    > ![Botón Agregar identidad](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Seleccione una **identidad administrada por el usuario** existente en la lista desplegable y seleccione **Aceptar**. 

    > [!div class="mx-imgBorder"]
    > ![Agregar identidad](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Uso de la API

1.  Después de crear una identidad, tenga en cuenta el identificador de recurso de la identidad. Debería ser similar al ejemplo siguiente: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.
    
2. Ejecute un método HTTPS PUT en el recurso de laboratorio para agregar una o varias identidades asignadas por el usuario al campo **managementIdentities**.


    ```json
    {
        "location": "southeastasia",
        "properties": {
        ...
            "managementIdentities": {
               "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}": {}
        },
        ...
        },
    ...
    }
    ```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las identidades administradas, consulte [¿Qué son las identidades administradas para los recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md).







