---
title: 'Procedimiento: Cambio de los tipos de cuenta que admite una aplicación | Azure'
titleSuffix: Microsoft identity platform
description: En este procedimiento, va a configurar una aplicación registrada en la plataforma de identidad de Microsoft para cambiar los usuarios o las cuentas que pueden acceder a la aplicación.
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
ms.openlocfilehash: c89cb9c43ae274170e0cedb01b1908a874b7317e
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039954"
---
# <a name="modify-the-accounts-supported-by-an-application"></a>Modificación de las cuentas compatibles con una aplicación

Cuando registró la aplicación en la plataforma de identidad de Microsoft, especificó quién (qué tipos de cuenta) puede acceder a ella. Por ejemplo, puede que haya especificado cuentas solo en su organización, lo cual es una aplicación de *inquilino único*. O bien, puede que haya especificado cuentas en varias organizaciones (incluida la suya), lo cual es una aplicación *multiinquilino*.

En las secciones siguientes, aprenderá a modificar el registro de la aplicación en Azure Portal para cambiar quién o qué tipos de cuentas pueden acceder a la aplicación.

## <a name="prerequisites"></a>Prerrequisitos

* Una [aplicación registrada en el inquilino de Azure AD](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Cambio del registro de la aplicación para admitir distintas cuentas

Para especificar una configuración diferente para los tipos de cuenta que admite el registro de aplicación existente:

1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Si tiene acceso a varios inquilinos, use el filtro **Directorio + suscripción** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para seleccionar el inquilino en el que desea registrar una aplicación.
1. Busque y seleccione **Azure Active Directory**.
1. En **Administrar**, seleccione **Registros de aplicaciones** y, a continuación, seleccione la aplicación.
1. Ahora, especifique qué personas pueden usar la aplicación. A veces, se conoce a estas personas como *público de inicio de sesión*.

    | Tipos de cuenta admitidos | Descripción |
    |-------------------------|-------------|
    | **Solo las cuentas de este directorio organizativo** | Seleccione esta opción si va a desarrollar una aplicación para que la utilicen usuarios (o invitados) de *su* inquilino.<br><br>A menudo denominada aplicación de *línea de negocio*, se trata de una aplicación de **un solo inquilino** en la plataforma de identidad de Microsoft. |
    | **Cuentas en cualquier directorio organizativo** | Seleccione esta opción si desea que los usuarios de *cualquier* inquilino de Azure AD puedan usar la aplicación. Esta opción es adecuada si, por ejemplo, va a desarrollar una aplicación de software como servicio (SaaS) que desea proporcionar a varias organizaciones.<br><br>Esta aplicación se denomina **multiinquilino** en la plataforma de identidad de Microsoft. |
1. Seleccione **Guardar**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Motivos de error al cambiar a una aplicación multiinquilino

A veces, el cambio de un registro de aplicaciones de un único inquilino a multiinquilino puede producir un error debido a conflictos de nombres de URI de identificador de aplicación. Un ejemplo de URI de identificador de aplicación es `https://contoso.onmicrosoft.com/myapp`.

El URI de id. de aplicación es una de las maneras en que una aplicación se identifica en los mensajes de protocolo. Cuando la aplicación es de un solo inquilino, es suficiente con que el URI de identificador de aplicación sea único en ese inquilino. En el caso de una aplicación multiinquilino, debe ser único a nivel global para que Azure AD pueda encontrar la aplicación entre todos los inquilinos. El carácter globalmente único viene impuesto por la necesidad de que el nombre de host del URI de identificador de aplicación coincida con uno de los [dominios de publicador comprobados](howto-configure-publisher-domain.md) del inquilino de Azure AD.

Por ejemplo, si el nombre del inquilino es *contoso.onmicrosoft.com*, un URI de identificador de aplicación válido sería `https://contoso.onmicrosoft.com/myapp`. Si el inquilino tiene el dominio comprobado *contoso.com*, un URI del identificador de aplicación válido también sería `https://contoso.com/myapp`. Si el URI del identificador de aplicación no sigue el segundo patrón, `https://contoso.com/myapp`, se producirá un error al convertir el registro de la aplicación en multiinquilino.

Para más información sobre cómo configurar un dominio de publicador comprobado, consulte [Configuración de un dominio comprobado](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los requisitos para la [conversión de una aplicación de un solo inquilino a una multiinquilino](howto-convert-app-to-be-multi-tenant.md).
