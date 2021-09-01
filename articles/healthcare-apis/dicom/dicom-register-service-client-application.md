---
title: 'Registro de una aplicación cliente de servicio en Azure Active Directory: Azure Healthcare APIs para DICOM'
description: En este artículo se explica cómo registrar una aplicación cliente de servicio en Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: 8408d1e2e670083f1e5bb1c30723a3c4ad83b8f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780502"
---
# <a name="register-a-service-client-application"></a>Registro de una aplicación cliente de servicio

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo aprenderá a registrar una aplicación cliente de servicio en Azure Active Directory (Azure AD).

## <a name="application-registrations-in-the-azure-portal"></a>Registros de aplicación en Azure Portal.

Los registros de aplicaciones cliente son representaciones de Azure AD de aplicaciones que se pueden utilizar para autenticar y obtener tokens. Un cliente de servicio está destinado a ser utilizado por una aplicación para obtener un token de acceso sin la autenticación interactiva de un usuario. Tendrá ciertos permisos de aplicación y utilizará un secreto de aplicación (contraseña) al obtener los tokens de acceso.

Consulte los pasos descritos a continuación para crear un nuevo cliente de servicio.

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**.
2. En la hoja **Azure Active Directory**, seleccione **Registros de aplicaciones**.
3. Seleccione **Nuevo registro**.

   [ ![Registros de aplicaciones de Azure.](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. Proporcione a la aplicación de servicio DICOM un nombre para mostrar orientado al usuario. Las aplicaciones cliente de servicio no suelen usar una dirección URL de respuesta.

   [ ![Registrar una aplicación de Azure.](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. Seleccione **Registrar**.

## <a name="api-permissions"></a>Permisos de API

Ahora que ha registrado la aplicación, de servicio DICOM, deberá seleccionar los permisos de API que esta aplicación debe solicitar en nombre de los usuarios.

1. Seleccione la hoja **Permisos de API**.

   [ ![Agregar permisos de API.](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. Seleccione **Agregar un permiso**.

   Si usa DICOM, agregará un permiso a las API de Azure para DICOM mediante la búsqueda de **Azure Healthcare APIs** en API usadas en **APIs Mi organización Usos**. Solo podrá encontrarlo si ya ha registrado el proveedor de recursos `Microsoft.HealthcareAPIs`.

   [ ![Permisos de Mi API](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)


   [ ![Solicitud de permisos de API.](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

3. Seleccione los ámbitos (permisos) que la aplicación cliente confidencial solicitará en nombre del usuario.

   [ ![Seleccione ámbitos de permisos.](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

4. Dé su consentimiento a la aplicación. Si no tiene los permisos necesarios, consulte con el administrador de ADD.


## <a name="application-secret"></a>Secreto de aplicación

El cliente de servicio necesita un secreto (contraseña) para obtener un token.

1. Seleccione **Certificados y secretos** y luego seleccione **Nuevo secreto de cliente**.

   [ ![Certificados y secretos](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. Escriba una **descripción** del secreto de cliente. Seleccione el menú desplegable **Expira** para elegir un período de tiempo de expiración y, a continuación, haga clic en **Agregar**.

   [ ![Descripción del secreto de cliente](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. Una vez creada la cadena secreta de cliente, copie su **valor** y su **id.** y almacénelos en una ubicación segura.

   [ ![Id. del valor del secreto de cliente](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

4. Seleccione Agregar.

> [!NOTE]
> La cadena secreta de cliente solo es visible una vez está en Azure Portal. Hay que salir de la página web de certificados y secretos y, a continuación, volver a ella. Entonces, la cadena Value se enmascara. Es importante realizar una copia de la cadena secreta de cliente inmediatamente después de generarla. Si no tiene una copia de seguridad del secreto de su cliente, debe repetir los pasos anteriores para volver a generarlo.


## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido cómo registrar una aplicación cliente de servicio en Azure AD. 

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)




