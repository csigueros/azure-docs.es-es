---
title: Configuración del registro de contenedor integrado para Red Hat OpenShift 4 en Azure
description: Configuración del registro de contenedor integrado para Red Hat OpenShift 4 en Azure
author: majguo
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: d1eec47a7801975b3974ea172bd2cfbdc5ea0901
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214783"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configuración del registro de contenedor integrado para Red Hat OpenShift 4 en Azure

Red Hat OpenShift en Azure proporciona un registro de imágenes de contenedor integrado llamado [OpenShift Container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) que agrega la capacidad de aprovisionar automáticamente nuevos repositorios de imágenes a petición. Esto proporciona a los usuarios una ubicación integrada para que las compilaciones de la aplicación inserten las imágenes resultantes.

En este artículo, configurará el registro de imágenes de contenedor integrado para un clúster de Red Hat OpenShift en Azure (ARO) 4. Aprenderá a:

> [!div class="checklist"]
> * Configuración de Azure AD
> * Configurar OpenID Connect
> * Acceder al registro de imágenes de contenedor integrado

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de ARO. Si necesita un clúster de ARO, consulte el tutorial de ARO, [Creación de un clúster de Red Hat OpenShift en Azure 4](./tutorial-create-cluster.md). Asegúrese de crear el clúster con el argumento `--pull-secret` en `az aro create`.  Esto es necesario para configurar la autenticación de Azure Active Directory y el registro de contenedor integrado.

Cuando tenga el clúster, conéctese al mismo mediante los pasos que aparecen en [Conexión a un clúster de Red Hat OpenShift en Azure 4](./tutorial-connect-cluster.md).
   * Asegúrese de seguir los pasos que aparecen en "Instalación de la CLI de OpenShift", porque usaremos el comando `oc` más adelante en este artículo.
   * Anote la dirección URL de la consola del clúster, que tiene este aspecto: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`. Los valores de `<random>` y `<region>` se usarán más adelante en este artículo.
   * Anote las credenciales de `kubeadmin`. Las usará más adelante en este artículo.

### <a name="configure-azure-active-directory-authentication"></a>Configuración de la autenticación de Azure Active Directory 

Azure Active Directory (Azure AD) implementa OpenID Connect (OIDC). OIDC le permite usar Azure AD para iniciar sesión en el clúster de ARO. Siga los pasos descritos en [Configuración de la autenticación de Azure Active Directory](configure-azure-ad-cli.md) para configurar el clúster.

## <a name="access-the-built-in-container-image-registry"></a>Acceder al registro de imágenes de contenedor integrado

Ahora que ha configurado los métodos de autenticación en el clúster de ARO, habilitaremos el acceso al registro integrado.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definición del usuario de Azure AD como administrador

1. Inicie sesión en la consola web de OpenShift desde el explorador con las credenciales de un usuario de Azure AD. Podrá aprovechar la autenticación de OpenID de OpenShift en Azure Active Directory para usar OpenID con el fin de definir el administrador.

   1. Use InPrivate, Incógnito u otra característica de ventana del explorador equivalente para iniciar sesión en la consola. La ventana se verá distinta después de habilitar OIDC.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Ventana de inicio de sesión habilitada para OpenID Connect.":::
   1. Seleccione **openid**.

   > [!NOTE]
   > Anote el nombre de usuario y la contraseña que usa para iniciar sesión aquí. Este nombre de usuario y contraseña funcionarán como administrador para otras acciones en este y en otros artículos.
2. Siga estos pasos para iniciar sesión con la CLI de OpenShift.  Para el análisis, este proceso se conoce como `oc login`.
   1. En la parte superior derecha de la consola web, expanda el menú contextual del usuario que inició sesión y, luego, seleccione **Copy Login Command** (Copiar comando de inicio de sesión).
   2. Si es necesario, inicie sesión en una pestaña nueva con el mismo usuario.
   3. Seleccione **Mostrar token**.
   4. Copie el valor que aparece debajo de **Iniciar sesión con este token** en el portapapeles y ejecútelo en un shell, como se muestra aquí.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Ejecute `oc whoami` en la consola y anote la salida como **\<aad-user>** .  Usaremos este valor más adelante en este artículo.
4. Cierre la sesión de la consola web de OpenShift. Seleccione el botón en la esquina superior derecha de la ventana del explorador con la etiqueta **\<aad-user>** y elija **Cerrar sesión**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Concesión de los roles necesarios para la interacción con el registro al usuario de Azure AD

1. Inicie sesión en la consola web de OpenShift desde el explorador con las credenciales de `kubeadmin`.
1. Inicie sesión en la CLI de OpenShift con el token para `kubeadmin` según los pasos para `oc login` mencionados anteriormente, pero hágalo después de iniciar sesión en la consola web con `kubeadmin`.
1. Ejecute los comandos siguientes para habilitar el acceso al registro integrado correspondiente a **aad-user**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Obtención de la dirección URL del registro de contenedor

Use el comando `oc get route` como se muestra a continuación para obtener la dirección URL del registro de contenedor.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Anote la salida de la consola correspondiente a la **dirección URL del registro de contenedor**. Se usará como el nombre completo del registro en esta guía y las siguientes.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha configurado el registro de imagen de contenedor integrado, puede implementar una aplicación en OpenShift. En el caso de las aplicaciones Java, consulte [Implementación de una aplicación Java con Open Liberty/WebSphere Liberty en un clúster de Red Hat OpenShift en Azure 4](howto-deploy-java-liberty-app.md).