---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: b356480175c4b3da537b1e3e49fcdca676b2d6d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779874"
---
Si desea conceder permisos a la aplicación (id. de aplicación: 1), siga estos pasos: 

1. Seleccione **Registros de aplicaciones** y, luego, seleccione la aplicación que creó (id. de aplicación: 1).
1. En **Administrar**, seleccione **Permisos de API**.
1. En **Permisos configurados**, seleccione **Agregar un permiso**.
1. Seleccione la pestaña **Mis API**.
1. Seleccione la API a la que la aplicación web debe tener acceso (id. de aplicación: 2). Por ejemplo, *my-api1*.
1. En **Permiso**, expanda **Tareas** y, a continuación, seleccione los ámbitos que definió anteriormente; por ejemplo, *tasks.read* y *tasks.write*.
1. Seleccione **Agregar permisos**.
1. Seleccione **Conceder consentimiento de administrador para \<*your tenant name*>** .
1. Seleccione **Sí**.
1. Seleccione **Actualizar** y, a continuación, compruebe que aparece "Concedido para..." en **Estado** para ambos ámbitos.
1. En la lista **Permisos configurados**, seleccione el ámbito y copie el nombre completo del ámbito. 

    ![Captura de pantalla del panel de permisos configurados en que se muestran los permisos de acceso de lectura concedidos.](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  