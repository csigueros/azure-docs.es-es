---
title: 'Búsqueda de nombres de usuario durante el inicio de sesión: Azure Active Directory | Microsoft Docs'
description: Cómo los mensajes en pantalla reflejan la búsqueda del nombre de usuario durante el inicio de sesión de Azure Active Directory
services: active-directory
author: curtand
manager: KarenH444
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca9aa2d0d90d048a8fe3a4399bf3dd579be3de4
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985549"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Detección del dominio de inicio para las páginas de inicio de sesión de Azure Active Directory

Vamos a cambiar el comportamiento de inicio de sesión en Azure Active Directory (Azure AD) para incorporar nuevos métodos de autenticación y mejorar la facilidad de uso. Durante el inicio de sesión, Azure AD determina dónde un usuario necesita autenticarse. Azure AD toma decisiones inteligentes según la lectura de la configuración de la organización y del usuario para saber el nombre de usuario escrito en la página de inicio de sesión. Se trata de un avance hacia un futuro sin contraseñas que permite otras credenciales como FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamiento de la detección del dominio de inicio

Históricamente, la detección del dominio de inicio la controlaba el dominio que se proporciona en el inicio de sesión o mediante una directiva de detección del dominio de inicio para algunas aplicaciones heredadas. Por ejemplo, en nuestro comportamiento de detección, un usuario de Azure Active Directory podría escribir incorrectamente su nombre de usuario pero, aún así, conseguiría acceder a la pantalla de recopilación de credenciales de la organización. Eso sucede cuando el usuario facilita correctamente el nombre de dominio de la organización “contoso.com”. Este comportamiento no permite la granularidad para personalizar las experiencias de un usuario individual.

Para admitir una serie más amplia de credenciales y aumentar la facilidad de uso, se ha actualizado el comportamiento de la búsqueda de nombre de usuario de Azure Active Directory durante el proceso de inicio de sesión. El nuevo comportamiento toma decisiones inteligentes según la lectura de la configuración del nivel de organización y usuario en función del nombre de usuario escrito en la página de inicio de sesión. Para hacerlo posible, Azure Active Directory comprobará si el nombre de usuario escrito en la página de inicio de sesión existe en el dominio especificado o redirecciona al usuario para que proporcione las credenciales.

Una ventaja adicional de este trabajo es la mejora de los mensajes de error. Estos son algunos ejemplos de mejora de los mensajes de error al iniciar sesión en una aplicación que solo admite usurarios de Azure Active Directory.

- El nombre de usuario se ha escrito incorrectamente o aún no se ha sincronizado con Azure AD:
  
    ![el nombre de usuario se ha escrito incorrectamente o no se ha encontrado](./media/signin-realm-discovery/typo-username.png)
  
- El nombre de dominio se ha escrito incorrectamente:
  
    ![el nombre de dominio se ha escrito incorrectamente o no se ha encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- El usuario intenta iniciar sesión con un dominio de consumidor conocido:
  
    ![inicio de sesión con un dominio de consumidor conocido](./media/signin-realm-discovery/consumer-domain.png)
  
- La contraseña se ha escrito incorrectamente, pero el nombre de usuario es preciso:  
  
    ![la contraseña se ha escrito incorrectamente con un nombre de usuario correcto](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esta característica puede influir en los dominios federados que se basan en la detección del dominio de inicio del nivel de dominio anterior para forzar la federación. Para obtener actualizaciones sobre cuándo se agregará la compatibilidad con dominios federados, vea [Detección del dominio de inicio durante el inicio de sesión en los servicios de Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Mientras tanto, algunas organizaciones han formado a sus empleados para iniciar sesión con un nombre de usuario que no existe en Azure Active Directory, pero que contiene el nombre de dominio adecuado, porque actualmente los nombres de dominio enrutan a los usuarios al punto de conexión del dominio de su organización. El nuevo comportamiento de inicio de sesión no permite esto. Se notifica al usuario para que corrija el nombre de usuario y no se le permite iniciar sesión con un nombre de usuario que no existe en Azure Active Directory.
>
> Si un usuario o su organización realizan actividades que dependen del comportamiento anterior, es importante que los administradores de la organización actualicen la documentación de autenticación e inicio de sesión del empleado y formen a los empleados para que usen su nombre de usuario de Azure Active Directory para iniciar sesión.
  
Si tiene problemas con el nuevo comportamiento, deje sus comentarios en la sección **Comentarios** sección de este artículo.  

## <a name="next-steps"></a>Pasos siguientes

[Personalización de marca del inicio de sesión](../fundamentals/add-custom-domain.md)
