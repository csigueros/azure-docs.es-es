---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: kengaderdus
ms.openlocfilehash: 02aff100840f80a24210df7d653f2fed0059d9a5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038144"
---
#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/) 

1. Seleccione **Registros de aplicaciones** y, después, seleccione la aplicación web que debe tener acceso a la API. Por ejemplo, *webapp1*.
1. En **Administrar**, seleccione **Permisos de API**.
1. En **Permisos configurados**, seleccione **Agregar un permiso**.
1. Seleccione la pestaña **Mis API**.
1. Seleccione la API a la que la aplicación web debe tener acceso. Por ejemplo, *webapi1*.
1. En **Permiso**, expanda **demo** y, a continuación, seleccione los ámbitos que definió anteriormente. Por ejemplo, *demo.read* y *demo.write*.
1. Seleccione **Agregar permisos**.
1. Seleccione **Conceder consentimiento de administrador para (el nombre de inquilino)** .
1. Si se le pide que seleccione una cuenta, seleccione la cuenta de administrador que tiene iniciada actualmente la sesión o bien inicie sesión con una cuenta en el inquilino de Azure AD B2C que tenga asignado al menos el rol *Administrador de aplicaciones en la nube*.
1. Seleccione **Sí**.
1. Seleccione **Actualizar** y, a continuación, compruebe que aparece "Concedido para..." en **Estado** para ambos ámbitos.

#### <a name="applications-legacy"></a>[Aplicaciones (heredado)](#tab/applications-legacy/)

1. Seleccione **Aplicaciones (heredado)** y, después, seleccione la aplicación web que debe tener acceso a la API. Por ejemplo, *webapp1*.
1. Seleccione **Acceso de API** y, a continuación, seleccione **Agregar**.
1. En la lista desplegable **Seleccionar API** , seleccione la API a la que se debe conceder acceso a la aplicación web. Por ejemplo, *webapi1*.
1. En la lista desplegable **Seleccionar ámbitos**, seleccione los ámbitos que ha definido anteriormente. Por ejemplo, *demo.read* y *demo.write*.
1. Seleccione **Aceptar**.
