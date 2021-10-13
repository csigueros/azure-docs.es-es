---
title: 'Detección de recursos de Azure para su administración en PIM: Azure AD | Microsoft Docs'
description: Aprenda a detectar recursos de Azure que se administran en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07923556630157dcde3f23c24e1321ec4a02fa5c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667998"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Detección de recursos de Azure para su administración en Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), puede mejorar la protección de los recursos de Azure. Esto es útil para:

- Organizaciones que ya usan Privileged Identity Management para proteger roles de Azure AD.
- Grupos de administración y propietarios de suscripciones que están intentando proteger los recursos de producción.

La primera vez que se configura Privileged Identity Management para recursos de Azure, es preciso detectar y seleccionar los recursos que se van a proteger con Privileged Identity Management. No hay ningún límite en cuanto al número de recursos que se pueden administrar con Privileged Identity Management. Sin embargo, se recomienda empezar por los recursos de producción más críticos.

## <a name="discover-resources"></a>Detección de recursos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Azure resources** (Recursos de Azure).

    Si esta es la primera vez que usa Privileged Identity Management para recursos de Azure, verá la página **Detectar recursos**.

    ![Panel de detección de recursos sin recursos mostrados para la experiencia inicial](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Si otro administrador de la organización ya administra recursos de Azure en Privileged Identity Management, verá una lista de los recursos que se están administrando actualmente.

    ![Panel de detección de recursos con los recursos que se administran actualmente](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Seleccione **Detectar recursos** para iniciar la experiencia de detección.

    ![En el panel de detección se enumeran los recursos que se pueden administrar, como las suscripciones y los grupos de administración.](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. En la página **Detección**, utilice **Filtro de estado de recursos** y **Seleccionar tipo de recurso** para filtrar los grupos de administración o las suscripciones en las que tiene permiso de escritura. Empezar con **Todo** es, probablemente, más fácil.

   Puede buscar y seleccionar recursos de grupo de administración o suscripción para administrarlos en Privileged Identity Management. Al administrar un grupo de administración o una suscripción en Privileged Identity Management, también se pueden administrar sus recursos secundarios.

   > [!Note]
   > Al agregar un nuevo recurso secundario de Azure a un grupo de administración administrado por PIM, puede hacer que se administre el recurso secundario buscándolo en PIM.

1. Seleccione los recursos no administrados que quiera administrar.

1. Seleccione **Administrar recurso** para empezar a administrar los recursos seleccionados.

    > [!NOTE]
    > Una vez definido un grupo de administración o suscripción como administrado, no puede ser no administrado. Esto impide que otro administrador de recursos pueda eliminar la configuración de Privileged Identity Management.

    ![Panel de detección con un recurso seleccionado y la opción Administrar recurso resaltada](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Si ve un mensaje para confirmar la incorporación del recurso seleccionado para su administración, seleccione **Sí**. A continuación, se configurará PIM para administrar todos los objetos secundarios nuevos y existentes en los recursos.

    ![Mensaje que confirma la incorporación de los recursos seleccionados para la administración](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de las opciones de rol de recursos de Azure en Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Asignación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-assign-roles.md)
