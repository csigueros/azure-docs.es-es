---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/08/2021
ms.author: mimart
ms.openlocfilehash: b3bf18bcc0c20407f9c0d4722b95266dde31d731
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708616"
---
Para registrar una aplicación en su inquilino de Azure AD B2C, puede usar la nueva experiencia unificada **Registros de aplicaciones**, o bien la experiencia anterior **Aplicaciones (heredado)** . [Más información acerca de la nueva experiencia](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C:
    1. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
    1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *ROPC_Auth_app*.
1. Deje los demás valores como están y seleccione **Registrar**.
1. Anote el **Id. de aplicación (cliente)** para usarlo en un paso posterior.
1. En **Administrar**, seleccione **Autenticación**.
1. Seleccione **Probar la nueva experiencia** (si se muestra).
1. En **Configuración avanzada** y en la sección **Habilite los siguientes flujos móviles y de escritorio**, seleccione **Sí** para tratar la aplicación como un cliente público. Esta opción de configuración es necesaria para el flujo de ROPC.
1. Seleccione **Guardar**.
1. En el menú de la izquierda, seleccione **Manifiesto** para abrir el editor de manifiestos. 
1. Establezca el atributo **oauth2AllowImplicitFlow** en *true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Seleccione **Guardar**.

#### <a name="applications-legacy"></a>[Applications (Legacy)](#tab/applications-legacy/) (Aplicaciones [heredadas])

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C:
    1. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
    1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**
1. Seleccione **Aplicaciones (heredado)** y, a continuación, **Agregar**.
1. Escriba un nombre para la aplicación. Por ejemplo, *ROPC_Auth_app*.
1. En **Cliente nativo**, seleccione **Sí**.
1. Deje los demás valores como están y seleccione **Crear**.
1. Anote el valor de **ID. DE APLICACIÓN** para usarlo en un paso posterior.