---
title: 'Tutorial: asignación de roles de Azure Static Web Apps con Microsoft Graph'
description: Aprenda a usar una función sin servidor para asignar roles de usuario personalizados basados en pertenencia a grupos Active Directory.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: cshoe
keywords: autorización de aplicaciones web estáticas, asignación de roles de usuario, roles personalizados
ms.openlocfilehash: bf9089f7e645dd32bbfd5f30041f76f71a04a5ec
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858836"
---
# <a name="tutorial-assign-custom-roles-with-a-function-and-microsoft-graph"></a>Tutorial: asignación de roles personalizados con una función y Microsoft Graph

En este artículo se muestra cómo usar una función para consultar [Microsoft Graph](https://developer.microsoft.com/graph) y asignar roles personalizados a un usuario en función de su pertenencia a grupos Active Directory.

En este tutorial, aprenderá a:

- Implementación de una aplicación web estática.
- Creación de un registro de aplicaciones de Azure Active Directory.
- Configuración de autenticación personalizada con Azure Active Directory.
- Configure una [función sin servidor](authentication-authorization.md?tabs=function#role-management) que consulte la pertenencia a grupos Active Directory del usuario y devuelva una lista de roles personalizados.

> [!NOTE]
> En este tutorial es necesario [usar una función para asignar roles](authentication-authorization.md?tabs=function#role-management). La administración de roles basada en funciones está actualmente en versión preliminar.

## <a name="prerequisites"></a>Prerrequisitos

- Una **cuenta activa de Azure**: en caso de no tener una, puede [crear una cuenta gratuita](https://azure.microsoft.com/free/).
- El usuario debe tener suficientes permisos para crear una aplicación de Azure Active Directory.

## <a name="create-a-github-repository"></a>Crear un repositorio de GitHub

1. Vaya a la siguiente ubicación para crear un repositorio:
    - [https://github.com/staticwebdev/roles-function/generate](https://github.com/login?return_to=/staticwebdev/roles-function/generate)

1. Asigne el nombre **my-custom-roles-app** al repositorio.

1. Seleccione **Create repository from template** (Crear repositorio a partir de plantilla).

## <a name="deploy-the-static-web-app-to-azure"></a>Implementación de la aplicación web estática en Azure

1. Desde una ventana nueva del explorador, vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

1. Haga clic en **Crear un recurso** en la esquina superior izquierda.

1. Escriba **static web apps** en el cuadro de búsqueda.

1. Seleccione **Static Web App**.

1. Seleccione **Crear**.

1. Configure la instancia de Azure Static Web Apps con la siguiente información:

    | **Entrada** | **Valor** | **Notas** |
    | ---------- | ---------- | ---------- |
    | _Suscripción_ | Selección de su suscripción a Azure | |
    | _Grupos de recursos_ | Cree uno nuevo denominado **my-custom-roles-app-group** | |
    | _Nombre_ | **my-custom-roles-app**  | |
    | _Plan de hospedaje_ | **Estándar** | La personalización de la autenticación y la asignación de roles mediante una función requieren el plan Estándar |
    | _Región_ | Seleccione la región más cercana a la suya | |
    | _Detalles de implementación_ | Seleccione **GitHub** como origen | |

1. Seleccione el botón **Sign-in with GitHub** (Iniciar sesión con GitHub) y autentíquese con GitHub.

1. Seleccione el nombre de la _Organización_ en la que ha creado el repositorio.

1. Seleccione **my-custom-roles-app** en el menú desplegable _Repositorio_.

1. Seleccione **main** en el menú desplegable _Rama_.

1. En la sección _Detalles de la compilación_, agregue información de configuración específica de esta aplicación.

    | **Entrada** | **Valor** | **Notas** |
    | ---------- | ---------- | ---------- |
    | _Valores preestablecidos de compilación_ | **Personalizada** | |
    | _Ubicación de la aplicación_ | **Front-end** | Carpeta del repositorio que contiene la aplicación |
    | _Ubicación de la API_ | **api** | Carpeta del repositorio que contiene la API |
    | _Ubicación de salida_ | | Esta aplicación no tiene salida de compilación |

1. Seleccione **Revisar + crear**. A continuación, seleccione **Crear** para crear la aplicación web estática e iniciar la primera implementación.

1. Seleccione **Ir al recurso** para abrir la nueva aplicación estática.

1. En la sección de información general, busque la **dirección URL** de la aplicación. Copie este valor en un editor de texto, ya que necesitará esta dirección URL para configurar la autenticación de Active Directory y probar la aplicación.

## <a name="create-an-azure-active-directory-application"></a>Creación de una aplicación de Azure Active Directory

1. En Azure Portal, busque y vaya a *Azure Active Directory*.

1. En la barra del menú, seleccione **Registros de aplicaciones**.

1. Seleccione **Nuevo registro** para abrir la página *Registrar una aplicación*

1. Escriba un nombre para la aplicación. Por ejemplo, **MyStaticWebApp**.

1. Para la opción *Tipos de cuenta admitidos*, seleccione **Solo las cuentas de este directorio organizativo**.

1. En *URI de redirección*, seleccione **Web** y escriba la [devolución de llamada de autenticación](authentication-custom.md#authentication-callbacks) de inicio de sesión de Azure Active Directory de la aplicación web estática. Por ejemplo, `<YOUR_SITE_URL>/.auth/login/aad/callback`.

    Reemplace `<YOUR_SITE_URL>` por la dirección URL de la aplicación web estática.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-app-registration.png" alt-text="Crear un registro de aplicación":::

1. Seleccione **Registrar**.

1. Una vez creado el registro de la aplicación, copie el **Id. de aplicación (cliente)** y el **Id. de directorio (inquilino)** en la sección *Essentials* de un editor de texto. Necesitará estos valores para configurar la autenticación de Active Directory en la aplicación web estática.

### <a name="enable-id-tokens"></a>Habilitación de tokens de identificador

1. En la barra del menú, seleccione *Autenticación*.

1. En la sección *Flujos de concesión implícita e híbridos*, seleccione **Tokens de id. (utilizados para flujos implícitos e híbridos)** .

    :::image type="content" source="media/assign-roles-microsoft-graph/enable-id-tokens.png" alt-text="Habilitación de tokens de identificador":::
    
    Esta configuración la requiere Static Web Apps para autenticar a los usuarios.

1. Seleccione **Guardar**.

### <a name="create-a-client-secret"></a>Creación de un secreto de cliente

1. Seleccione *Certificados y secretos* en la barra del menú.

1. En la sección *Secretos de cliente*, seleccione **Nuevo secreto de cliente**.

1. Escriba un nombre para el secreto de cliente. Por ejemplo, **MyStaticWebApp**.

1. Deje el valor predeterminado de _6 meses_ para el campo *Expiración*.

    > [!NOTE]
    > Debe rotar el secreto antes de la fecha de expiración generando un nuevo secreto y actualizando la aplicación con su valor.

1. Seleccione **Agregar**.

1. Anote el **valor** del secreto de cliente que ha creado. Necesitará este valor para configurar la autenticación de Active Directory en la aplicación web estática.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-client-secret.png" alt-text="Creación de un secreto de cliente":::

## <a name="configure-active-directory-authentication"></a>Configuración de la autenticación de Active Directory

1. En un explorador, abra el repositorio GitHub que contiene la aplicación web estática que implementó. Vaya al archivo de configuración de la aplicación en *frontend/staticwebapp.config.json*. Incluye las secciones siguientes:

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
              "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

    > [!NOTE]
    > Para obtener un token de acceso para Microsoft Graph, el campo `loginParameters` debe configurarse con `resource=https://graph.microsoft.com`.

1. Seleccione el botón **Editar** para actualizar el archivo.

1. Actualice el valor *openIdIssuer* de `https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>` reemplazando`<YOUR_AAD_TENANT_ID>` por el id. de directorio (inquilino) de Azure Active Directory.

1. Seleccione **Confirmar directamente en la rama principal** y seleccione **Confirmar cambios**.

1. La ejecución de Acciones de GitHub se desencadena para actualizar la aplicación web estática.

1. Vaya al recurso de la aplicación web estática en Azure Portal.

1. En la barra del menú, seleccione **Configuración**.

1. En la sección *Configuración de la aplicación*, agregue la siguiente configuración:

    | Nombre | Valor |
    |------|-------|
    | `AAD_CLIENT_ID` | *El id. de cliente de la aplicación de Azure Active Directory* |
    | `AAD_CLIENT_SECRET` | *El valor del secreto de cliente de la aplicación de Active Directory* |

1. Seleccione **Guardar**.

## <a name="verify-custom-roles"></a>Verificación de roles personalizados

La aplicación de ejemplo contiene una función sin servidor (*api/GetRoles/index.js*) que consulta Microsoft Graph para determinar si un usuario está en un grupo definido previamente. En función de las pertenencias a grupos del usuario, la función asigna roles personalizados al usuario. La aplicación está configurada para restringir determinadas rutas en función de estos roles personalizados.

1. En el repositorio de GitHub, vaya a la función *GetRoles* que se encuentra en *api/GetRoles/index.js*. Cerca de la parte superior, hay un objeto `roleGroupMappings` que asigna roles de usuario personalizados a grupos de Azure Active Directory.

1. Haz clic en el botón **Editar**.

1. Actualice el objeto con los id. de grupo del inquilino de Azure Active Directory.

    Por ejemplo, si tiene grupos con los id. `6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` y `b6059db5-9cef-4b27-9434-bb793aa31805`, actualizaría el objeto a:

    ```js
    const roleGroupMappings = {
      'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
      'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    Se llama a la función *GetRoles* cada vez que un usuario se autentica correctamente con Azure Active Directory. La función usa el token de acceso del usuario para consultar su pertenencia al grupo Active Directory de Microsoft Graph. Si el usuario es miembro de cualquier grupo definido en el objeto `roleGroupMappings`, la función devuelve los roles personalizados correspondientes.
    
    En el ejemplo anterior, si un usuario es miembro del grupo de Active Directory con el identificador `b6059db5-9cef-4b27-9434-bb793aa31805`, se le concede el rol `reader`.

1. Seleccione **Confirmar directamente en la rama principal** y seleccione **Confirmar cambios**.

1. La ejecución de Acciones de GitHub se desencadena para actualizar la aplicación web estática.

1. Una vez completada la implementación, puede verificar los cambios; para ello, vaya a la dirección URL de la aplicación.

1. Inicie sesión en la aplicación web estática mediante Azure Active Directory.

1. Cuando haya iniciado sesión, la aplicación de ejemplo muestra la lista de roles que se le asignan en función de la pertenencia a grupos de Active Directory de su identidad. En función de estos roles, se le permite o prohíbe acceder a algunas de las rutas de la aplicación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Limpie los recursos que implementó eliminando el grupo de recursos.

1. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda.

1. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.

1. Seleccione el nombre del grupo de recursos que ha utilizado en este tutorial.

1. Seleccione **Eliminar grupo de recursos** del menú superior.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Autenticación y autorización](./authentication-authorization.md)
