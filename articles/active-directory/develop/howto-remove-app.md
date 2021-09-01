---
title: 'Procedimiento: Eliminación de una aplicación registrada en la plataforma de identidad de Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: En este procedimiento, aprenderá a registrar una aplicación mediante la plataforma de identidad de Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 0cea2fdd28c486dbd4b1ca3872cb4e5afaa0379a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039687"
---
# <a name="remove-an-application-registered-with-the-microsoft-identity-platform"></a>Eliminación de una aplicación registrada con la plataforma de identidad de Microsoft

Es posible que los desarrolladores empresariales y proveedores de software como servicio (SaaS) que han registrado aplicaciones con la plataforma de identidad de Microsoft deban quitar el registro de una aplicación.

En las secciones siguientes, aprenderá a:

* Eliminación de una aplicación creada por el usuario o por la organización
* Eliminación de una aplicación creada por otra organización

## <a name="prerequisites"></a>Prerrequisitos

* Una [aplicación registrada en el inquilino de Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Eliminación de una aplicación creada por el usuario o por la organización

Las aplicaciones que el usuario o la empresa han registrado están representadas tanto por un objeto de aplicación como por un objeto de entidad de servicio en el inquilino. Para obtener más información, vea [Objetos de aplicación y objetos de entidad de servicio](./app-objects-and-service-principals.md).

> [!NOTE]
> Si se elimina una aplicación, también se eliminará el objeto de entidad de servicio del directorio principal de la aplicación. En las aplicaciones multiinquilino, los objetos de entidad de servicio de otros directorios no se eliminarán.

Para eliminar una aplicación, debe aparecer como propietario de la aplicación o tener privilegios de administrador.

1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Si tiene acceso a varios inquilinos, use el filtro **Directorio + suscripción** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para seleccionar el inquilino donde está registrada la aplicación.
1. Busque y seleccione **Azure Active Directory**. 
1. En **Administrar**, seleccione **Registros de aplicaciones** y, a continuación, la aplicación que desea configurar. Cuando haya seleccionado la aplicación, verá la página **Introducción** de la aplicación.
1. En la página **Introducción**, seleccione **Eliminar**.
1. Lea las consecuencias de la eliminación.  Active la casilla si aparece una en la parte inferior del panel.
1. Seleccione **Eliminar** para confirmar que quiere eliminar la aplicación.

## <a name="remove-an-application-authored-by-another-organization"></a>Eliminación de una aplicación creada por otra organización

Si está viendo **Registros de aplicaciones** en el contexto de un inquilino, un subconjunto de las aplicaciones que aparecen en la pestaña **Todas las aplicaciones** son de otro inquilino y se registraron en su inquilino durante el proceso de consentimiento. Más específicamente, se representan solo mediante un objeto de entidad de servicio en su inquilino, sin un objeto aplicación correspondiente. Para más información sobre las diferencias entre objetos de aplicación y objetos de entidad de servicio, consulte [Objetos de aplicación y de entidad de servicio en Azure AD](./app-objects-and-service-principals.md).

Para quitar el acceso de una aplicación a su directorio (después de concederle consentimiento), el administrador de la compañía debe eliminar su entidad de servicio. El administrador debe tener acceso de administrador global, y puede quitar la aplicación mediante Azure Portal o usar los [cmdlets de PowerShell de Azure AD](/previous-versions/azure/jj151815(v=azure.100)) para eliminar el acceso.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los [objetos de aplicación y de entidad de servicio](app-objects-and-service-principals.md) en la plataforma de identidad de Microsoft.
