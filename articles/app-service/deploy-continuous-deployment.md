---
title: Configuración de la implementación continua
description: Aprenda a habilitar CI/CD para Azure App Service desde GitHub, Bitbucket, Azure Repos u otros repositorios. Seleccione la canalización de compilación que se ajuste a sus necesidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bff93e0dcfb49871cefa68d16d7a7db85c0f69ce
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122638376"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implementación continua en Azure App Service

[Azure App Service](overview.md) le permite realizar implementaciones continuas desde los repositorios de [GitHub](https://help.github.com/articles/create-a-repo), [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) y [Azure Repos](/azure/devops/repos/git/creatingrepo) al incorporar las últimas actualizaciones.

> [!NOTE]
> La página **Centro de desarrollo (clásico)** de Azure Portal, una versión anterior de la funcionalidad de implementación, quedó en desuso en marzo de 2021. Este cambio no afecta a ninguna configuración de implementación existente en la aplicación, y puede continuar con la administración de la implementación de aplicaciones desde la página **Centro de implementación** del portal.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-the-deployment-source"></a>Configuración del origen de implementación

1. En [Azure Portal](https://portal.azure.com), vaya a la página de administración de la aplicación de App Service.

1. En el panel izquierdo, seleccione **Centro de implementación**. Luego, seleccione **Configuración**. 

1. En el cuadro **Origen**, seleccione una de las opciones de CI/CD:

    ![Captura de pantalla que muestra cómo elegir el origen de implementación.](media/app-service-continuous-deployment/choose-source.png)

Seleccione la pestaña correspondiente al proveedor de la compilación para continuar.

# <a name="github"></a>[GitHub](#tab/github)

4. [Acciones de GitHub](#how-the-github-actions-build-provider-works) es el proveedor de compilación predeterminado. Para cambiar el proveedor, seleccione **Cambiar proveedor** > **Servicio de compilación de App Service** (Kudu) > **Aceptar**.

    > [!NOTE]
    > Para usar Azure Pipelines como proveedor de compilación para la aplicación de App Service, configure CI/CD directamente desde Azure Pipelines. No lo configure en App Service. La opción **Azure Pipelines** simplemente apunta a la dirección correcta.

1. Si va a realizar la implementación desde GitHub por primera vez, seleccione **Autorizar** y siga las indicaciones de autorización. Si quiere realizar la implementación desde un repositorio de usuario diferente, seleccione **Cambiar cuenta**.

1. Después de autorizar su cuenta de Azure con GitHub, seleccione la **organización**, el **repositorio** y la **rama** para configurar CI/CD. Si no encuentra ningún repositorio u organización, es posible que tenga que habilitar más permisos en GitHub. Para más información, consulte [Administración del acceso a los repositorios de la organización](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories).

1. Cuando se seleccione Acciones de GitHub como proveedor de compilación, puede seleccionar el archivo de flujo de trabajo que quiera con las listas desplegables **Pila en tiempo de ejecución** y **Versión**. Azure confirma este archivo de flujo de trabajo en el repositorio de GitHub seleccionado para controlar las tareas de compilación e implementación. Para ver el archivo antes de guardar los cambios, seleccione **Previsualizar archivo**.

    > [!NOTE]
    > App Service detecta la [configuración de la pila de lenguaje](configure-common.md#configure-language-stack-settings) de la aplicación y selecciona la plantilla de flujo de trabajo más adecuada. Si elige otra plantilla, esta podría implementar una aplicación que no se ejecute correctamente. Para más información, consulte [Cómo funciona el proveedor de compilación de Acciones de GitHub](#how-the-github-actions-build-provider-works).

1. Seleccione **Guardar**.
   
    Las nuevas confirmaciones del repositorio y la rama seleccionados ahora se implementan continuamente en su aplicación de App Service. Puede hacer el seguimiento de las confirmaciones y las implementaciones en la pestaña **Registros**.

# <a name="bitbucket"></a>[Bitbucket](#tab/bitbucket)

La integración de Bitbucket usa los servicios de compilación de App Service (Kudu) para la automatización de la compilación.

4. Si va a realizar la implementación desde Bitbucket por primera vez, seleccione **Autorizar** y siga las indicaciones de autorización. Si quiere realizar la implementación desde un repositorio de usuario diferente, seleccione **Cambiar cuenta**.

1. Para Bitbucket, seleccione el **equipo** , el **repositorio**  y la **rama**  de Bitbucket que quiere implementar continuamente.

1. Seleccione **Guardar**.
   
    Las nuevas confirmaciones del repositorio y la rama seleccionados ahora se implementan continuamente en su aplicación de App Service. Puede hacer el seguimiento de las confirmaciones y las implementaciones en la pestaña **Registros**.
   
# <a name="local-git"></a>[Git local](#tab/local)

Vea [Implementación de Git local en Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)
   
   > [!NOTE]
   > Azure Repos se admite como origen de implementación para las aplicaciones de Windows.
   >

4. El servicio de compilación de App Service (Kudu) es el proveedor de compilación predeterminado.

    > [!NOTE]
    > Para usar Azure Pipelines como proveedor de compilación para la aplicación de App Service, configure CI/CD directamente desde Azure Pipelines. No lo configure en App Service. La opción **Azure Pipelines** simplemente apunta a la dirección correcta.

1. Seleccione la **organización de Azure DevOps** , el **proyecto**, el **repositorio** y la **rama** que quiere implementar continuamente. 

    Si su organización de DevOps no figura en la lista, significa que aún no se ha vinculado a su suscripción de Azure. Para más información, consulte [Creación de una conexión de servicio de Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Deshabilitación de la implementación continua

1. En [Azure Portal](https://portal.azure.com), vaya a la página de administración de la aplicación de App Service.

1. En el panel izquierdo, seleccione **Centro de implementación**. A continuación, seleccione **Configuración** > **Desconectar**:

    ![Captura de pantalla que muestra cómo desconectar la sincronización de carpetas en la nube con la aplicación de App Service en Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. De manera predeterminada, el archivo de flujo de trabajo de Acciones de GitHub se conserva en el repositorio, pero seguirá desencadenando la implementación en la aplicación. Para eliminar el archivo del repositorio, seleccione **Eliminar archivo de flujo de trabajo**.

1. Seleccione **Aceptar**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Cómo funciona el proveedor de compilación de Acciones de GitHub

El proveedor de compilación de Acciones de GitHub es una opción para [CI/CD de GitHub](#configure-the-deployment-source), y completa estas acciones para configurar CI/CD:

- Deposita un archivo de flujo de trabajo de Acciones de GitHub en el repositorio de GitHub para controlar las tareas de compilación e implementación en App Service.
- Agrega el perfil de publicación de la aplicación como secreto de GitHub. El archivo de flujo de trabajo usa este secreto para autenticarse con App Service.
- Captura la información de los [registros de ejecución del flujo de trabajo](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) y la muestra en la pestaña **Registros** del Centro de implementación de la aplicación.

Puede personalizar el proveedor de compilación de Acciones de GitHub de estas maneras:

- Personalice el archivo de flujo de trabajo después de que se genere en el repositorio de GitHub. Para más información, consulte [Sintaxis de flujo de trabajo para Acciones de GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Solo tiene que asegurarse de que el flujo de trabajo se implementa en App Service con la acción [azure/webapps-deploy](https://github.com/Azure/webapps-deploy).
- Si la rama seleccionada está protegida, todavía puede obtener una vista previa del archivo de flujo de trabajo sin guardar la configuración y, después, agregarla manualmente al repositorio. Este método no ofrece la integración de registros con Azure Portal.
- En lugar de usar un perfil de publicación, implemente con una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) en Azure Active Directory.

#### <a name="authenticate-by-using-a-service-principal"></a>Autenticación mediante una entidad de servicio

Esta configuración opcional reemplaza la autenticación predeterminada por los perfiles de publicación en el archivo de flujo de trabajo generado.

1. Genere una entidad de servicio mediante el comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) de la [CLI de Azure](/cli/azure/). En el ejemplo siguiente, reemplace \<subscription-id> , \<group-name> y \<app-name> con sus propios valores:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Por seguridad, conceda el acceso mínimo necesario a la entidad de servicio. El ámbito del ejemplo anterior se limita a la aplicación específica de App Service y no a todo el grupo de recursos.
    
1. Guarde la salida JSON completa para el paso siguiente, incluido `{}` de nivel superior.

1. En su repositorio en [GitHub](https://github.com/), seleccione **Configuración** > **Secretos** > **Agregar un nuevo secreto**.

1. Pegue la salida JSON completa del comando de la CLI de Azure en el campo de valor del secreto. Asigne un nombre al secreto, como `AZURE_CREDENTIALS`.

1. En el archivo de flujo de trabajo generado por el Centro de implementación, revise el paso de `azure/webapps-deploy` para que se vea como en el ejemplo siguiente (que se ha modificado desde un archivo de flujo de trabajo de Node.js):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added.
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile.
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure.
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Implementación desde otros repositorios

En el caso de las aplicaciones de Windows, puede configurar manualmente la implementación continua desde un repositorio GIT o de Mercurial en la nube que el portal no admite directamente, por ejemplo, [GitLab](https://gitlab.com/). Para ello, seleccione **Git externo** en la lista desplegable **Origen**. Para obtener más información, consulte [Configuración de la implementación continua mediante pasos manuales](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Más recursos

* [Implementación desde Azure Pipelines a Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Investigación de los problemas habituales con la implementación continua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Uso de Azure PowerShell](/powershell/azure/)
* [Proyecto Kudu](https://github.com/projectkudu/kudu/wiki)
