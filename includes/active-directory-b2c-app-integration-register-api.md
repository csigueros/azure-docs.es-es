---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: kengaderdus
ms.openlocfilehash: 65376f6154390bad8bfaa736dc4e76204f65f396
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007642"
---
Siga estos pasos para crear el registro de aplicación de API web (**Id. de aplicación: 2**):

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. En **Nombre**, escriba un nombre para la aplicación (por ejemplo, **my-api1**). Deje los valores predeterminados para **URI de redireccionamiento** y **Tipos de cuenta admitidos**.
1. Seleccione **Registrar**.
1. Una vez completado el registro de la aplicación, seleccione **Información general.**
1. Anote el valor **Id. de aplicación (cliente)** para usarlo más adelante al configurar la aplicación web.

    ![Captura de pantalla en la que se muestra cómo obtener un id. de aplicación de la API web.](./media/active-directory-b2c-app-integration-register-api/get-azure-ad-b2c-web-api-app-id.png)
