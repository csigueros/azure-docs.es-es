---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: bc3417f0ce0fae54c86c66bf3e55fd183d497c21
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227678"
---
Si desea conceder permisos a la aplicación (**identificador de aplicación: 1**), siga estos pasos: 

1. Seleccione **Registros de aplicaciones** y, luego, la aplicación que creó (**identificador de aplicación: 1**).
1. En **Administrar**, seleccione **Permisos de API**.
1. En **Permisos configurados**, seleccione **Agregar un permiso**.
1. Seleccione la pestaña **Mis API**.
1. Seleccione la API (**identificador de aplicación:2**) a la que la aplicación web debe tener acceso. Por ejemplo, escriba **my-api1**.
1. En **Permiso**, expanda **Tareas** y, a continuación, seleccione los ámbitos que definió anteriormente; por ejemplo, **tasks.read** y **tasks.write**.
1. Seleccione **Agregar permisos**.
1. Seleccione **Conceder consentimiento de administrador para \<*your tenant name*>** .
1. Seleccione **Sí**.
1. Seleccione **Actualizar** y compruebe que aparece **Granted for...** (Concedido para...) en **Estado** para ambos ámbitos.
1. En la lista **Permisos configurados**, seleccione el ámbito y copie el nombre completo del ámbito. 

    ![Captura de pantalla del panel de permisos configurados en que se muestran los permisos de acceso de lectura concedidos.](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  