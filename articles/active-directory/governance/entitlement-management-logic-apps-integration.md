---
title: Desencadenamiento de una instancia de Logic Apps personalizada con administración de derechos de Azure AD
description: Aprenda a configurar y usar instancias personalizadas de Logic Apps en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: karenhoran
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 11/02/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd706be5107558422971336cdb598c1148d6f83
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511330"
---
# <a name="trigger-custom-logic-apps-with-azure-ad-entitlement-management"></a>Desencadenamiento de una instancia de Logic Apps personalizada con administración de derechos de Azure AD


[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) se puede usar para automatizar flujos de trabajo personalizados y conectar aplicaciones y servicios en un solo lugar. Los usuarios pueden integrar Logic Apps con la administración de derechos para ampliar sus flujos de trabajo de gobernanza más allá de los principales casos de uso de la administración de derechos.

Después, estas instancias de Logic Apps se pueden desencadenar para ejecutarse de acuerdo con los casos de uso de administración de derechos, como cuando se concede o solicita un paquete de acceso. Por ejemplo, un administrador podría crear y vincular una aplicación lógica personalizada a la administración de derechos para que cuando un usuario solicite un paquete de acceso, se desencadene una aplicación lógica que garantice que al usuario también se le asignen ciertas características en una aplicación SAAS de terceros (como Salesforce) o se le envíe un correo electrónico personalizado.

Los casos de uso de administración de derechos que se pueden integrar con Logic Apps incluyen los siguientes:  

- cuando se solicita un paquete de acceso  

- cuando se concede una solicitud de paquete de acceso  

- cuando expira una asignación de paquete de acceso  

Estos desencadenadores para Logic Apps se controlan en una nueva pestaña dentro de las directivas de paquetes de acceso denominada **Reglas**. Además, en la pestaña **Extensiones personalizadas** de la página Catálogo se mostrarán todas las instancias de Logic Apps agregadas para un catálogo determinado. En este artículo se describe cómo crear y agregar aplicaciones lógicas a catálogos y a paquetes de acceso en la administración de derechos.

## <a name="create-and-add-a-logic-app-to-a-catalog-for-use-in-entitlement-management"></a>Creación y adición de una aplicación lógica a un catálogo para su uso en la administración de derechos 

**Requisitos previos de roles:** Administrador global, Administrador de Identity Governance, Propietario del catálogo o Propietario del grupo de recursos 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. En Azure Portal, seleccione **Azure Active Directory** y, luego, **Gobierno de identidades**. 

1. En el menú de la izquierda, seleccione **Catálogos**. 

1. En el menú de la izquierda, seleccione **Extensiones personalizadas (versión preliminar)** . 

1. En la barra de navegación del encabezado, seleccione **Agregar una extensión personalizada**.  

1. En la pestaña **Datos básicos**, escriba el nombre de la extensión personalizada (la aplicación lógica vinculada que va a agregar) y la descripción del flujo de trabajo. A partir de ahora estos campos se mostrarán en la pestaña **Extensiones personalizadas** del catálogo. 

    ![Panel para crear una extensión personalizada](./media/entitlement-management-logic-apps/create-custom-extension.png)


1. Después, vaya a la pestaña **Detalles**. 

1. Seleccione **Sí** en el campo "Create new logic app" (Crear aplicación lógica). En caso contrario, seleccione **No** y vaya al paso 9 si va a usar una aplicación lógica existente. Si ha seleccionado Sí, seleccione una de las opciones siguientes y pase al paso 9: 

    1. Seleccione **create new Azure AD application** (Crear aplicación de Azure AD) si quiere usar una aplicación nueva como base para la nueva aplicación lógica, o bien
    
        ![Panel para seleccionar una aplicación nueva para la aplicación lógica](./media/entitlement-management-logic-apps/new-app-selection.png)

    1. seleccione **una aplicación de Azure AD existente** si quiere usar una aplicación existente como base para la nueva aplicación lógica.
    
        ![Panel para seleccionar la aplicación existente para la aplicación lógica](./media/entitlement-management-logic-apps/existing-app-selection.png)

    > [!Note]    
    > Más adelante, puede editar lo que hace la aplicación lógica en el diseñador de aplicaciones lógicas. Para ello, seleccione la aplicación lógica que ha creado en la pestaña **Extensiones personalizadas** de **Catálogos**.  

1. A continuación, escriba el **identificador de la suscripción**, el **grupo de recursos** y el **nombre de la aplicación lógica.** 

1. Luego, seleccione **Validar y crear**. 

1. Revise el resumen de la extensión personalizada y asegúrese de que los detalles de la llamada a la aplicación lógica son correctos. Seleccione **Crear**.

    ![Ejemplo de resumen de la extensión personalizada](./media/entitlement-management-logic-apps/custom-extension-summary.png)

1. Esta extensión personalizada de la aplicación lógica vinculada aparecerá ahora en la pestaña Extensiones personalizadas en Catálogos. Podrá llamarla en las directivas de paquetes de acceso.


## <a name="edit-a-linked-logic-app"></a>Edición de una aplicación lógica vinculada 

**Requisitos previos de roles:** Administrador global, administrador de Identity Governance o propietario del catálogo 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. En Azure Portal, seleccione **Azure Active Directory** y, luego, **Gobierno de identidades**. 

1. En el menú de la izquierda, seleccione **Catálogos**. 

1. En el menú de la izquierda, seleccione **Extensiones personalizadas**. 

1. Aquí puede ver todas las extensiones personalizadas (Logic Apps) que ha agregado a este catálogo. Para editar un flujo de trabajo de aplicación lógica o a fin de crear uno para una aplicación lógica recién agregada, seleccione la extensión personalizada de aplicación lógica en **Punto de conexión**. Esto abrirá el diseñador de aplicaciones lógicas y le permitirá crear el flujo de trabajo.  

 Para más información sobre la creación de flujos de trabajo de aplicación lógica, vea [Creación de flujos de trabajo automatizados con Azure Logic Apps en Azure Portal](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

## <a name="add-custom-extension-to-access-package-policy"></a>Adición de una extensión personalizada para acceder a la directiva de paquetes 

**Requisitos previos de rol**: Administrador global, Administrador de Identity Governance, Propietario del catálogo o Administrador de paquetes de acceso 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. En Azure Portal, seleccione **Azure Active Directory** y, luego, **Gobierno de identidades**. 

1. En el menú de la izquierda, seleccione **Paquetes de acceso**. 

1. Seleccione **Nuevo paquete de acceso** si quiere agregar una extensión personalizada (Aplicación lógica) a un nuevo paquete de acceso. O bien, seleccione el paquete de acceso al que quiera agregar una extensión personalizada (Aplicación lógica) de la lista de paquetes de acceso que ya se han creado.  

    > [!NOTE]  
    > Para más información sobre cómo crear un paquete de acceso, vea [Creación de un paquete de acceso en la administración de derechos](entitlement-management-access-package-create.md).  Para más información sobre cómo editar un paquete de acceso existente, vea [Cambio de la configuración de solicitud de un paquete de acceso en la administración de derechos de Azure AD](entitlement-management-access-package-request-policy.md#open-and-edit-an-existing-policy-of-request-settings). 

1. En la configuración de directiva del paquete de acceso, vaya a la pestaña **Reglas (versión preliminar)** . 

1. En el menú **Cuando** siguiente, seleccione el evento de paquete de acceso que quiera usar como desencadenador para esta extensión personalizada (aplicación lógica). Por ejemplo, si solo quiere desencadenar el flujo de trabajo de la aplicación lógica de extensión personalizada cuando un usuario solicita el paquete de acceso, seleccione **when request is created** (cuando se cree la solicitud). 

1. En el menú **Do** (Hacer) siguiente, seleccione la extensión personalizada (Aplicación lógica) que quiera agregar al paquete de acceso. La acción do (Hacer) que seleccione se ejecutará cuando se produzca el evento seleccionado en el campo when (cuando).  

1. Seleccione **Crear** si quiere agregar la extensión personalizada a un nuevo paquete de acceso. Seleccione **Actualizar** si quiere agregarla a un paquete de acceso existente.

    ![Adición de una aplicación lógica al paquete de acceso](./media/entitlement-management-logic-apps/add-logic-apps-access-package.png)

## <a name="troubleshooting-and-validation"></a>Solución de problemas y validación 

Para comprobar que la extensión personalizada ha desencadenado correctamente la aplicación lógica asociada cuando se llama mediante la opción **Hacer** del paquete de acceso, puede ver los registros de aplicación lógica. 

En la página de información general de una aplicación lógica específica se mostrarán las marcas de tiempo de la última ejecución de la aplicación lógica. Además, en la información general de un grupo de recursos con una extensión personalizada vinculada se mostrará el nombre de esa extensión personalizada si se ha configurado correctamente.  

## <a name="next-steps"></a>Pasos siguientes