---
title: 'Tutorial: Incorporación de usuarios a un grupo dinámico en Azure AD | Microsoft Docs'
description: En este tutorial, se usan grupos con reglas de pertenencia del usuario para agregar o quitar usuarios automáticamente
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3ef7aeea68002cdc3ff19efcc989701fb93714f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427361"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: Adición o eliminación automáticas de miembros de grupos

En Azure Active Directory (Azure AD), puede agregar o quitar usuarios de grupos de seguridad o grupos de Microsoft 365 automáticamente, por lo que no siempre tiene que hacerlo de forma manual. Cada vez que cambian las propiedades de un usuario o un dispositivo, Azure AD evalúa todas las reglas de grupos dinámicos de la organización de Azure AD, para ver si el cambio hace que se deban agregar o quitar miembros.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear un grupo de usuarios invitados de una empresa asociada, que se rellena automáticamente.
> * Asignar licencias al grupo para que los usuarios invitados puedan acceder a las características específicas del asociado.
> * Extra: proteger el grupo **Todos los usuarios** mediante la eliminación de los usuarios invitados para que, por ejemplo, pueda otorgar acceso a sitios internos a los usuarios miembros.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

Esta característica requiere una licencia de Azure AD Premium como administrador global de la organización. Si no dispone de una, en Azure AD, seleccione **Licencias** > **Productos** > **Probar/Comprar**.

No es necesario asignar licencias a los usuarios para que sean miembros de grupos dinámicos. Solo necesita el número mínimo de licencias Premium P1 de Azure AD disponibles en la organización para dar cobertura a dichos usuarios. 

## <a name="create-a-group-of-guest-users"></a>Creación de un grupo de usuarios invitados

En primer lugar, creará un grupo para los usuarios invitados que proceden de una única empresa asociada. Necesitan una licencia especial, por lo que a menudo resulta más eficaz crear un grupo para este propósito.

1. Inicie sesión en Azure Portal (https://portal.azure.com) con una cuenta que sea administrador global del inquilino.
2. Seleccione **Azure Active Directory** > **Grupos** > **Nuevo grupo**.
   ![Selección del comando para iniciar un nuevo grupo](./media/groups-dynamic-tutorial/new-group.png)
3. En la hoja **Grupo**:
  
   * Seleccione **Seguridad** como tipo de grupo.
   * Escriba `Guest users Contoso` como nombre y descripción del grupo.
   * Cambie el valor de **Tipo de pertenencia** a **Usuario dinámico**.
   
4. Seleccione **Propietarios** y, en la hoja **Agregar propietarios**, busque los propietarios que desee. Haga clic en los propietarios que desee para agregarlos a la selección.
5. Haga clic en **Seleccionar** para cerrar la hoja **Agregar propietarios**.  
6. Seleccione **Editar consulta dinámica** en el cuadro **Usuarios miembros dinámicos**.
7. En la hoja **Reglas de pertenencia dinámica**:

   * En el campo **Propiedad**, haga clic en el valor existente y seleccione **userType**. 
   * Compruebe que el campo **Operador** tenga seleccionada la opción **Es igual a**.  
   * Seleccione el campo **Valor** y escriba **Invitado**. 
   * Haga clic en el hipervínculo **Agregar expresión**  para agregar otra línea.
   * En el campo **Y/o** , seleccione **Y**.
   * En el campo **Propiedad**, seleccione **companyName**.
   * Compruebe que el campo **Operador** tenga seleccionada la opción **Es igual a**.
   * En el campo **Valor**, escriba **Contoso**.
   * Haga clic en **Guardar** para cerrar la hoja **Reglas de pertenencia dinámica**.
   
8. En la hoja **Grupo**, seleccione **Crear** para crear el grupo.

## <a name="assign-licenses"></a>Asignación de licencias

Ahora que tiene el nuevo grupo, puede aplicar las licencias que necesitan estos usuarios asociados.

1. En Azure AD, seleccione **Licencias**, seleccione una o varias licencias y, a continuación, seleccione **Asignar**.
2. Seleccione **Usuarios y grupos**, seleccione el grupo **Guest users Contoso** y guarde los cambios.
3. En **Opciones de asignación**, puede activar o desactivar los planes de servicio, incluidas las licencias que ha seleccionado. Cuando se realiza un cambio, asegúrese de hacer clic en **Aceptar** para guardar los cambios.
4. Para completar la asignación, en el panel **Asignar licencia**, haga clic en **Asignar** en la parte inferior del panel.

## <a name="remove-guests-from-all-users-group"></a>Eliminación de los invitados del grupo Todos los usuarios

Quizás su plan administrativo final es asignar todos los usuarios invitados a sus propios grupos por empresa. Ahora también puede cambiar el grupo **Todos los usuarios** de modo que esté reservado solo para los usuarios miembros de la organización. A continuación, puede usarlo para asignar aplicaciones y licencias que son específicas de su organización principal.

   ![Cambio del grupo Todos los usuarios a solo miembros](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

**Para eliminar el grupo de usuarios invitados**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que sea administrador global de su organización.
2. Seleccione **Azure Active Directory** > **Grupos**. Seleccione el grupo **Guest users Contoso**, seleccione los puntos suspensivos (...) y, a continuación, seleccione **Eliminar**. Cuando se elimina el grupo, se eliminan las licencias asignadas.

**Para restaurar el grupo Todos los usuarios**
1. Seleccione **Azure Active Directory** > **Grupos**. Seleccione el nombre del grupo **Todos los usuarios** para abrir el grupo.
1. Seleccione **Reglas de pertenencia dinámica**, borre todo el texto de la regla y seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:
> [!div class="checklist"]
> * Creación de un grupo de usuarios invitados
> * Asignación de licencias al nuevo grupo
> * Cambio del grupo Todos los usuarios a solo miembros

Avance al siguiente artículo para conocer más aspectos básicos de las licencias basadas en grupos
> [!div class="nextstepaction"]
> [Conceptos básicos de las licencias basadas en grupos](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



