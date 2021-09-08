---
title: Autenticación y autorización
description: Obtenga información sobre las distintas formas en que puede autenticar una aplicación o servicio para Azure Spatial Anchors y los niveles de control para los que tiene que obtener acceso para Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp, subject-rbac-steps
ms.openlocfilehash: ef49c6ebd356c97466b9b5a744cc58584c42bcb7
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126039"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticación y autorización en Azure Spatial Anchors

En este artículo obtendrá información sobre las distintas formas de autenticarse en Azure Spatial Anchors desde su aplicación o servicio web. También podrá obtener detalles sobre las formas de usar el control de acceso basado en roles de Azure (RBAC de Azure) en Azure Active Directory (Azure AD) para controlar el acceso a las cuentas de Spatial Anchors.

## <a name="overview"></a>Información general

![Diagrama en el que se muestra una introducción a la autenticación en Azure Spatial Anchors.](./media/spatial-anchors-authentication-overview.png)

Para obtener acceso a una determinada cuenta de Azure Spatial Anchors, los clientes deben obtener primero un token de acceso del servicio de token de seguridad (STS) de Azure Mixed Reality. Los tokens obtenidos de STS tienen una duración de 24 horas. Estos incluyen información de los servicios de Spatial Anchors que se usan para tomar decisiones de autorización acerca de la cuenta y asegurarse de que solo las entidades de seguridad autorizadas pueden tener acceso a esta.

Los tokens de acceso pueden obtenerse a cambio de claves de cuenta o los tokens que haya emitido Azure AD.

Las claves de cuenta le permiten empezar a trabajar rápidamente con el servicio de Azure Spatial Anchors. Sin embargo, antes de implementar la aplicación en la producción, es recomendable actualizarla para que pueda usar la autenticación de Azure AD.

Los tokens de autenticación de Azure AD pueden obtenerse de dos maneras:

- Si va a compilar una aplicación empresarial y su empresa usa Azure AD como sistema de identidades, puede usar en la aplicación la autenticación de Azure AD basada en el usuario. A continuación, debe conceder acceso a sus cuentas de Spatial Anchors usando los grupos de seguridad de Azure AD existentes. Igualmente, también puede conceder acceso directamente a los usuarios de la organización.
- En caso contrario, es recomendable que obtenga los tokens de Azure AD de un servicio web que sea compatible con la aplicación. Se recomienda que use este método en las aplicaciones de producción, ya que le permite evitar la inserción de credenciales para obtener acceso a Azure Spatial Anchors en la aplicación cliente.

## <a name="account-keys"></a>Claves de cuenta

La manera más sencilla de empezar es usar claves de cuenta para obtener acceso a la cuenta de Azure Spatial Anchors. Puede obtener las claves de cuenta en Azure Portal. Vaya a la cuenta y seleccione la pestaña **Claves**:

![Captura de pantalla en la que se muestra la pestaña "Claves" con el botón "Copiar" de la clave principal resaltado.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Hay disponibles dos claves. Ambas son válidas simultáneamente para obtener acceso a la cuenta de Spatial Anchors. Recuerde que es recomendable actualizar periódicamente la clave que use para acceder a la cuenta. Si cuenta con dos claves válidas independientes, podrá realizar las actualizaciones necesarias sin que se produzca tiempo de inactividad. Solo tiene que actualizar la clave principal y la clave secundaria de forma alternativa.

El SDK tiene compatibilidad integrada para realizar la autenticación a través de claves de cuenta. Solo tiene que establecer la propiedad `AccountKey` en el objeto `cloudSession`:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Una vez hecho esto, el SDK controlará el intercambio de la clave de cuenta para un token de acceso, así como el almacenamiento en caché necesario de tokens de la aplicación.

> [!WARNING]
> Se recomienda usar claves de cuenta para la incorporación rápida, pero solo durante el desarrollo o la creación de prototipos. Asimismo, no es recomendable que envíe su aplicación a producción con una clave de cuenta insertada en ella. En su lugar, use los métodos de autenticación de Azure AD basados en el usuario o en el servicio que se describen a continuación.

## <a name="azure-ad-user-authentication"></a>Autenticación de usuario de Azure AD

En el caso de las aplicaciones que tienen como destino los usuarios de Azure Active Directory, se recomienda usar un token de Azure AD para el usuario. Puede obtener este token mediante el [MSAL](../../active-directory/develop/msal-overview.md). Siga los pasos descritos en la [guía de inicio rápido sobre el registro de una aplicación](../../active-directory/develop/quickstart-register-app.md), que incluye:

**En Azure Portal**
1.    Registre la aplicación en Azure AD como una aplicación nativa. Como parte del registro, deberá determinar si la aplicación debe ser multiinquilino. También debe proporcionar las direcciones URL de redireccionamiento permitidas en la aplicación.
1.  Vaya a la pestaña **Permisos de API**.
1.  Seleccione **Agregar un permiso**.
    1.  Seleccione **Mixed Reality Resource Provider** (Proveedor de recursos de realidad mixta) en la pestaña **API usadas en mi organización**.
    2.  Seleccione **Permisos delegados**.
    3.  Seleccione **mixedreality.signin** en **mixedreality**.
    4.  Seleccione **Agregar permisos**.
1.  Seleccione **Conceder consentimiento de administrador**.

1. Asigne un [rol RBAC de ASA](#azure-role-based-access-control) a la aplicación o a los usuarios a los que quiera conceder acceso al recurso. Si quiere que los usuarios de la aplicación tengan distintos roles en la cuenta de ASA, registre varias aplicaciones en Azure AD y asigne un rol independiente a cada una. A continuación, implemente la lógica de autorización para usar el rol correcto para los usuarios. Para obtener los pasos de asignación de roles detallados, vea [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

**En el código**
1.    Asegúrese de usar el id. de aplicación y el URI de redirección de la propia aplicación de Azure AD como parámetros de **Id. de cliente** y **RedirectUri** de MSAL.
2.    Establezca la información del inquilino:
        1.    Si la aplicación admite **Solo mi organización**, reemplace este valor por el **id. de inquilino** o el **nombre de inquilino**. Por ejemplo, contoso.microsoft.com.
        2.    Si la aplicación admite las **cuentas de cualquier directorio organizativo**, reemplace este valor por **Organizations**.
        3.    Si la aplicación admite **Todos los usuarios de cuentas Microsoft**, reemplace este valor por **Común**.
3.    En la solicitud de token, establezca el **ámbito** en **"`https://sts.<account-domain>//.default`"** , donde `<account-domain>` se reemplaza por el **dominio de la cuenta** de Azure Spatial Anchors. Un ámbito de ejemplo de una cuenta de Azure Spatial Anchors en el dominio de la cuenta del este de EE. UU. 2 es **"`https://sts.mixedreality.azure.com//.default`"** . Este ámbito indicará a Azure AD que la aplicación solicita un token para el servicio de token de seguridad (STS) de Mixed Reality.

Después de completar estos pasos, la aplicación debe poder obtener desde MSAL un token de Azure AD. Puede establecer ese token de Azure AD como `authenticationToken` en el objeto de configuración de sesiones en la nube:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticación de servicio de Azure AD

Para implementar aplicaciones que Azure Spatial Anchors en la producción, se recomienda usar un servicio de back-end que gestione las solicitudes de autenticación. A continuación se muestra una información general acerca del proceso:

![Diagrama en el que se muestra una introducción a la autenticación en Azure Spatial Anchors.](./media/spatial-anchors-aad-authentication.png)

En este caso, se supone que la aplicación usa su propio mecanismo para autenticarse en su servicio back-end. (Por ejemplo, un cuenta de Microsoft, PlayFab, Facebook, un id. de Google o un nombre de usuario y una contraseña personalizados).  Una vez que los usuarios se autentican en el servicio de back-end, dicho servicio puede recuperar un token de Azure AD, cambiarlo por un token de acceso para Azure Spatial Anchors y devolverlo a la aplicación cliente.

El token de acceso de Azure AD se recupera mediante [MSAL](../../active-directory/develop/msal-overview.md). Siga los pasos que se indican en la [guía de inicio rápido para registrar una aplicación](../../active-directory/develop/quickstart-register-app.md), que incluyen:

**En Azure Portal**
1.    Registre la aplicación en Azure AD:
        1.    En Azure Portal, seleccione **Azure Active Directory** y, a continuación, **Registros de aplicaciones**.
        2.    Seleccione **Nuevo registro**.
        3.    Escriba el nombre de la aplicación, seleccione **Aplicación web o API** como tipo de aplicación y escriba la dirección URL de autenticación para el servicio. Seleccione **Crear**.
1.    En la aplicación, seleccione **Configuración** y, a continuación, seleccione la pestaña **Certificates and secrets** (Certificados y secretos). Cree un nuevo secreto de cliente, seleccione una duración y presione **Agregar**. Asegúrese de guardar el valor del secreto. Deberá incluirlo en el código del servicio web.
1. Asigne un [rol RBAC de ASA](#azure-role-based-access-control) a la aplicación o a los usuarios a los que quiera conceder acceso al recurso. Si quiere que los usuarios de la aplicación tengan distintos roles en la cuenta de ASA, registre varias aplicaciones en Azure AD y asigne un rol independiente a cada una. A continuación, implemente la lógica de autorización para usar el rol correcto para los usuarios. Para obtener los pasos de asignación de roles detallados, vea [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

**En el código**

>[!NOTE]
> Puede usar el [ejemplo de servicio](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample) que está disponible como parte de las [aplicaciones de ejemplo de Spatial Anchors](https://github.com/Azure/azure-spatial-anchors-samples).

1.    Asegúrese de usar el id. de aplicación, el secreto de aplicación y el URI de redirección de la propia aplicación de Azure AD como parámetros de **id. de cliente**, **secreto** y **RedirectUri** de MSAL.
2.    Establezca el id. de inquilino en su propio id. de inquilino de Azure AD en el parámetro de **autoridad** de MSAL.
3.    En la solicitud de token, establezca el **ámbito** en **"`https://sts.<account-domain>//.default`"** , donde `<account-domain>` se reemplaza por el **dominio de la cuenta** de Azure Spatial Anchors. Un ámbito de ejemplo de una cuenta de Azure Spatial Anchors en el dominio de la cuenta del este de EE. UU. 2 es **"`https://sts.mixedreality.azure.com//.default`"** .

Después de completar estos pasos, el servicio de back-end puede recuperar un token de Azure AD. A continuación, puede cambiarlo por un token de MR que va a devolver al cliente. El uso de un token de Azure AD para recuperar un token de MR se realiza a través de una llamada de REST. A continuación se muestra una llamada de ejemplo:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

El encabezado de autorización tiene el formato siguiente: `Bearer <Azure_AD_token>`.

La respuesta contiene el token de MR en texto sin formato.

Ese token de MR se devuelve después al cliente. La aplicación cliente, a continuación, puede establecerlo como su token de acceso en la configuración de la sesión en la nube:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Control de acceso basado en roles de Azure

Para ayudarle a controlar el nivel de acceso concedido a las aplicaciones, los servicios o los usuarios de Azure AD del servicio, puede asignar estos roles preexistentes según sea necesario en las cuentas de Azure Spatial Anchors:

- **Propietario de la cuenta de Spatial Anchors**. Las aplicaciones o los usuarios que tienen este rol pueden crear anclajes espaciales, consultarlos y eliminarlos. Al autenticarse en la cuenta mediante las claves de cuenta, el rol de propietario de cuenta de Spatial Anchors se asigna a la entidad de seguridad autenticada.
- **Colaborador de la cuenta de Spatial Anchors**. Las aplicaciones o los usuarios que tienen este rol pueden crear anclajes espaciales y consultarlos, pero no pueden eliminarlos.
- **Lector de la cuenta de Spatial Anchors**. Las aplicaciones o los usuarios que tienen este rol solo pueden consultar los anclajes espaciales. No pueden crear otros nuevos, eliminar los existentes ni actualizar sus metadatos. Este rol se usa normalmente para aplicaciones en las que algunos usuarios ajustan el entorno, mientras que otros solo pueden recuperar los anclajes colocados anteriormente en ese entorno.

## <a name="next-steps"></a>Pasos siguientes

Cree su primera aplicación con Azure Spatial Anchors:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
