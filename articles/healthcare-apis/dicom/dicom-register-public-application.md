---
title: 'Registro de una aplicación cliente pública en Azure Active Directory: Azure Healthcare APIs para DICOM'
description: En este artículo se explica cómo registrar una aplicación pública en Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780510"
---
# <a name="register-a-public-client-application"></a>Registro de una aplicación cliente pública

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo obtendrá información sobre cómo registrar una aplicación pública en Azure Active Directory (Azure AD).

Los registros de aplicaciones cliente son representaciones de aplicaciones de Azure AD que pueden autenticar y solicitar permisos de API en nombre de un usuario. Los clientes públicos son aplicaciones, como las aplicaciones móviles y las aplicaciones JavaScript de una sola página, que no pueden mantener la confidencialidad de los secretos. El procedimiento es similar al [registro de una aplicación cliente confidencial](dicom-register-confidential-client-application.md), pero dado que no se puede confiar en clientes públicos para almacenar un secreto de aplicación, no es necesario agregar uno.

En esta guía de inicio rápido se proporciona información general sobre cómo [registrar una aplicación mediante la plataforma de identidad de Microsoft](.././../active-directory/develop/quickstart-register-app.md).

## <a name="application-registrations-in-the-azure-portal"></a>Registros de aplicación en Azure Portal.

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**.
2. En la hoja **Azure Active Directory**, seleccione **Registros de aplicaciones**.
3. Seleccione **Nuevo registro**.

   [ ![Registro de aplicación DICOM en Azure Portal](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>Introducción al registro de aplicaciones

1. Proporcione a la aplicación de servicio DICOM un nombre para el usuario.

   [ ![Registro de una aplicación de Azure](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. En **Tipos de cuenta admitidos**, seleccione quién puede usar la aplicación o acceder a la API.
3. (**Opcional**) Proporcione un **URI de redireccionamiento**. Estos detalles se pueden cambiar más adelante, pero, si conoce la dirección URL de respuesta de la aplicación, indíquela ahora.
4. Seleccione **Registrar**.

## <a name="configuring-an-application-as-a-public-application"></a>Configuración de una aplicación como pública

Para configurar su aplicación [de escritorio](.././../active-directory/develop/scenario-desktop-app-registration.md), [móvil](.././../active-directory/develop/scenario-mobile-app-registration.md) o [de página única](.././../active-directory/develop/scenario-mobile-app-registration.md) como aplicación pública:

1. En [Azure Portal](https://portal.azure.com), seleccione la hoja **Registros de aplicaciones** y, a continuación, la hoja **Autenticación**.

   [ ![Registro de aplicaciones públicas de Azure.](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. En **Configuración avanzada**, seleccione **Sí**.

   En el caso de una aplicación de página única, seleccione **Tokens de acceso** y **Tokens de id.** para habilitar el flujo implícito.
   * Si los usuarios pueden iniciar sesión en la aplicación, seleccione **Tokens de id.**
   * Si la aplicación también necesita llamar a una API web protegida, seleccione **Tokens de acceso**.

3. Seleccione **Guardar**.

## <a name="api-permissions"></a>Permisos de API

De forma similar a la [aplicación cliente confidencial](dicom-register-confidential-client-application.md), deberá seleccionar los permisos de API que esta aplicación debe solicitar en nombre de los usuarios.

Si usa el servicio DICOM, agregará un permiso a la API de Azure para DICOM buscando Azure Healthcare APIs en “API usadas en mi organización”. Solo lo encontrará si ya ha registrado el proveedor de recursos Microsoft.HealthcareAPIs.

[ ![Permisos de Mi API](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

Seleccione los permisos que desea que solicite la aplicación del servicio DICOM.

[ ![Solicitud de permisos de API](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha obtenido información sobre cómo registrar una aplicación cliente pública en Azure Active Directory.  

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)










