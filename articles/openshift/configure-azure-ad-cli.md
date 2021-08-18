---
title: 'Red Hat OpenShift en Azure con OpenShift 4: configuración de la autenticación de Azure Active Directory mediante la línea de comandos'
description: Obtenga información sobre cómo configurar la autenticación de Azure Active Directory para un clúster de Red Hat OpenShift en Azure que ejecute OpenShift 4 mediante la línea de comandos.
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9571e7692a70d36155f395ec57bdedafe5970ac8
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369778"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configuración de la autenticación de Azure Active Directory para un clúster de Red Hat OpenShift 4 en Azure (CLI)

Si decide instalar y usar la CLI localmente, para este artículo es necesario que ejecute la versión CLI de Azure 2.6.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

Recupere las direcciones URL específicas del clúster que se van a usar para configurar la aplicación de Azure Active Directory.

Establezca las variables para el grupo de recursos y el nombre del clúster.

Reemplace **\<resource_group>** por el nombre del grupo de recursos y **\<aro_cluster>** por el nombre del clúster.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Construya la dirección URL de devolución de llamada de OAuth del clúster y almacénela en una variable **oauthCallbackURL**. 

> [!NOTE]
> La sección `AAD` de la dirección URL de devolución de llamada de OAuth debe coincidir con el nombre del proveedor de identidades de OAuth que se va a configurar más adelante.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

El formato de oauthCallbackURL es ligeramente diferente con dominios personalizados:

* Ejecute el siguiente comando si usa un dominio personalizado, por ejemplo, `contoso.com`. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Si no usa un dominio personalizado, `$domain` es una cadena alfanumérica de ocho caracteres que se extiende mediante `$location.aroapp.io`.

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> La sección `AAD` de la dirección URL de devolución de llamada de OAuth debe coincidir con el nombre del proveedor de identidades de OAuth que se va a configurar más adelante.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Creación de una aplicación de Azure Active Directory para autenticación

Reemplace **\<client_secret>** por una contraseña segura para la aplicación.

```azurecli-interactive
client_secret=<client_secret>
```

Cree una aplicación de Azure Active Directory y recupere el identificador de la aplicación creado.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Recupere el identificador de inquilino de la suscripción que posee la aplicación.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Creación de un archivo de manifiesto para definir las notificaciones opcionales que se van a incluir en el token de identificador

Los desarrolladores de aplicaciones pueden usar [notificaciones opcionales](../active-directory/develop/active-directory-optional-claims.md) en sus aplicaciones de Azure AD para especificar qué notificaciones desean incluir en los tokens que se envían a las aplicaciones.

Estas notificaciones opcionales sirven para:

- Seleccionar las notificaciones adicionales que se incluirán en los tokens para la aplicación.
- Cambiar el comportamiento de ciertas notificaciones que Azure AD devuelve en tokens.
- Agregar notificaciones personalizadas para la aplicación y acceder a ellas.

Se va a configurar OpenShift para usar la notificación `email` y revertir a `upn` para establecer el nombre de usuario preferido agregando `upn` como parte del token de identificador devuelto por Azure Active Directory.

Cree un archivo **manifest.json** para configurar la aplicación de Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Actualización de las notificaciones opcionales de la aplicación de Azure Active Directory con un manifiesto

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Actualización de los permisos de ámbito de las aplicaciones de Azure Active Directory

Para poder leer la información de usuario de Azure Active Directory, es necesario definir los ámbitos adecuados.

Agregue el permiso para el ámbito **Graph.User.Read de Azure Active Directory** para habilitar el inicio de sesión y leer el perfil de usuario.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> A menos que se autentique como administrador global de esta instancia de Azure Active Directory, puede omitir con seguridad el mensaje para otorgar el consentimiento. A los usuarios de dominio estándar se les pide que otorguen el consentimiento al iniciar sesión por primera vez en el clúster con las credenciales de AAD.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Asignación de usuarios y grupos al clúster (opcional)

Las aplicaciones registradas en un inquilino de Azure Active Directory (Azure AD) están disponibles de forma predeterminada para todos los usuarios del inquilino que se autentica correctamente. Azure AD permite a los administradores y desarrolladores de inquilinos restringir una aplicación a un conjunto específico de usuarios o a los grupos de seguridad del inquilino.

Siga las instrucciones de la documentación de Azure Active Directory para [asignar usuarios y grupos a la aplicación](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

## <a name="configure-openshift-openid-authentication"></a>Configuración de la autenticación OpenID en OpenShift

Recupere las credenciales de `kubeadmin`. Ejecute el siguiente comando para buscar la contraseña del usuario `kubeadmin`.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Inicie sesión en el servidor de API del clúster de OpenShift mediante el siguiente comando. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Cree un secreto de OpenShift para almacenar el secreto de aplicación de Azure Active Directory.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Cree un archivo **oidc.yaml** para configurar la autenticación OpenID de OpenShift en Azure Active Directory. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Aplique la configuración al clúster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Obtendrá una respuesta similar a la siguiente.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verificación del inicio de sesión mediante Azure Active Directory

Si ahora cierra sesión en la consola web de OpenShift y vuelve a iniciar sesión, se le presentará una nueva opción para iniciar sesión con **AAD**. Debe esperar unos minutos.

![Pantalla de inicio de sesión con la opción de Azure Active Directory](media/aro4-login-2.png)
