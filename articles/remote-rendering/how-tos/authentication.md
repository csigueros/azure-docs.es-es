---
title: Authentication
description: Explica el funcionamiento de la autenticación
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 01b2dbaa8ed318f08fd68660078ae6fb9923221d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969774"
---
# <a name="configure-authentication"></a>Configurar la autenticación

Azure Remote Rendering usa el mismo mecanismo de autenticación que [Azure Spatial Anchors (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Los clientes deben establecer *una* de las siguientes para llamar a las API de REST correctamente:

* **AccountKey**: se puede obtener en la pestaña "Claves" de la cuenta de Remote Rendering de Azure Portal. Las claves de cuenta solo se recomiendan para el desarrollo o la creación de prototipos.
    ![Id. de cuenta](./media/azure-account-primary-key.png)

* **AccountDomain**: se puede obtener en la pestaña "Información general" de la cuenta de Remote Rendering de Azure Portal.
    ![Dominio de cuenta](./media/azure-account-domain.png)

* **AuthenticationToken** es un token de Azure AD, que se puede obtener mediante la [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Hay varios flujos diferentes disponibles para aceptar las credenciales de usuario y usar esas credenciales para obtener un token de acceso.

* **MRAccessToken** es un token de MR, que se puede obtener del servicio de token de seguridad (STS) de Azure Mixed Reality. Recuperado del punto de conexión `https://sts.<accountDomain>` mediante una llamada REST similar a lo siguiente:

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    El encabezado de autorización tiene el formato siguiente: `Bearer <Azure_AD_token>` o `Bearer <accoundId>:<accountKey>`. Es preferible el primero por seguridad. El token que devuelve esta llamada REST es el token de acceso de MR.

## <a name="authentication-for-deployed-applications"></a>Autenticación para aplicaciones implementadas

Se recomiendan las claves de cuenta para la creación rápida de prototipos solo durante la fase de desarrollo. Se recomienda no enviar su aplicación a producción con una clave de cuenta insertada en ella. El enfoque recomendado es usar un enfoque de autenticación de Azure AD basado en el usuario o en el servicio.

### <a name="azure-ad-user-authentication"></a>Autenticación de usuario de Azure AD

La autenticación de Azure AD se describe en la [Documentación de Azure Spatial Anchors](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication).

Siga los pasos para configurar la autenticación de usuario de Azure Active Directory en Azure Portal.

1. Registre la aplicación en Azure Active Directory. Como parte del registro, deberá determinar si la aplicación debe ser multiinquilino. También deberá proporcionar las direcciones URL de redireccionamiento que se permiten para la aplicación en la hoja Autenticación.
:::image type="content" source="./media/azure-active-directory-app-setup.png" alt-text="Configuración de autenticación":::

1. En la pestaña Permisos de API, solicite **Permisos delegados** para el ámbito **mixedreality.signin** en **mixedreality**.
:::image type="content" source="./media/azure-active-directory-app-api-permissions.png" alt-text="Permisos de API":::

1. Conceda permiso de administrador en la pestaña Seguridad -> Permisos. :::image type="content" source="./media/azure-active-directory-grant-admin-consent.png" alt-text="Consentimiento del administrador":::

1. A continuación, vaya al recurso de Azure Remote Rendering. En el panel Control de acceso, conceda los [roles](#azure-role-based-access-control) deseados para las aplicaciones y el usuario en nombre de los que desea usar permisos de acceso delegados para el recurso de Azure Remote Rendering.
:::image type="content" source="./media/azure-remote-rendering-add-role-assignment.png" alt-text="Adición de permisos":::
:::image type="content" source="./media/azure-remote-rendering-role-assignments.png" alt-text="Asignaciones de roles":::

Para obtener información sobre el uso de la autenticación de usuario de Azure AD en el código de la aplicación, consulte el [Tutorial: Protección de Azure Remote Rendering y el almacenamiento de modelos: Autenticación de Azure Active Directory](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication).

## <a name="azure-role-based-access-control"></a>Control de acceso basado en roles de Azure

Para ayudar a controlar el nivel de acceso concedido al servicio, use los siguientes roles al conceder el acceso basado en roles:

* **Administrador de Remote Rendering**: proporciona al usuario funcionalidades de conversión, administración de sesiones, representación y diagnóstico para Azure Remote Rendering.
* **Cliente de Remote Rendering**: proporciona al usuario funcionalidades de administración de sesiones, representación y diagnóstico para Azure Remote Rendering.

## <a name="next-steps"></a>Pasos siguientes

* [Crear una cuenta](create-an-account.md)
* [Uso de las API de front-end de Azure para la autenticación](frontend-apis.md)
* [Scripts de PowerShell de ejemplo](../samples/powershell-example-scripts.md)
