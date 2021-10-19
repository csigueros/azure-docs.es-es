---
title: 'Registro de una aplicación cliente confidencial en Azure AD: Azure API for FHIR'
description: Registre una aplicación cliente confidencial en Azure Active Directory que se autentique en nombre de un usuario y solicite acceso a las aplicaciones de recursos.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 2851f230a19bfbafb514e9fb22fd3f37de1f610b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272554"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Registro de una aplicación de cliente confidencial en Azure Active Directory para Azure API for FHIR

En este tutorial aprenderá a registrar una aplicación cliente confidencial en Azure Active Directory (Azure AD).  

Un registro de aplicación cliente es una representación de Azure AD de una aplicación que se puede usar para autenticar en nombre de un usuario y solicitar acceso a [aplicaciones de recursos](register-resource-azure-ad-client-app.md). Una aplicación cliente confidencial es una aplicación en la que se puede confiar para almacenar un secreto y presentar dicho secreto cuando se solicitan tokens de acceso. Ejemplos de aplicaciones confidenciales son las aplicaciones de servidor. 

Para registrar una nueva aplicación cliente confidencial, consulte los siguientes pasos. 

## <a name="register-a-new-application"></a>Registro de una nueva aplicación

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**.

1. Seleccione **App registrations** (Registros de aplicaciones). 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-new-app-registration.png" alt-text="Azure Portal. Nuevo registro de aplicaciones.":::

1. Seleccione **Nuevo registro**.

1. Asigne a la aplicación un nombre para mostrar orientado al usuario.

1. En **Tipos de cuenta admitidos**, seleccione quién puede usar la aplicación o acceder a la API.

1. (Opcional) Proporcione un **URI de redireccionamiento**. Estos detalles se pueden cambiar más adelante, pero si conoce la dirección URL de respuesta de la aplicación, indíquelo ahora.

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client.png" alt-text="Nuevo registro de la aplicación cliente confidencial.":::

1. Seleccione **Registrar**.

## <a name="api-permissions"></a>Permisos de API

Los permisos para Azure API for FHIR se administran a través de RBAC. Para más información, visite [Configuración de RbAC de Azure para FHIR.](configure-azure-rbac.md)

>[!NOTE]
>Use grant_type de client_credentials al intentar retener un token de acceso para Azure API for FHIR mediante herramientas como Postman. Para obtener más información, [visite Testing the FHIR API on Azure API for FHIR](tutorial-web-app-test-postman.md).


## <a name="application-secret"></a>Secreto de aplicación

1. Seleccione **Certificados y secretos** y luego seleccione **Nuevo secreto de cliente**. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-secret.png" alt-text="Cliente confidencial. Secreto de la aplicación.":::

1. Escriba una **descripción** del secreto de cliente. Seleccione el menú desplegable **Expira** para elegir un período de tiempo de expiración y, a continuación, haga clic en **Agregar**.

   :::image type="content" source="media/add-azure-active-directory/add-a-client-secret.png" alt-text="Adición de un secreto de cliente.":::

1. Una vez creada la cadena secreta de cliente, copie su **valor** y su **identificador** y almacénelos en una ubicación segura.

   :::image type="content" source="media/add-azure-active-directory/client-secret-string-password.png" alt-text="Cadena de secreto del cliente."::: 

> [!NOTE]
>La cadena secreta de cliente solo está visible una vez dentro de Azure Portal. Si sale de la página web "Certificados y secretos" y, a continuación, vuelve a ella, la cadena Value se enmascara. Es importante realizar una copia de la cadena secreta de cliente inmediatamente después de generarla. Si no tiene una copia de seguridad del secreto de cliente, repita los pasos anteriores para volver a generarlo.
 
## <a name="next-steps"></a>Pasos siguientes

En este artículo se le ha guiado por los pasos para registrar una aplicación cliente confidencial en Azure AD. También se le mostraron los pasos para agregar permisos de API a Azure Healthcare API. Por último, se le ha mostrado cómo crear un secreto de aplicación. Además, puede aprender a acceder al servidor de FHIR mediante Postman.
 
>[!div class="nextstepaction"]
>[Acceso al servicio FHIR mediante Postman](./../use-postman.md)
