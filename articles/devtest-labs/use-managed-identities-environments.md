---
title: Uso de identidades administradas de Azure para crear entornos
description: Obtenga más información sobre el uso de identidades administradas en Azure para implementar entornos en un laboratorio de Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 220937d20c63420501e3ef6bb1b6c5f8a820613d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400326"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Uso de identidades administradas de Azure para implementar entornos en un laboratorio 

Como propietario de un laboratorio, puede usar una identidad administrada para implementar entornos en un laboratorio. Esta característica es de ayuda en escenarios donde el entorno contiene o tiene referencias a recursos de Azure que están fuera de su grupo de recursos. Estos recursos incluyen almacenes de claves, galerías de imágenes compartidas y redes. Las identidades administradas posibilitan la creación de entornos de espacio aislado que no se limitan al grupo de recursos de dicho entorno. 

De manera predeterminada, cuando se crea un entorno, el laboratorio crea una identidad asignada por el sistema al implementar la plantilla de Azure Resource Manager (plantilla de ARM). La identidad asignada por el sistema accede a los recursos y servicios de Azure en nombre de un usuario del laboratorio. De manera predeterminada, DevTest Labs crea una identidad asignada por el sistema la primera vez que crea el entorno de laboratorio. Obtenga más información sobre [por qué un laboratorio crea una identidad asignada por el sistema](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity). 

Como propietario del laboratorio, usted puede optar por conceder permisos de identidad asignada por el sistema del laboratorio para acceder a recursos de Azure fuera de este. También puede usar la identidad asignada por el usuario para el escenario. La identidad asignada por el sistema del laboratorio solo es válida durante la vida útil del laboratorio. La identidad asignada por el sistema se elimina al eliminar el laboratorio. Si tiene entornos en varios laboratorios que necesitan usar una identidad, considere la posibilidad de usar una identidad asignada por el usuario.  

> [!NOTE]
> Actualmente, se admite una única identidad asignada por el usuario por laboratorio. 

## <a name="prerequisites"></a>Prerequisites

- [Creación, enumeración, eliminación o asignación de un rol a una identidad administrada asignada por el usuario mediante Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Asegúrese de que la identidad administrada se haya creado en la misma región y suscripción que el laboratorio. No es necesario que la identidad administrada esté en el mismo grupo de recursos.

## <a name="use-azure-portal"></a>Usar Azure Portal

En esta sección, como propietario del laboratorio, use Azure Portal para agregar una identidad administrada por el usuario al laboratorio. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque **DevTest Labs**.
1. En la lista de laboratorios, seleccione el laboratorio que quiera.
1. Seleccione **Configuración y directivas** -> **Identidad (versión preliminar)** . 
1. Para agregar una identidad asignada por el usuario, seleccione la pestaña **User Assigned** (Asignada por el usuario).
1. Presione **Agregar**.
1. Seleccione en la lista desplegable un usuario ya existente que haya creado o al que tenga acceso.
 
    ![Adición de una identidad administrada por el usuario](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Presione **Guardar** en la parte superior de la página.

    Una vez guardado, el laboratorio usará esta identidad al implementar todos los entornos de laboratorio. También puede acceder al recurso de la identidad en Azure mediante la selección de la identidad en la lista. 

No es necesario que el propietario del laboratorio haga nada especial para implementar un entorno. La identidad agregada al laboratorio debe tener permisos para los recursos externos a los que el entorno necesita acceder. 

Para cambiar la identidad administrada por el usuario asignada al laboratorio, primero debe quitar la identidad que se ha adjuntado al laboratorio y, a continuación, agregar otra al laboratorio. Para quitar una identidad adjuntada al laboratorio, seleccione **... (puntos suspensivos)** y haga clic en **Quitar**. 

## <a name="use-api"></a>Uso de la API

1. Tras crear una identidad, anote su id. de recurso. Debería ser similar al ejemplo siguiente: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. Ejecute un método HTTP PUT en el recurso de laboratorio para agregar una identidad asignada por el usuario o habilitar una identidad asignada por el sistema para el laboratorio.

   > [!NOTE]
   > Independientemente de si crea una identidad asignada por el usuario, el laboratorio crea automáticamente una identidad asignada por el sistema la primera vez que se crea un entorno de laboratorio. Sin embargo, si una identidad asignada por el usuario ya está configurada para el laboratorio, el servicio DevTest Lab sigue utilizando esa identidad para implementar entornos de laboratorio. 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
Una vez que se agrega la identidad asignada por el usuario al laboratorio, el servicio DevTest Labs la usa al implementar entornos de Azure Resource Manager. Por ejemplo, si necesita la plantilla de Resource Manager para acceder a una imagen de la galería de imágenes compartidas, asegúrese de que la identidad tenga los permisos necesarios para el recurso de la galería. 
