---
title: 'Registro de una aplicación de servicio en Azure AD: Azure API for FHIR'
description: Obtenga información sobre cómo registrar una aplicación cliente de servicio en Azure Active Directory.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 3eb942160e45c589377ee3ecd283ea2939a23934
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273407"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Registro de una aplicación cliente de servicio en Azure Active Directory para Azure API for FHIR

En este artículo aprenderá a registrar una aplicación cliente de servicio en Azure Active Directory. Los registros de aplicaciones cliente son representaciones de Azure Active Directory de aplicaciones que se pueden utilizar para autenticar y obtener tokens. Un cliente de servicio está destinado a ser utilizado por una aplicación para obtener un token de acceso sin la autenticación interactiva de un usuario. Tendrá ciertos permisos de aplicación y utilizará un secreto de aplicación (contraseña) al obtener los tokens de acceso.

Siga estos pasos para crear un cliente de servicio nuevo.

## <a name="app-registrations-in-azure-portal"></a>Registros de aplicaciones en Azure Portal

1. En [Azure Portal](https://portal.azure.com), diríjase a **Azure Active Directory**.

2. Seleccione **App registrations** (Registros de aplicaciones).

    ![Azure Portal. Nuevo registro de aplicaciones.](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. Seleccione **Nuevo registro**.

4. Asigne al cliente de servicio un nombre para mostrar. Las aplicaciones cliente de servicio no suelen usar una dirección URL de respuesta.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. Nuevo registro de aplicaciones de cliente de servicio ":::.

5. Seleccione **Registrar**.

## <a name="api-permissions"></a>Permisos de API

Los permisos para Azure API for FHIR se administran a través de RBAC. Para más información, visite [Configuración de Azure RBAC para FHIR.](configure-azure-rbac.md)

>[!NOTE]
>Use grant_type de client_credentials al intentar retener un token de acceso para Azure API for FHIR mediante herramientas como Postman. Para más información, visite [Testing the FHIR API on Azure API for FHIR](tutorial-web-app-test-postman.md).

## <a name="application-secret"></a>Secreto de aplicación

El cliente de servicio necesita un secreto (contraseña) para obtener un token.

1. Seleccione **Certificados y secretos**.
2. Seleccione **Nuevo secreto de cliente**.

    ![Azure Portal. Secreto de cliente de servicio](media/add-azure-active-directory/portal-aad-register-new-app-registration-service-client-secret.png)

3. Proporcione una descripción y la duración del secreto (1 año, 2 años o nunca).

4. Una vez generado el secreto, solo se mostrará una vez en el portal. Tome nota del mismo y guárdelo de forma segura.

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido cómo registrar una aplicación cliente de servicio en Azure Active Directory. A continuación, pruebe el acceso al servidor de FHIR mediante Postman.
 
>[!div class="nextstepaction"]
>[Acceso al servicio FHIR mediante Postman](./../use-postman.md)
