---
title: Adición del inicio de sesión único vinculado a una aplicación
description: Adición del inicio de sesión único vinculado a una aplicación en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: bc50777e503c50ca68955b8324b75c7c6aa66c77
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548489"
---
# <a name="add-linked-single-sign-on-to-an-application"></a>Adición del inicio de sesión único vinculado a una aplicación

En este artículo se muestra cómo configurar el inicio de sesión único (SSO) basado en vínculo para la aplicación en Azure Active Directory (Azure AD). El inicio de sesión único basado en vínculo permite a Azure AD proporcionar inicio de sesión único a una aplicación que ya está configurada para el inicio de sesión único en otro servicio. La opción Vinculado permite configurar la ubicación de destino cuando un usuario selecciona la aplicación en el portal de Microsoft 365 o Aplicaciones de la organización.

El inicio de sesión único basado en vínculo no proporciona la funcionalidad de inicio de sesión a través de Azure AD. La opción solo establece la ubicación a la que se envían los usuarios cuando seleccionen la aplicación en Aplicaciones o el portal Microsoft 365.

Estos son algunos de los escenarios comunes en los que el inicio de sesión único basado en vínculo resulta de gran utilidad:
- Incorporación de un vínculo a una aplicación web personalizada que actualmente use la federación, como Servicios de federación de Active Directory (AD FS).
- Incorporación de vínculos profundos a páginas web específicas que desea que aparezcan en las páginas de acceso de su usuario.
- Incorporación de un vínculo a una aplicación que no requiera autenticación. La opción vinculada no proporciona funcionalidad de inicio de sesión con credenciales de Azure AD, aunque sí puede usar algunas de las otras características de las aplicaciones empresariales. Por ejemplo, puede usar registros de auditoría y agregar un logotipo personalizado y un nombre de la aplicación.

## <a name="prerequisites"></a>Requisitos previos

Para configurar el inicio de sesión único basado en vínculo en su inquilino de Azure AD, necesita lo siguiente:
-   Una cuenta de Azure con una suscripción activa. [Cree su cuenta de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
-   Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
-   Una aplicación que admite el inicio de sesión único basado en vínculo.

## <a name="configure-linked-based-single-sign-on"></a>Configure el inicio de sesión único basado en vínculo

1.  Inicie sesión en [Azure Portal](https://portal.azure.com) con el rol adecuado.
2.  Seleccione **Azure Active Directory** en Azure Services y, a continuación, **Aplicaciones empresariales**.
3.  Busque y seleccione la aplicación a la que desea agregar el inicio de sesión único vinculado.
4.  Seleccione **Inicio de sesión único** y, a continuación, **Agregar**.
5.  Escriba la dirección URL de la página de inicio de sesión de la aplicación.
6.  Seleccione **Guardar**. 

## <a name="next-steps"></a>Pasos siguientes

- [Administración del acceso a aplicaciones](what-is-access-management.md)