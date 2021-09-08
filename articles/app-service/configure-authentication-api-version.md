---
title: Administración de versiones de API de AuthN/AuthZ
description: Actualice la API de autenticación de App Service a la versión V2 o ánclela a una versión específica, si es necesario.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e526f4a56806fa41d8955337d7e8463afac80b21
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113095322"
---
# <a name="manage-the-api-and-runtime-versions-of-app-service-authentication"></a>Administración de las versiones de la API y el entorno de ejecución de la autenticación de App Service

En este artículo, se muestra cómo personalizar las versiones de la API y el entorno de ejecución de la [autenticación y autorización integradas en App Service](overview-authentication-authorization.md).

Hay dos versiones de la API de administración para la autenticación de App Service. La versión V2 es necesaria para la experiencia "Autenticación" en Azure Portal. Una aplicación que ya use la API V1 puede actualizarse a la versión V2 una vez realizados algunos cambios. En concreto, la configuración de secretos debe moverse a una configuración de la aplicación con espacios fijos. Puede hacerse automáticamente desde la sección "Autenticación" del portal de la aplicación.

## <a name="update-the-configuration-version"></a>Actualización de la versión de configuración

> [!WARNING]
> La migración a V2 deshabilita la administración de la característica Autenticación o Autorización de App Service de la aplicación en algunos clientes, como su experiencia existente en Azure Portal, la CLI de Azure y Azure PowerShell. Esto no se puede revertir.

La API de V2 no admite la creación ni edición de una cuenta Microsoft como proveedor distintivo, como se hacía en V1, sino que aprovecha la [Plataforma de identidad de Microsoft](../active-directory/develop/v2-overview.md) convergente para que los usuarios inicien sesión con Azure AD y cuentas personales de Microsoft. Al cambiar a la API V2, se usa la configuración V1 de Azure Active Directory para configurar el proveedor de la Plataforma de identidad de Microsoft. El proveedor de la cuenta Microsoft V1 se traslada durante el proceso de migración y sigue funcionando de la manera habitual, aunque se recomienda pasar al modelo más reciente de la Plataforma de identidad de Microsoft. Vea [Compatibilidad con los registros del proveedor de cuentas Microsoft](#support-for-microsoft-account-provider-registrations) para obtener más información.

El proceso de migración automatizado mueve los secretos del proveedor a la configuración de la aplicación y luego convierte el resto de la configuración al nuevo formato. Para usar la migración automática:

1. Vaya a la aplicación en el portal y seleccione la opción de menú **Autenticación**.
1. Si la aplicación está configurada mediante el modelo V1, se ve un botón **Actualizar**.
1. Revise la descripción del mensaje de confirmación. Si está listo para realizar la migración, haga clic en **Actualizar** en el mensaje.

### <a name="manually-managing-the-migration"></a>Administración manual de la migración

Los pasos siguientes permiten migrar manualmente la aplicación a la API de V2 si no quiere usar la versión automática mencionada anteriormente.

#### <a name="moving-secrets-to-application-settings"></a>Movimiento de secretos a la configuración de la aplicación

1. Obtenga la configuración existente mediante la API V1:

   ```azurecli
   az webapp auth show -g <group_name> -n <site_name>
   ```

   En la carga JSON resultante, anote el valor secreto que se usa para cada proveedor que ha configurado:

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Cuenta de Microsoft: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Los valores de secreto son credenciales de seguridad importantes y deben administrarse con cuidado. No comparta estos valores ni los conserve en una máquina local.

1. Cree la configuración de la aplicación con espacios fijos para cada valor de secreto. Puede elegir el nombre de cada configuración de la aplicación. Su valor debe coincidir con lo que se obtuvo en el paso anterior o [hacer referencia a un secreto de Key Vault](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) que haya creado con ese valor.

   Para crear la configuración, puede usar Azure Portal o ejecutar una variación del código siguiente para cada proveedor:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > La configuración de la aplicación para esta configuración debe marcarse como con espacios fijos, lo que significa que no se moverán entre entornos durante una [operación de intercambio de espacios](./deploy-staging-slots.md). Esto se debe a que la configuración de autenticación en sí está asociada al entorno. 

1. Cree un nuevo archivo JSON denominado `authsettings.json`. Tome el resultado que recibió previamente y quite de él cada valor de secreto. Escriba la salida restante en el archivo, asegurándose de que no se incluya ningún secreto. En algunos casos, la configuración puede tener matrices que contengan cadenas vacías. Asegúrese de que `microsoftAccountOAuthScopes` no lo haga y, si es así, cambie ese valor a `null`.

1. Agregue una propiedad a `authsettings.json` que apunte al nombre de la configuración de la aplicación que creó anteriormente para cada proveedor:
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Cuenta de Microsoft: `microsoftAccountClientSecretSettingName`

   Un archivo de ejemplo después de esta operación podría tener un aspecto similar al siguiente, en este caso solo está configurado para AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Envíe este archivo como la nueva configuración de Autenticación / Autorización para la aplicación:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Valide que la aplicación siga funcionando según lo esperado después de este gesto.

1. Elimine el archivo usado en los pasos anteriores.

Ha migrado la aplicación para almacenar los secretos del proveedor de identidades como configuración de la aplicación.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Compatibilidad con los registros del proveedor de cuentas Microsoft

Si la configuración existente contiene un proveedor Cuentas de Microsoft y no contiene un proveedor Azure Active Directory, puede cambiar la configuración al proveedor Azure Active Directory y luego realizar la migración. Para ello, siga estos pasos:

1. Vaya a [**Registros de aplicaciones**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) en Azure Portal y busque el registro asociado a su proveedor Cuentas de Microsoft. Puede estar bajo el encabezado "Aplicaciones de cuenta personal".
1. Vaya a la página "Autenticación" para el registro. En "URI de redireccionamiento" debería ver una entrada que termina en `/.auth/login/microsoftaccount/callback`. Copie este URI.
1. Agregue un nuevo URI que coincida con el que acaba de copiar, salvo que en su lugar termine en `/.auth/login/aad/callback`. Esto permitirá que el registro se use en la configuración de Autenticación / Autorización de App Service.
1. Vaya a la configuración de Autenticación / Autorización de App Service para la aplicación.
1. Recopile la configuración para el proveedor Cuenta de Microsoft.
1. Configure el proveedor Azure Active Directory mediante el modo de administración "Avanzado", proporcionando los valores de identificador de cliente y secreto de cliente que recopiló en el paso anterior. Para la URL del emisor, use `<authentication-endpoint>/<tenant-id>/v2.0` y reemplace *\<authentication-endpoint>* por el [punto de conexión de autenticación del entorno de nube](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (por ejemplo, "https://login.microsoftonline.com" para Azure global), y reemplace también *\<tenant-id>* por el **identificador de directorio (inquilino)** .
1. Una vez que haya guardado la configuración, pruebe el flujo de inicio de sesión; para ello, vaya en al explorador hasta el punto de conexión `/.auth/login/aad` del sitio y complete el flujo de inicio de sesión.
1. En este punto, ha copiado correctamente la configuración, pero la configuración del proveedor Cuenta de Microsoft existente permanece. Antes de quitarla, asegúrese de que todas las partes de la aplicación hagan referencia al proveedor Azure Active Directory a través de los vínculos de inicio de sesión, etc. Compruebe que todas las partes de la aplicación funcionen según lo previsto.
1. Una vez que haya validado que todo funciona con el proveedor Azure Active Directory de AAD, puede quitar la configuración del proveedor Cuentas de Microsoft.

> [!WARNING]
> Es posible converger los dos registros modificando los [tipos de cuenta admitidos](../active-directory/develop/supported-accounts-validation.md) para el registro de la aplicación de AAD. Sin embargo, esto forzaría una nueva solicitud de consentimiento para los usuarios de Cuenta de Microsoft, y las notificaciones de identidad de esos usuarios pueden ser diferentes en la estructura, en especial `sub` cambiará los valores dado que se usa un nuevo identificador de aplicación. No se recomienda este enfoque a menos que se entienda cabalmente. En su lugar, debe esperar a que se admitan los dos registros en la superficie de la API V2.

#### <a name="switching-to-v2"></a>Cambio a V2

Una vez seguidos los pasos anteriores, vaya a la aplicación en Azure Portal. Seleccione la sección "Autenticación (versión preliminar)". 

Como alternativa, puede realizar una solicitud PUT en el recurso de `config/authsettingsv2` en el recurso del sitio. El esquema de la carga es el mismo que el capturado en la [configuración basada en archivos](configure-authentication-file-based.md).

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Anclaje de la aplicación a una versión específica en tiempo de ejecución de autenticación

Al habilitar la autenticación y la autorización, el middleware de la plataforma se inserta en la canalización de solicitudes HTTP, tal y como se describe en la [información general sobre las características](overview-authentication-authorization.md#how-it-works). Este middleware de la plataforma se actualiza periódicamente con nuevas características y mejoras, como parte de las actualizaciones habituales de la plataforma. La aplicación web o de funciones se ejecutará de forma predeterminada en la versión más reciente del middleware de la plataforma. Estas actualizaciones automáticas siempre son compatibles con las versiones anteriores. Sin embargo, en el caso excepcional de que la actualización automática provoque algún problema en tiempo de ejecución a la aplicación web o de funciones, se puede revertir temporalmente a la versión anterior del middleware. En este artículo se explica cómo anclar temporalmente una aplicación a una versión específica del middleware de autenticación.

### <a name="automatic-and-manual-version-updates"></a>Actualizaciones de versiones automáticas y manuales 

Para anclar la aplicación a una versión específica del middleware de la plataforma, establezca el valor `runtimeVersion` para la aplicación. La aplicación siempre se ejecuta en la versión más reciente, a menos que decida volver a anclarla explícitamente a una versión específica. El número de versiones admitidas a la vez es reducido. Si ancla la aplicación a una versión que ya no se admite, la aplicación usará la versión más reciente. Para ejecutar siempre la versión más reciente, establezca `runtimeVersion` en ~1. 

### <a name="view-and-update-the-current-runtime-version"></a>Visualización y actualización de la versión actual del entorno de ejecución

Puede cambiar la versión en tiempo de ejecución utilizada por la aplicación. La nueva versión en tiempo de ejecución surtirá efecto después de reiniciar la aplicación. 

#### <a name="view-the-current-runtime-version"></a>Visualización de la versión actual del runtime

Para ver la versión actual del middleware de autenticación de la plataforma, utilice la CLI de Azure o uno de los puntos de conexión HTTP de la versión integrada en la aplicación.

##### <a name="from-the-azure-cli"></a>Desde la CLI de Azure

En la CLI de Azure, consulte la versión actual del middleware mediante el comando [az webapp auth show](/cli/azure/webapp/auth#az_webapp_auth_show).

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

En el código, reemplace `<my_app_name>` por el nombre de la aplicación. Reemplace también `<my_resource_group>` por el nombre del grupo de recursos para la aplicación.

Verá el campo `runtimeVersion` en la salida de la CLI. Será similar al siguiente ejemplo de salida, que se ha abreviado para mayor claridad: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>En el punto de conexión de la versión

También puede consultar el punto de conexión de /.auth/version en una aplicación para ver la versión actual del middleware en el que se ejecuta la aplicación. Será similar al siguiente ejemplo de salida:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Actualización de la versión de tiempo de ejecución

En la CLI de Azure, puede actualizar el valor de `runtimeVersion` en la aplicación mediante el comando [az webapp auth update](/cli/azure/webapp/auth#az_webapp_auth_update).

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Reemplace `<my_app_name>` por el nombre de la aplicación. Reemplace también `<my_resource_group>` por el nombre del grupo de recursos para la aplicación. Por último, reemplace `<version>` por una versión válida del tiempo de ejecución 1.x o `~1` en el caso de la versión más reciente. Consulte las [notas de la versión de las diferentes versiones del entorno de ejecución](https://github.com/Azure/app-service-announcements) para ayudar a determinar la versión a la quiere realizar el anclaje.

Este comando se puede ejecutar desde [Azure Cloud Shell](../cloud-shell/overview.md), para lo que es preciso hacer clic en **Pruébelo** en el código de ejemplo anterior. También puede usar la [CLI de Azure localmente](/cli/azure/install-azure-cli) para ejecutar este comando después de ejecutar [az login](/cli/azure/reference-index#az_login) para iniciar sesión.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Autenticación y autorización de usuarios de un extremo a otro](tutorial-auth-aad.md)
