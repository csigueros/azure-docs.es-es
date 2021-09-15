---
title: 'Inicio rápido: Creación de una aplicación Ruby'
description: Para empezar a trabajar con Azure App Service, debe implementar su primera aplicación de Ruby en un contenedor Linux en App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dffb2634b59c54632364d8469244edb6840d7b9d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435427"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Creación de una aplicación Ruby on Rails en App Service

[Azure App Service en Linux](overview.md#app-service-on-linux) proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones que usa el sistema operativo Linux. En este tutorial de inicio rápido se explica cómo implementar una aplicación Ruby on Rails en App Service en Linux mediante [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> La pila de desarrollo de Ruby solo admite Ruby on Rails de momento. Si desea usar otra plataforma, como Sinatra, o una versión de Ruby no admitida, tiene que [ejecutarla en un contenedor personalizado](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Instalación de Ruby 2.6 o superior</a>
* <a href="https://git-scm.com/" target="_blank">Instalación de Git</a>

## <a name="download-the-sample"></a>Descarga del ejemplo

1. En una ventana del terminal, clone la aplicación de ejemplo en la máquina local y se desplazará al directorio que contiene el código de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. Asegúrese de que la rama predeterminada sea `main`.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > En App Service, no es obligatorio cambiar el nombre de la rama. Sin embargo, como muchos repositorios están cambiando la rama predeterminada a `main`, en este tutorial veremos también cómo implementar un repositorio desde `main`. Para obtener más información, consulte [Cambio de la rama de implementación](deploy-local-git.md#change-deployment-branch).

## <a name="run-the-application-locally"></a>Ejecutar la aplicación localmente

1. Instale los gems necesarios. Hay un elemento `Gemfile` incluido en el ejemplo, por lo que solo tiene que ejecutar el siguiente comando:

    ```bash
    bundle install
    ```

1. Una vez instalados los gems, inicie la aplicación:

    ```bash
    bundle exec rails server
    ```

1. Mediante el explorador web, vaya a `http://localhost:3000` para probar la aplicación localmente.

    ![Hello World configurada](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Creación de una aplicación web

1. Cree una [aplicación web](overview.md#app-service-on-linux) en el plan de App Service de `myAppServicePlan`. 

    En Cloud Shell, puede usar el comando [`az webapp create`](/cli/azure/webapp). En el siguiente ejemplo, reemplace `<app-name>` por un nombre único global de aplicación (los caracteres válidos son `a-z`, `0-9` y `-`). El tiempo de ejecución se establece en `RUBY|2.6`. Para ver todos los entornos en tiempo de ejecución admitidos, ejecute [`az webapp list-runtimes --linux`](/cli/azure/webapp). 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.6' --deployment-local-git
    ```

    Cuando se haya creado la aplicación web, la CLI de Azure mostrará información similar a la del ejemplo siguiente:

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    Ha creado una nueva aplicación web vacía, con la implementación de Git habilitada.

    > [!NOTE]
    > La dirección URL del repositorio de Git remoto se muestra en la propiedad `deploymentLocalGitUrl`, con el formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde esta dirección URL, ya que la necesitará más adelante.
    >

1. Vaya a la aplicación para ver la aplicación web recién creada con una imagen integrada. Reemplace _&lt;app_name_ por el nombre de la aplicación web.

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    Este es el aspecto que debería tener su nueva aplicación web:

    ![Página de presentación](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Implementación de aplicación

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)] 

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

## <a name="browse-to-the-app"></a>Navegación hasta la aplicación

Una vez finalizada la implementación, espere unos 10 segundos hasta que se reinicie la aplicación web y, a continuación, navegue a la aplicación web y compruebe los resultados.

```bash
http://<app-name>.azurewebsites.net
```

![Aplicación web actualizada](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Mientras se reinicia la aplicación, puede que aparezca el código de estado HTTP `Error 503 Server unavailable` en el explorador o la página predeterminada `Hey, Ruby developers!`. Puede que la aplicación tarde unos minutos en reiniciarse completamente.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Ruby on Rails con Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configure Ruby app](configure-language-ruby.md) (Configuración de una aplicación de Ruby)
