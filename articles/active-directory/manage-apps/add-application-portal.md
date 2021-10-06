---
title: 'Inicio rápido: Adición de una aplicación empresarial'
description: Agregue una aplicación empresarial en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 66de4e18f8a6c88ae3c1504197eb6f39632c6425
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058173"
---
# <a name="quickstart-add-an-enterprise-application-in-azure-active-directory"></a>Inicio rápido: Adición de una aplicación empresarial en Azure Active Directory

En esta guía de inicio rápido, usará el centro de administración de Azure Active Directory para agregar una aplicación empresarial al inquilino de Azure Active Directory (Azure AD). Azure AD incluye una galería que contiene miles de aplicaciones empresariales que se han integrado previamente. Muchas de las aplicaciones que su organización usa probablemente estén ya en la galería. En este inicio rápido, se usa la aplicación llamada **Azure AD SAML Toolkit** como ejemplo, pero los conceptos se aplican a la mayoría de las [aplicaciones empresariales de la galería](../saas-apps/tutorial-list.md).

Para probar los pasos de este inicio rápido, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para agregar una aplicación empresarial al inquilino de Azure AD, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.

## <a name="add-an-enterprise-application"></a>Adición de una aplicación empresarial

Para agregar una aplicación empresarial al inquilino:

1. Vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú de la izquierda, seleccione **Aplicaciones empresariales**. Se abre el panel **Todas las aplicaciones**, en el que se ve una lista de las aplicaciones que hay en su inquilino de Azure AD.
1. En el panel **Aplicaciones empresariales**, seleccione **Nueva aplicación**.
1. Se abre el panel **Examinar la Galería de Azure AD**, que muestra iconos para plataformas en la nube, aplicaciones locales y aplicaciones destacadas. Las aplicaciones que aparecen en la sección **Aplicaciones destacadas** incluyen iconos que indican si admiten el inicio de sesión único federado (SSO) y el aprovisionamiento. Busque y seleccione la aplicación. En este inicio rápido, se utiliza **Azure AD SAML Toolkit**.

    :::image type="content" source="media/add-application-portal/browse-gallery.png" alt-text="Búsqueda la aplicación que desea agregar en la galería de aplicaciones empresariales.":::

1. Escriba el nombre que desea usar para reconocer la instancia de la aplicación. Por ejemplo, `Azure AD SAML Toolkit 1`.
1. Seleccione **Crear**.

Si decide instalar una aplicación que usa el inicio de sesión único basado en OpenID Connect, en lugar de ver el botón **Crear**, verá un botón que le redirigirá a la página de inicio de sesión o de registro de la aplicación en función de si ya tiene una cuenta allí. Para más información, consulte [Adición de una aplicación de inicio de sesión único basado en OpenID Connect](add-application-portal-setup-oidc-sso.md). Después de iniciar sesión, la aplicación se agrega al inquilino.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto completar el siguiente inicio rápido, mantenga la aplicación empresarial que ha creado. De lo contrario, puede considerar la posibilidad de eliminarla para limpiar el inquilino. Para más información, consulte [Inicio rápido: Eliminación de una aplicación del inquilino](delete-application-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo crear una cuenta de usuario y asignarla a la aplicación empresarial que ha agregado.
> [!div class="nextstepaction"]
> [Inicio rápido: Creación y asignación de una cuenta de usuario en Azure Active Directory](add-application-portal-assign-users.md)
