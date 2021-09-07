---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 4ab3986f66d420d1fcb1ae67747cae5e42f7c59b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779892"
---
Siga estos pasos para crear el registro de aplicación de API web (Id. de aplicación: 2):

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **nombre** para la aplicación (por ejemplo, *my-api1*). Deje los valores predeterminados para el **URI de redirección**. 
1. Seleccione **Registrar**.
1. Una vez completado el registro de la aplicación, seleccione **Información general.**
1. Anote el **Id. de aplicación (cliente)** para usarlo más adelante al configurar la aplicación web.

    ![Captura de pantalla en la que se muestra cómo obtener un Id. de aplicación de la API web.](./media/active-directory-b2c-app-integration-register-api/get-azure-ad-b2c-web-api-app-id.png)