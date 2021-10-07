---
title: Uso de identidades administradas de Azure para crear entornos
description: Obtenga más información sobre el uso de identidades administradas en Azure para implementar entornos en un laboratorio de Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 29ebda2920dc6fce5596d9b8b535ef014fd2240e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595973"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Uso de identidades administradas de Azure para implementar entornos en un laboratorio 

Como propietario de un laboratorio, puede usar una identidad administrada para implementar entornos en un laboratorio. Esta característica es útil en escenarios en los que el entorno contiene o incluye referencias a recursos de Azure, como almacenes de claves, galerías de imágenes compartidas y redes que son externas al grupo de recursos del entorno. Permite la creación de entornos de espacio aislado que no se limitan al grupo de recursos de dicho entorno. 

De forma predeterminada, al crear un entorno, el laboratorio crea una identidad asignada por el sistema para acceder a los recursos y servicios de Azure en nombre de un usuario del laboratorio al implementar la plantilla de Azure Resource Manager (plantilla de ARM). Obtenga más información sobre [por qué un laboratorio crea una identidad asignada por el sistema](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity). Para los laboratorios nuevos y existentes, se crea una identidad asignada por el sistema de forma predeterminada la primera vez que se crea un entorno de laboratorio.  

Tenga en cuenta que, como propietario del laboratorio, puede optar por conceder los permisos de la identidad asignada por el sistema del laboratorio para acceder a recursos de Azure fuera del laboratorio o puede usar su propia identidad asignada por el usuario para el escenario. La identidad asignada por el sistema del laboratorio solo es válida durante la vida útil del laboratorio. La identidad asignada por el sistema se elimina al eliminar el laboratorio. Si tiene entornos en varios laboratorios que necesitan usar una identidad, considere la posibilidad de usar una identidad asignada por el usuario.  

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
1. Seleccione en la lista desplegable un usuario existente que haya creado o al que tenga acceso.
 
    ![Adición de una identidad administrada por el usuario](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Presione **Guardar** en la parte superior de la página.

    Una vez guardado, el laboratorio usará esta identidad al implementar todos los entornos de laboratorio. También puede acceder al recurso de la identidad en Azure mediante la selección de la identidad en la lista. 

El propietario del laboratorio no necesita hacer nada especial al implementar un entorno siempre que la identidad agregada al laboratorio tenga permisos para los recursos externos a los que el laboratorio necesita tener acceso. 

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
 
Una vez que la identidad asignada por el usuario se agrega al laboratorio, el servicio de Azure DevTest Labs la usará al implementar entornos de Azure Resource Manager. Por ejemplo, si necesita la plantilla de Resource Manager para tener acceso a una imagen externa de la galería de imágenes compartidas, asegúrese de que la identidad que ha agregado al laboratorio tiene los permisos mínimos necesarios para el recurso de la galería de imágenes compartidas. 
