---
title: Cómo proteger una aplicación de demonio en Microsoft Azure Maps
titleSuffix: Azure Maps
description: En este artículo, se explica cómo hospedar aplicaciones de demonio, como procesos en segundo plano, temporizadores y trabajos, en un entorno seguro y de confianza en Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philema
custom.ms: subject-rbac-steps
ms.openlocfilehash: b888dccbd7ce5cbf948b2da5494dd554b2d80649
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112541777"
---
# <a name="secure-a-daemon-application"></a>Protección de una aplicación demonio

En este artículo, se explica cómo hospedar aplicaciones de demonio en un entorno seguro y de confianza en Microsoft Azure Maps.

Estos son algunos ejemplos de aplicaciones de demonio:

- Instancia de Azure WebJobs
- Aplicación de función de Azure
- Servicio de Windows
- Un servicio confiable ejecutándose en segundo plano

## <a name="view-azure-maps-authentication-details"></a>Visualización de los detalles de autenticación de Azure Maps

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

>[!IMPORTANT]
>En el caso de las aplicaciones de producción, se recomienda implementar Azure AD y el control de acceso basado en rol de Azure (RBAC de Azure). Para obtener información general sobre los conceptos de Azure AD, consulte [Autenticación con Azure Maps](azure-maps-authentication.md).

## <a name="scenario-shared-key-authentication-with-azure-key-vault"></a>Escenario: Autenticación mediante claves compartidas con Azure Key Vault

Las aplicaciones que usan la autenticación mediante claves compartidas deben almacenar las claves en un almacén seguro. En este escenario, se explica cómo almacenar de forma segura la clave de una aplicación como un secreto en Azure Key Vault. En lugar de almacenar la clave compartida en la configuración de la aplicación, es la aplicación quien debe recuperar la clave compartida como un secreto de Azure Key Vault. Para simplificar la regeneración de claves, se recomienda que las aplicaciones usen una clave cada vez. Después, las aplicaciones pueden volver a generar la clave no utilizada e implementar la clave regenerada en Azure Key Vault, todo ello mientras se mantienen las conexiones actuales con una clave. Para saber cómo configurar un almacén de Azure Key Vault, consulte [Guía del desarrollador de Azure Key Vault](../key-vault/general/developers-guide.md).

>[!IMPORTANT]
>En este escenario, se accede indirectamente a Azure Active Directory a través de Azure Key Vault. Sin embargo, se recomienda usar la autenticación de Azure AD directamente. El uso de Azure AD evita la complejidad adicional y los requisitos operativos de usar la autenticación mediante claves compartidas y la configuración de un almacén de claves.

En los pasos siguientes se describe este proceso:

1. [Cree un almacén de claves de Azure Key Vault](../key-vault/general/quick-create-portal.md).
2. Cree una [entidad de servicio de Azure AD](../active-directory/fundamentals/service-accounts-principal.md) mediante la creación de un registro de aplicación o una identidad administrada. La entidad de servicio creada es responsable de acceder al almacén de claves de Azure Key Vault.
3. Asigne a la entidad de servicio acceso a los secretos de Azure Key Vault con el permiso `get`. Para obtener más información sobre cómo establecer los permisos, consulte [Asignación de una directiva de acceso de Key Vault mediante Azure Portal](../key-vault/general/assign-access-policy-portal.md).
4. Asigne temporalmente acceso a los secretos con el permiso `set` para el desarrollador.
5. Establezca la clave compartida en los secretos del almacén de claves y haga referencia al identificador del secreto en la configuración de la aplicación de demonio.
6. Quite el permiso `set` de los secretos.
7. Para recuperar el secreto de la clave compartida de Azure Key Vault, implemente la autenticación de Azure Active Directory en la aplicación de demonio.
8. Cree una solicitud de la API REST de Azure Maps con la clave compartida.
Ahora, la aplicación de demonio puede recuperar la clave compartida del almacén de claves.

> [!TIP]
> Si la aplicación está hospedada en el entorno de Azure, se recomienda usar una identidad administrada para reducir el costo y la complejidad de administrar un secreto para la autenticación. Para obtener información sobre cómo configurar una identidad administrada, consulte [Tutorial: Uso de una identidad administrada para conectar Key Vault a una aplicación web de Azure en .NET](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

## <a name="scenario-azure-ad-role-based-access-control"></a>Escenario: control de acceso basado en rol de Azure AD

Cuando se crea una cuenta de Azure Maps, se muestra el valor `Client ID` de Azure Maps en la página Detalles de autenticación de Azure Portal. Este valor representa la cuenta que debe usarse para las solicitudes de la API REST. Este valor debe almacenarse en la configuración de la aplicación y debe recuperarse antes de realizar las solicitudes HTTP. El objetivo del escenario es permitir que la aplicación de demonio se autentique en Azure AD y llame a las API REST de Azure Maps.

> [!TIP]
>Para habilitar las ventajas de los componentes de identidades administradas, se recomienda el hospedaje en Azure Virtual Machines, Virtual Machine Scale Sets o App Services.

### <a name="host-a-daemon-on-azure-resources"></a>Hospedaje de un demonio en recursos de Azure

Cuando ejecute aplicaciones en recursos de Azure, puede configurar identidades administradas por Azure para lograr un esfuerzo de administración de credenciales mínimo y de bajo costo.

Para habilitar el acceso de las aplicaciones a una identidad administrada, consulte [¿Qué son las identidades administradas de recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Las siguientes son algunas de las ventajas de las identidades administradas:

- Autenticación mediante criptografía de claves públicas con certificados X509 administrada por el sistema de Azure.
- Seguridad de Azure AD con certificados X509 en lugar de secretos de cliente.
- Azure administra y renueva todos los certificados asociados al recurso de la identidad administrada.
- Simplificación de la administración operativa de credenciales, ya que la identidad administrada elimina la necesidad de un servicio de almacén de secretos protegido, como Azure Key Vault.

### <a name="host-a-daemon-on-non-azure-resources"></a>Hospedaje de un demonio en recursos que no son de Azure

Cuando se ejecutan aplicaciones en un entorno que no es de Azure, las identidades administradas no están disponibles. Por tanto, debe configurar una entidad de servicio mediante un registro de aplicación de Azure AD para la aplicación de demonio.

#### <a name="create-new-application-registration"></a>Creación de un nuevo registro de aplicación

Si ya ha creado el registro de aplicación, vaya a [Asignación de permisos de API delegados](#assign-delegated-api-permissions).

Para crear un nuevo registro de aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Seleccione **Azure Active Directory**.

3. En **Administrar** en el menú de la izquierda, seleccione **Registros de aplicaciones**.

4. Seleccione la pestaña **+ Nuevo registro**.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/app-registration.png" alt-text="Visualización de registros de aplicaciones.":::

5. Escriba un **Nombre** y seleccione una opción en **Supported account types** (Tipos de cuenta admitidos).

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-create.png" alt-text="Creación de un registro de aplicación.":::

6. Seleccione **Registrar**.  

#### <a name="assign-delegated-api-permissions"></a>Asignación de permisos de API delegados

Para asignar permisos de API delegados a Azure Maps:

1. Si no lo ha hecho aún, inicie sesión en [Azure Portal](https://portal.azure.com).

2. Seleccione **Azure Active Directory**.

3. En **Administrar** en el menú de la izquierda, seleccione **Registros de aplicaciones**.

4. Seleccione la aplicación.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-select.png" alt-text="Selección de registros de aplicaciones.":::

5. En el panel izquierdo, en **Administrar**, seleccione **Permisos de API**.

6. Seleccione **Agregar un permiso**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-add-permissions.png" alt-text="Adición de un permiso de aplicación.":::

7. Seleccione la pestaña **API usadas en mi organización**.

8. En el cuadro de búsqueda, escriba **Azure Maps**.

9. Seleccione **Azure Maps**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/app-permissions.png" alt-text="Solicitud de permiso de aplicación.":::

10. Seleccione la casilla **Access Azure Maps** (Acceder a Azure Maps).

11. Seleccione **Agregar permisos**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/select-app-permissions.png" alt-text="Selección de permisos de API de la aplicación.":::

#### <a name="create-a-client-secret-or-configure-certificate"></a>Creación de un secreto de cliente o configuración de un certificado

Para implementar la autenticación basada en el servidor o en la aplicación, puede elegir una de estas dos opciones:

- Cargar un certificado de clave pública.
- Crear un secreto de cliente.

##### <a name="upload-a-public-key-certificate"></a>Carga de un certificado de clave pública

Para cargar un certificado de clave pública:

1. En el panel izquierdo, en **Administrar**, seleccione **Certificados y secretos**.

2. Seleccione **Cargar certificado**.
   :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate.png" alt-text="Carga de un certificado.":::

3. A la derecha del cuadro de texto, seleccione el icono de archivo.

4. Elija un archivo *.crt*, *.cer* o *.pem* y seleccione **Agregar**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate-file.png" alt-text="Carga de un archivo de certificado.":::

##### <a name="create-a-client-secret"></a>Creación de un secreto de cliente

Para crear un secreto de cliente:

1. En el panel izquierdo, en **Administrar**, seleccione **Certificados y secretos**.

2. Seleccione **+ Nuevo secreto de cliente**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret.png" alt-text="Nuevo secreto de cliente.":::

3. Escriba una descripción del secreto de cliente.

4. Seleccione **Agregar**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret-add.png" alt-text="Adición de un nuevo secreto de cliente.":::

5. Copie el secreto y almacénelo de forma segura en un servicio como Azure Key Vault. También usaremos el secreto en la sección [Solicitud de un token con una identidad administrada](#request-a-token-with-managed-identity) de este artículo.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/copy-client-secret.png" alt-text="Copia del secreto de cliente.":::

     >[!IMPORTANT]
     >Consulte la [Guía del desarrollador de Azure Key Vault](../key-vault/general/developers-guide.md) para almacenar de forma segura el certificado o el secreto. Usará este secreto para obtener tokens de Azure AD.

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

### <a name="request-a-token-with-managed-identity"></a>Solicitud de un token con una identidad administrada

Una vez configurada una identidad administrada para el recurso de hospedaje, puede usar el SDK o la API REST de Azure para adquirir un token para Azure Maps. Para obtener información sobre cómo adquirir un token de acceso, consulte [Cómo usar identidades administradas de recursos de Azure en una máquina virtual de Azure para adquirir un token de acceso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

### <a name="request-token-with-application-registration"></a>Solicitud de un token con un registro de aplicación

Después de registrar la aplicación y asociarla a Azure Maps, debe solicitar un token de acceso.

Para adquirir el token de acceso:

1. Si no lo ha hecho aún, inicie sesión en [Azure Portal](https://portal.azure.com).

2. Seleccione **Azure Active Directory**.

3. En **Administrar** en el menú de la izquierda, seleccione **Registros de aplicaciones**.

4. Seleccione la aplicación.

5. Debe abrirse la página Información general. Copie el id. de la aplicación (id. de cliente) y el id. del directorio (inquilino).

      :::image type="content" border="true" source="./media/how-to-manage-authentication/get-token-params.png" alt-text="Copia de los parámetros del token.":::

Usaremos la aplicación [Postman](https://www.postman.com/) para crear la solicitud de token, pero puede usar otro entorno de desarrollo de API.

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *POST Token Request*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL en la barra de direcciones [reemplace `<Tenant ID>` por el identificador del directorio (inquilino), `<Client ID>` por el identificador de la aplicación (cliente) y `<Client Secret>` por el secreto del cliente]:

    ```http
    https://login.microsoftonline.com/<Tenant ID>/oauth2/v2.0/token?response_type=token&grant_type=client_credentials&client_id=<Client ID>&client_secret=<Client Secret>%3D&scope=api%3A%2F%2Fazmaps.fundamentals%2F.default
    ```

6. Seleccione **Enviar**.

7. Debe mostrarse la siguiente respuesta de JSON:

```json
{
    "token_type": "Bearer",
    "expires_in": 86399,
    "ext_expires_in": 86399,
    "access_token&quot;: &quot;eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFq..."
}
```

Para obtener más información sobre el flujo de autenticación, consulte [La Plataforma de identidad de Microsoft y el flujo de credenciales de cliente de OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret).

## <a name="next-steps"></a>Pasos siguientes

Para obtener ejemplos más detallados, vea:
> [!div class="nextstepaction"]
> [Escenarios de autenticación de Azure AD](../active-directory/develop/authentication-vs-authorization.md)

Descubra las métricas de uso de API de la cuenta de Azure Maps:
> [!div class="nextstepaction"]
> [Visualización de métricas de uso](how-to-view-api-usage.md)

Explore ejemplos acerca de cómo integrar Azure AD con Azure Maps:
> [!div class="nextstepaction"]
> [Ejemplos de Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
