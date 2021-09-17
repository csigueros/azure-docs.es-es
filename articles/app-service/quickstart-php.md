---
title: 'Inicio rápido: Creación de una aplicación web de PHP'
description: Implementación de su primera aplicación Hola mundo de PHP en Azure App Service en cuestión de minutos. Realice la implementación mediante Git, que es una de las distintas maneras de realizar implementaciones en App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 05/02/2021
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 94d863dfcc3b6fd8b1d316994541ea61fd32c080
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787376"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Creación de una aplicación web PHP en Azure App Service

::: zone pivot="platform-windows"  
[Azure App Service](overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático.  En este tutorial de inicio rápido se explica cómo se implementa una aplicación PHP en Azure App Service en Windows.
::: zone-end  

::: zone pivot="platform-linux"
[Azure App Service](overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático.  En esta guía de inicio rápido se explica cómo se implementa una aplicación de PHP en Azure App Service en Linux.
::: zone-end  

Se crea la aplicación web con la [CLI de Azure](/cli/azure/get-started-with-azure-cli) en Cloud Shell y se usa Git para implementar el código PHP de ejemplo en la aplicación web.

![Aplicación de ejemplo que se ejecuta en Azure](media/quickstart-php/hello-world-in-browser.png)

Estos pasos se pueden realizar en este caso con una máquina Mac, Windows o Linux. Una vez instalados los requisitos previos, tardará aproximadamente cinco minutos en completar los pasos.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerrequisitos

Para completar esta guía de inicio rápido:

* <a href="https://git-scm.com/" target="_blank">Instalación de Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">Instalación de PHP</a>

## <a name="download-the-sample-locally"></a>Descarga local del código

1. Ejecute los siguientes comandos en una ventana de terminal. Con ello, se clonará la aplicación de ejemplo en el equipo local y se desplazará al directorio que contiene el código de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/php-docs-hello-world
    cd php-docs-hello-world
    ```
    
1. Asegúrese de que la rama predeterminada es `main`.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > App Service no exige el cambio de nombre de rama. Sin embargo, dado que muchos repositorios cambian su rama predeterminada a `main`, en este inicio rápido también se muestra cómo implementar un repositorio desde `main`.
    
## <a name="run-the-app-locally"></a>Ejecución de la aplicación de forma local

1. Ejecute la aplicación localmente para ver cómo debería ser si se implementara en Azure. Abra una ventana de terminal y use el comando `php` para iniciar el servidor web de PHP integrado.

    ```bash
    php -S localhost:8080
    ```
    
1. Abra un explorador web y vaya a la aplicación de ejemplo en `http://localhost:8080`.

    Verá el mensaje **Hola mundo** desde la aplicación de ejemplo mostrada en la página.
    
    ![Aplicación de ejemplo que se ejecuta localmente](media/quickstart-php/localhost-hello-world-in-browser.png)
    
1. En la ventana de terminal, presione **Ctrl + C** para salir del servidor web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Creación de una aplicación web

1. En Cloud Shell, cree una aplicación web en el plan de App Service `myAppServicePlan` con el comando [`az webapp create`](/cli/azure/webapp#az_webapp_create). 

    En el siguiente ejemplo, reemplace `<app-name>` por un nombre único global de aplicación (los caracteres válidos son `a-z`, `0-9` y `-`). El tiempo de ejecución se establece en `PHP|7.4`. Para ver todos los entornos en tiempo de ejecución admitidos, ejecute [`az webapp list-runtimes`](/cli/azure/webapp#az_webapp_list_runtimes). 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'PHP|7.4' --deployment-local-git
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
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    Ha creado una nueva aplicación web vacía, con la implementación de Git habilitada.

    > [!NOTE]
    > La dirección URL del repositorio de Git remoto se muestra en la propiedad `deploymentLocalGitUrl`, con el formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde esta dirección URL, ya que la necesitará más adelante.
    >

1. Vaya a la aplicación web recién creada. Reemplace _&lt;app-name>_ por el nombre de aplicación único creado en el paso anterior.

    ```bash
    http://<app-name>.azurewebsites.net
    ```

    Este es el aspecto que debería tener su nueva aplicación web:

    ![Página de la aplicación web vacía](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

  <pre>
  Counting objects: 2, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (2/2), done.
  Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
  Total 2 (delta 1), reused 0 (delta 0)
  remote: Updating branch 'main'.
  remote: Updating submodules.
  remote: Preparing deployment for commit id '25f18051e9'.
  remote: Generating deployment script.
  remote: Running deployment command...
  remote: Handling Basic Web Site deployment.
  remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
  remote: Copying file: '.gitignore'
  remote: Copying file: 'LICENSE'
  remote: Copying file: 'README.md'
  remote: Copying file: 'index.php'
  remote: Ignoring: .git
  remote: Finished successfully.
  remote: Running post deployment command(s)...
  remote: Deployment successful.
  To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      cc39b1e..25f1805  main -> main
  </pre>

## <a name="browse-to-the-app"></a>Navegación hasta la aplicación

Vaya a la aplicación implementada mediante el explorador web.

```
http://<app-name>.azurewebsites.net
```

El código de ejemplo de PHP se está ejecutando en una aplicación web de Azure App Service.

![Aplicación de ejemplo que se ejecuta en Azure](media/quickstart-php/hello-world-in-browser.png)

**¡Enhorabuena!** Ha implementado la primera aplicación PHP en App Service.

## <a name="update-locally-and-redeploy-the-code"></a>Actualización local y nueva implementación del código

1. Con un editor de texto local, abra el archivo `index.php` dentro de la aplicación PHP y realice un pequeño cambio en el texto dentro de la cadena situada junto a `echo`:

    ```php
    echo "Hello Azure!";
    ```

1. En la ventana del terminal local, confirme los cambios en Git e inserte los cambios de código en Azure.

    ```bash
    git commit -am "updated output"
    git push azure main
    ```

1. Una vez que la implementación haya finalizado, vuelva a la ventana del explorador que abrió en el paso **Navegación hasta la aplicación** y actualice la página.

    ![Aplicación de ejemplo actualizada que se ejecuta en Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Administración de la nueva aplicación de Azure

1. Vaya a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> para administrar la aplicación web que ha creado. Busque y seleccione **App Services**.

    ![Buscar App Services, Azure Portal, crear una aplicación web de PHP](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Seleccione el nombre de la aplicación de Azure.

    ![Navegación en el portal a la aplicación de Azure](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Se mostrará la página de **información general** de la aplicación web. En ella, puede realizar tareas de administración básicas como **examinar**, **detener**, **reiniciar** y **eliminar**.

    ![Página de App Service en Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    En el menú de la aplicación web se proporcionan distintas opciones para configurar la aplicación. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [PHP con MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Configure PHP app](configure-language-php.md) (Configuración de una aplicación de PHP)
