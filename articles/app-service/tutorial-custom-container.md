---
title: 'Tutorial: Compilación y ejecución de una imagen personalizada en Azure App Service'
description: Una guía paso a paso para crear una imagen personalizada de Linux o Windows, insertarla en Azure Container Registry y, a continuación, implementarla en Azure App Service. Obtenga información sobre cómo migrar software personalizado a App Service en un contenedor personalizado.
ms.topic: tutorial
ms.date: 08/04/2021
ms.author: msangapu
keywords: azure app service, web app, linux, windows, docker, container
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 1574464f4f6f4c4abe8a3fc45247f28b7b97bd96
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428081"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migración de software personalizado a Azure App Service mediante un contenedor personalizado

::: zone pivot="container-windows"  

[Azure App Service](overview.md) proporciona las pilas de aplicaciones predefinidas en Windows, como ASP.NET o Node.js, que se ejecutan en IIS. El entorno preconfigurado de Windows bloquea el sistema operativo desde el acceso administrativo, las instalaciones de software, cambios en la caché global de ensamblados, etc (consulte [Funcionalidad del sistema operativo en Azure App Service](operating-system-functionality.md)). Sin embargo, mediante un contenedor de Windows personalizado de App Service se pueden realizar los cambios en el sistema operativo que necesita la aplicación, por lo que es fácil migrar aplicaciones locales que requieran configuración personalizada para el sistema operativo y el software. En este tutorial se muestra cómo migrar a App Service a partir de una aplicación de ASP.NET que utiliza fuentes personalizadas instaladas en la biblioteca de fuentes de Windows. Implemente una imagen de Windows con configuración personalizada de Visual Studio en [Azure Container Registry](../container-registry/index.yml) y ejecútela en App Service.

![Muestra la aplicación web que se ejecuta en un contenedor Windows.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Registrarse para obtener una cuenta de Docker Hub</a>
- Instalar <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker para Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Cambiar Docker para ejecutar contenedores de Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instalar Visual Studio 2019</a> con las cargas de trabajo **ASP.NET y desarrollo web** y **desarrollo de Azure**. Si ya ha instalado Visual Studio 2019:
    - Para instalar las actualizaciones más recientes de Visual Studio, haga clic en **Ayuda** > **Buscar actualizaciones**.
    - Para agregar las cargas de trabajo en Visual Studio, haga clic en **Herramientas** > **Get Tools and Features** (Obtener herramientas y características).

## <a name="set-up-the-app-locally"></a>Instalación local de la aplicación

### <a name="download-the-sample"></a>Descarga del ejemplo

En este paso se configura el proyecto de .NET local.

- [Descarga del proyecto de ejemplo](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip)
- Extraiga (descomprima) el archivo *custom-font-win-container.zip*.

El proyecto de ejemplo contiene una aplicación de ASP.NET sencilla que usa una fuente personalizada instalada en la biblioteca de fuentes de Windows. No es necesario instalar fuentes, pero es un ejemplo de aplicación integrada con el sistema operativo subyacente. Para migrar este tipo de aplicación a App Service, debe o bien rediseñar el código para quitar la integración o migrarla como está a un contenedor de Windows personalizado.

### <a name="install-the-font"></a>Instalación de la fuente

En el Explorador de Windows, vaya a _custom-font-win-container-master/CustomFontSample_, haga clic con el botón derecho en _FrederickatheGreat Regular.ttf_ y seleccione **Instalar**.

Esta fuente está disponible públicamente en [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Ejecución la aplicación

Abra el archivo *custom-font-win-container/CustomFontSample.sln* en Visual Studio. 

Escriba `Ctrl+F5` para ejecutar la aplicación sin depurarla. La aplicación se muestra en el explorador predeterminado. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Captura de pantalla que muestra la aplicación en el explorador predeterminado.":::

Dado que usa una fuente instalada, la aplicación no se puede ejecutar en el espacio aislado de App Service. Sin embargo, puede implementarla mediante un contenedor de Windows en su lugar, ya que puede instalar la fuente en el contenedor de Windows.

### <a name="configure-windows-container"></a>Configuración del contenedor de Windows

En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CustomFontSample** y seleccione **Agregar** > **Container Orchestration Support** (Compatibilidad con la orquestación de contenedores).

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Captura de pantalla de la ventana Explorador de soluciones que muestra los elementos de menú seleccionados (el proyecto CustomFontSample, Agregar y Compatibilidad con el orquestador de contenedor).":::

Seleccione **Docker Compose** > **Aceptar**.

El proyecto ahora está configurado para ejecutarse en un contenedor de Windows. _Dockerfile_ se agrega al proyecto **CustomFontSample** y, a la solución, un proyecto **docker-compose**. 

Desde el Explorador de soluciones, abra **Dockerfile**.

Tiene que usar una [imagen principal compatible](configure-custom-container.md#supported-parent-images). Reemplace la línea `FROM` por el código siguiente para cambiar la imagen primaria:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Agregue la siguiente línea al final del archivo y guárdelo:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Puede encontrar _InstallFont.ps1_ en el proyecto **CustomFontSample**. Es un sencillo script que instala la fuente. Puede encontrar una versión más compleja del script en el [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Para probar el contenedor de Windows localmente, asegúrese de que Docker se ha iniciado en la máquina local.
>

## <a name="publish-to-azure-container-registry"></a>Publicación en Azure Container Registry

[Azure Container Registry](../container-registry/index.yml) puede almacenar las imágenes para las implementaciones de contenedor. Puede configurar App Service para usar imágenes hospedadas en Azure Container Registry.

### <a name="open-publish-wizard"></a>Apertura del asistente para publicación

En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CustomFontSample** y seleccione **Publicar**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Captura de pantalla del Explorador de soluciones que muestra los elementos seleccionados (proyecto CustomFontSample y Publicar).":::

### <a name="create-registry-and-publish"></a>Creación y publicación del registro

En el asistente para publicación, seleccione **Container Registry** > **Crear una instancia de Azure Container Registry** > **Publicar**.

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Captura de pantalla del Asistente para publicación que muestra elementos seleccionados (Container Registry, Crear una instancia de Azure Container Registry y el botón Publicar).":::

### <a name="sign-in-with-azure-account"></a>Inicio de sesión con la cuenta de Azure

En el cuadro de diálogo **Crear una instancia de Azure Container Registry**, seleccione **Agregar una cuenta** e inicie sesión en su suscripción de Azure. Si ya ha iniciado sesión, seleccione la cuenta que contiene la suscripción deseada en la lista desplegable.

![Inicio de sesión en Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Configuración del registro

Configure el nuevo registro de contenedor en función de los valores sugeridos en la siguiente tabla. Cuando haya terminado, haga clic en **Crear**.

| Configuración  | Valor sugerido | Para obtener más información |
| ----------------- | ------------ | ----|
|**Prefijo de DNS**| Mantenga el nombre de registro generado o cámbielo por otro nombre único. |  |
|**Grupo de recursos**| Haga clic en **Nuevo**, escriba **myResourceGroup** y haga clic en **Aceptar**. |  |
|**SKU**| Básico | [Planes de tarifa](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Ubicación del registro**| Oeste de Europa | |

![Configuración del registro de contenedor de Azure](./media/tutorial-custom-container/configure-registry.png)

Se abre una ventana de terminal que muestra el progreso de la implementación de la imagen. Espere a que la implementación se complete.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-web-app"></a>Creación de una aplicación web

En el menú izquierdo, seleccione **Crear un recurso** > **Web** > **Web App for Containers**.

### <a name="configure-app-basics"></a>Configuración de los aspectos básicos de la aplicación

En la pestaña **Aspectos básicos**, configure los valores según la tabla siguiente y, a continuación, haga clic en **Siguiente: Docker**.

| Configuración  | Valor sugerido | Para obtener más información |
| ----------------- | ------------ | ----|
|**Suscripción**| Asegúrese de que se ha seleccionado la suscripción correcta. |  |
|**Grupo de recursos**| Seleccione **Crear nuevo**, escriba **myResourceGroup** y haga clic en **Aceptar**. |  |
|**Nombre**| Escriba un nombre único. | La dirección URL de la aplicación web es `https://<app-name>.azurewebsites.net`, donde `<app-name>` es el nombre de la aplicación. |
|**Publicar**| Contenedor de Docker | |
|**Sistema operativo**| Windows | |
|**Región**| Oeste de Europa | |
|**Plan de Windows**| Seleccione **Crear nuevo**, escriba **myAppServicePlan** y haga clic en **Aceptar**. | |

La pestaña **Aspectos básicos** debe tener el siguiente aspecto:

![Muestra la pestaña Aspectos básicos que se usa para configurar la aplicación web.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Configuración del contenedor de Windows

En la pestaña **Docker**, configure el contenedor de Windows personalizado como se muestra en la tabla siguiente y seleccione **Revisar y crear**.

| Configuración  | Valor sugerido |
| ----------------- | ------------ |
|**Origen de la imagen**| Registro de Azure Container |
|**Registro**| Seleccione el [registro que ha creado anteriormente](#publish-to-azure-container-registry). |
|**Imagen**| customfontsample |
|**Tag**| latest |

### <a name="complete-app-creation"></a>Creación completa de la aplicación

Haga clic en **Crear** y espere que Azure cree los recursos necesarios.

## <a name="browse-to-the-web-app"></a>Navegación hasta la aplicación web

Una vez completada la operación de Azure, se muestra un cuadro de notificación.

![Muestra que se ha completado la operación de Azure.](media/tutorial-custom-container/portal-create-finished.png)

1. Haga clic en **Ir al recurso**.

2. En la página de la aplicación, haga clic en el vínculo situado bajo **URL**.

Se abre una nueva página del explorador en la página siguiente:

![Muestra la nueva página del explorador para la aplicación web.](media/tutorial-custom-container/app-starting.png)

Espere unos minutos e inténtelo de nuevo hasta que llegue a la página principal con la atractiva fuente esperada:

![Muestra la página principal con la fuente que ha configurado.](media/tutorial-custom-container/app-running.png)

**¡Enhorabuena!** Ha migrado una aplicación de ASP.NET a Azure App Service en un contenedor de Windows.

## <a name="see-container-start-up-logs"></a>Consulta de los registros de inicio del contenedor

El contenedor de Windows puede tardar un tiempo en cargarse. Para ver el progreso, vaya a la siguiente dirección URL sustituyendo *\<app-name>* por el nombre de la aplicación.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Los registros transmitidos tienen este aspecto:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service usa la tecnología de contenedores de Docker para hospedar imágenes integradas e imágenes personalizadas. Para ver una lista de imágenes integradas, ejecute el comando de la CLI de Azure ['az webapp list-runtimes --linux'](/cli/azure/webapp#az_webapp_list_runtimes). Si esas imágenes no satisfacen sus necesidades, puede crear e implementar una imagen personalizada.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Insertar una imagen personalizada de Docker en Azure Container Registry
> * Implementar la imagen personalizada en App Service
> * Configuración de las variables de entorno
> * Extraer la imagen en App Service mediante una identidad administrada
> * Acceso a los registros de diagnóstico
> * Habilitar CI/CD en Azure Container Registry para App Service
> * Conexión al contenedor con SSH

Al completar este tutorial, se incurre en un pequeño gasto en su cuenta de Azure para el registro de contenedor y puede suponer costos adicionales para hospedar el contenedor durante más de un mes.

## <a name="set-up-your-initial-environment"></a>Configuración del entorno inicial

- Disponga de una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Instale [Docker](https://docs.docker.com/get-started/#setup), el cual se usará para compilar imágenes de Docker. La instalación de Docker puede requerir reiniciar el equipo.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- En este tutorial se necesita la versión 2.0.80, o versiones posteriores, de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

Después de instalar Docker o ejecutar Azure Cloud Shell, abra una ventana de terminal y compruebe que Docker está instalado:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Clonación o descarga de la aplicación de ejemplo

Puede obtener el ejemplo de este tutorial a través de Git Clone o mediante descarga.

### <a name="clone-with-git"></a>Clonación con Git

Clone el repositorio de ejemplo:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Asegúrese de incluir el argumento `--config core.autocrlf=input` para garantizar los finales de línea correctos en los archivos que se usan en el contenedor de Linux:

A continuación, vaya a esa carpeta:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Descargar desde GitHub

En lugar de usar Git Clone, puede visitar [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux), seleccionar **Clone** (Clonar) y, a continuación, seleccionar **Download ZIP** (Descargar ZIP). 

Descomprima el archivo ZIP en una carpeta denominada *docker-django-webapp-linux*. 

A continuación, abra una ventana de terminal en esa carpeta *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>(Opcional) Examen del archivo de Docker

El archivo del ejemplo denominado _Dockerfile_ que describe la imagen de Docker y contiene las instrucciones de configuración:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* El primer grupo de comandos instala los requisitos de la aplicación en el entorno.
* El segundo grupo de comandos crea un servidor [SSH](https://www.ssh.com/ssh/protocol/) para la comunicación segura entre el contenedor y el host.
* La última línea del archivo, `ENTRYPOINT ["init.sh"]`, invoca a `init.sh` para iniciar el servicio SSH y el servidor de Python.

## <a name="build-and-test-the-image-locally"></a>Compilación y prueba de la imagen de forma local

> [!NOTE]
> Docker Hub tiene [cuotas en el número de extracciones anónimas por IP y el número de extracciones autenticadas por usuario gratuito (consulte **Transferencia de datos**)](https://www.docker.com/pricing). Si observa que las extracciones de Docker Hub están limitadas, pruebe `docker login` si aún no ha iniciado sesión.
> 

1. Ejecute el siguiente comando para compilar la imagen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Ejecute el contenedor de Docker de forma local para comprobar que la compilación funciona:

    ```bash
    docker run -it -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Este comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) especifica el puerto con el argumento `-p` seguido del nombre de la imagen. `-it` le permite detenerlo con `Ctrl+C`.
    
    > [!TIP]
    > Si ejecuta Windows y ve el error *standard_init_linux.go:211: exec user process caused "no such file or directory"* (el proceso de usuario ejecutado provocó el error "no existe dicho archivo o directorio"), el archivo *init.sh* contiene los finales de línea CR o LF en lugar de los finales LF esperados. Este error se produce si ha usado git para clonar el repositorio de ejemplo, pero omitió el parámetro `--config core.autocrlf=input`. En este caso, vuelva a clonar el repositorio con el argumento "--config". También puede ver el error si editó *init.sh* y lo guardó con finales CR o LF. En este caso, guarde el archivo de nuevo solo con los finales LF.

1. Vaya a `http://localhost:8000` para comprobar que la aplicación web y el contenedor funcionan correctamente.

    ![Prueba local de la aplicación web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

En esta sección y en las siguientes, se aprovisionan los recursos de Azure en los que se inserta la imagen y, a continuación, se implementa un contenedor en Azure App Service. Para empezar, cree un grupo de recursos en el que recopilar todos estos recursos.

Ejecute el comando [az group create](/cli/azure/group#az_group_create) para crear un grupo de recursos:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Puede cambiar el valor de `--location` para especificar una región cercana.

## <a name="push-the-image-to-azure-container-registry"></a>Inserción de la imagen en Azure Container Registry

En esta sección, va a insertar la imagen en Azure Container Registry desde la que App Service puede implementarla.

1. Ejecute el comando [`az acr create`](/cli/azure/acr#az_acr_create) para crear una instancia de Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
    ```
    
    Reemplace `<registry-name>` por un nombre único para el registro. El nombre debe contener solo letras y números, y debe ser único en todo Azure.

1. Ejecute el comando [`az acr show`](/cli/azure/acr#az_acr_show) para recuperar las credenciales para el registro:

    ```azurecli-interactive
    az acr credential show --resource-group myResourceGroup --name <registry-name>
    ```
    
    La salida JSON de este comando proporciona dos contraseñas junto con el nombre de usuario del registro.
    
1. Use el comando `docker login` para iniciar sesión en el registro de contenedor:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Reemplace `<registry-name>` y `<registry-username>` por los valores de los pasos anteriores. Cuando se le solicite, escriba una de las contraseñas del paso anterior.

    Use el mismo nombre de registro en el resto de pasos de esta sección.

1. Una vez que el inicio de sesión se realice correctamente, etiquete la imagen local de Docker para el registro:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Use el comando `docker push` para enviar la imagen al registro:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    La primera vez que la imagen se carga puede tardar unos minutos, ya que incluye la imagen base. Las cargas posteriores suelen ser más rápidas.

    Mientras espera, puede completar los pasos de la sección siguiente para configurar App Service de modo que la implementación se haga desde el registro.

1. Use el comando `az acr repository list` para comprobar que la operación de instalación se realizó de forma correcta:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    La salida debe mostrar el nombre de la imagen.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Configuración de App Service para implementar la imagen desde el registro

Para implementar un contenedor en Azure App Service, primero debe crear una aplicación web en App Service y luego conectarla al registro de contenedor. Cuando se inicia la aplicación web, App Service extrae automáticamente la imagen del registro.

1. Cree un plan de App Service mediante el comando [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create):

    ```azurecli-interactive
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
    ```

    Un plan de App Service corresponde a la máquina virtual que hospeda la aplicación web. De forma predeterminada, el comando anterior usa un [plan de tarifa B1](https://azure.microsoft.com/pricing/details/app-service/linux/) económico que es gratuito durante el primer mes. Puede controlar el plan con el parámetro `--sku`.

1. Cree la aplicación web con el comando [`az webpp create`](/cli/azure/webapp#az_webapp_create):

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Reemplace `<app-name>` por un nombre para la aplicación web, el cual debe ser único en todo Azure. Reemplace también `<registry-name>` por el nombre del registro de la sección anterior.

1. Use [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) para establecer la variable de entorno `WEBSITES_PORT` según lo esperado por el código de la aplicación: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Reemplace `<app-name>` por el valor que usó en el paso anterior.
    
    Para más información sobre esta variable de entorno, consulte el archivo [Léame del repositorio de GitHub del ejemplo](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Habilite [la identidad administrada asignada por el sistema](./overview-managed-identity.md) para la aplicación web mediante el comando [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign):

    ```azurecli-interactive
    az webapp identity assign --resource-group myResourceGroup --name <app-name> --query principalId --output tsv
    ```

    Reemplace `<app-name>` por el valor que usó en el paso anterior. La salida del comando (filtrada por los argumentos `--query` y `--output`) es la entidad de servicio de la identidad asignada, la cual usará pronto.

    La identidad administrada le permite conceder permisos a la aplicación web para acceder a otros recursos de Azure sin necesidad de credenciales específicas.

1. Recupere el identificador de la suscripción con el comando [`az account show`](/cli/azure/account#az_account_show) que necesitará en el paso siguiente:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Conceda permiso a la identidad administrada para acceder al registro de contenedor:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Reemplace los siguientes valores:
    - `<principal-id>` por el identificador de entidad de servicio del comando `az webapp identity assign`
    - `<registry-name>` por el nombre del registro de contenedor
    - `<subscription-id>` por el identificador de suscripción recuperado con el comando `az account show`

    Para más información acerca de estos permisos, consulte [¿Qué es el control de acceso basado en rol de Azure?](../role-based-access-control/overview.md).

1. Configure la aplicación para que use la identidad administrada para la extracción de Azure Container Registry.

    ```azurecli-interactive
    az resource update --ids /subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app-name>/config/web --set properties.acrUseManagedIdentityCreds=True
    ```
    
    Reemplace los siguientes valores:
    - `<subscription-id>` por el id. de suscripción recuperado en el comando `az account show`.
    - `<app-name>` por el nombre de la aplicación web.

    > [!TIP]
    > Si la aplicación usa una [identidad administrada asignada por el usuario](overview-managed-identity.md#add-a-user-assigned-identity), establezca una propiedad `AcrUserManagedIdentityID` adicional para especificar su id. de cliente:
    >
    > ```azurecli-interactive
    > clientId=$(az identity show --resource-group <group-name> --name <identity-name> --query clientId --output tsv)
    > az resource update --ids /subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app-name>/config/web --set properties.AcrUserManagedIdentityID=$clientId
    > ```

## <a name="deploy-the-image-and-test-the-app"></a>Implementación de la imagen y prueba de la aplicación

Puede completar estos pasos una vez que la imagen se inserte en el registro de contenedor y la instancia de App Service esté totalmente aprovisionada.

1. Use el comando [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) para especificar el registro de contenedor y la imagen que se vaya a implementar para la aplicación web:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Reemplace `<app-name>` por el nombre de la aplicación web y reemplace `<registry-name>` en dos lugares por el nombre del registro. 

    - Al usar un registro que no sea de Docker Hub (como se muestra en este ejemplo), `--docker-registry-server-url` debe tener el formato `https://`, seguido del nombre de dominio completo del registro.
    - El mensaje "No se proporcionó ninguna credencial para acceder a Azure Container Registry. Intentando buscar..." indica que Azure usa la identidad administrada de la aplicación para autenticarse con el registro de contenedor en lugar de solicitar un nombre de usuario y una contraseña.
    - Si se produce el error, "AttributeError: el objeto 'NoneType' no tiene el atributo 'reserved'", asegúrese de que el valor de `<app-name>` sea correcto.

    > [!TIP]
    > Puede recuperar la configuración del contenedor de la aplicación web en cualquier momento con el comando `az webapp config container show --name <app-name> --resource-group myResourceGroup`. La imagen se especifica en la propiedad `DOCKER_CUSTOM_IMAGE_NAME`. Cuando la aplicación web se implementa con Azure DevOps o con las plantillas de Azure Resource Manager, la imagen también puede aparecer en una propiedad denominada `LinuxFxVersion`. Ambas propiedades tienen el mismo propósito. Si ambas están presentes en la configuración de la aplicación web, `LinuxFxVersion` tiene prioridad.

1. Una vez que se complete el comando `az webapp config container set`, la aplicación web debe ejecutarse en el contenedor en App Service.

    Para probar la aplicación, vaya a `https://<app-name>.azurewebsites.net` y reemplace `<app-name>` por el nombre de la aplicación web. En el primer acceso, la aplicación puede tardar algún tiempo en responder porque App Service debe extraer toda la imagen del registro. Si el tiempo del explorador se agota, simplemente actualice la página. Una vez que se extraiga la imagen inicial, las pruebas posteriores se ejecutarán mucho más rápido.

    ![Prueba correcta de la aplicación web en Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="access-diagnostic-logs"></a>Acceso a los registros de diagnóstico

Mientras espera a que App Service extraiga la imagen, resulta útil para ver exactamente lo que hace App Service transmitiendo los registros del contenedor al terminal.

1. Active el registro de contenedor:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
    ```
    
1. Habilite el streaming de registro:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group myResourceGroup
    ```
    
    Si no ve los registros de la consola de inmediato, vuelve a comprobarlo en 30 segundos.

    También puede inspeccionar los archivos de registro desde el explorador en `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Para detener el streaming del registro en cualquier momento, escriba `Ctrl+C`.

## <a name="configure-continuous-deployment"></a>Configuración de la implementación continua

La aplicación de App Service puede extraer ahora la imagen de contenedor de forma segura desde el registro de contenedor privado. Sin embargo, no sabe cuándo se actualiza esa imagen en el registro. Cada vez que inserta la imagen actualizada en el registro, debe desencadenar manualmente una extracción de imagen mediante el reinicio de la aplicación de App Service. En este paso, habilitará CI/CD para que se notifique a App Service de una nueva imagen y desencadene una extracción automáticamente.

1. Habilite CI/CD en App Service.

    ```azurecli-interactive
    az webapp deployment container config --enable-cd true --name <app-name> --resource-group myResourceGroup --query CI_CD_URL --output tsv
    ```

    `CI_CD_URL` es una dirección URL que App Service genera automáticamente. El registro debe tener esta dirección URL para notificar a App Service que se ha producido una inserción de imagen. En realidad, no crea el webhook automáticamente.

1. Cree un webhook en el registro de contenedor mediante la dirección CI_CD_URL que obtuvo en el último paso.

    ```azurecli-interactive
    az acr webhook create --name appserviceCD --registry <registry-name> --uri '<ci-cd-url>' --actions push --scope appsvc-tutorial-custom-image:latest
    ```

1. Para probar si el webhook está configurado correctamente, haga ping en el webhook y compruebe si obtiene una respuesta 200 OK.

    ```azurecli-interactive
    eventId=$(az acr webhook ping --name appserviceCD --registry <registry-name> --query id --output tsv)
    az acr webhook list-events --name appserviceCD --registry <registry-name> --query "[?id=='$eventId'].eventResponseMessage"
    ```

    > [!TIP]
    > Para ver toda la información sobre todos los eventos de webhook, quite el parámetro `--query`.
    >
    > Si transmite el registro de contenedor, debería ver el mensaje después del ping en el webhook: `Starting container for site`, porque el webhook desencadena el reinicio de la aplicación. Puesto que no ha realizado ninguna actualización de la imagen, no hay nada nuevo que App Service pueda extraer.

## <a name="modify-the-app-code-and-redeploy"></a>Modificación del código de la aplicación y nueva implementación

En esta sección, realizará un cambio en el código de la aplicación web, reconstruirá la imagen y, a continuación, lo enviará al registro de contenedor. A continuación, App Service extrae automáticamente la imagen actualizada del registro para actualizar la aplicación web en ejecución.

1. En la carpeta local *docker-django-webapp-linux*, abra el archivo *app/templates/app/index.html*.

1. Cambie el primer elemento HTML para que coincida con el código siguiente.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Guarde los cambios.

1. Cambie a la carpeta *docker-django-webapp-linux* y vuelva a generar la imagen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Actualice el número de versión de la etiqueta de la imagen a v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:v1.0.1
    ```

    Reemplace `<registry-name>` por el nombre del registro.

1. Inserte la imagen en el registro:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:v1.0.1
    ```

1. Una vez completada la inserción de la imagen, el webhook notifica a App Service la inserción y App Service intenta extraer la imagen actualizada. Espere unos minutos y, a continuación, compruebe que la actualización se ha implementado. Para ello, vaya a `https://<app-name>.azurewebsites.net`.

## <a name="connect-to-the-container-using-ssh"></a>Conexión al contenedor con SSH

SSH habilita la comunicación segura entre un contenedor y un cliente. Para habilitar la conexión SSH en su contenedor, la imagen personalizada debe configurarse para ello. Una vez que el contenedor se esté ejecutando, puede abrir una conexión SSH.

### <a name="configure-the-container-for-ssh"></a>Configuración del contenedor para SSH

La aplicación de ejemplo que se usa en este tutorial ya tiene la configuración necesaria en el archivo *Dockerfile* que instala el servidor SSH y que también establece las credenciales de inicio de sesión. Esta sección solo es meramente informativa. Para conectarse al contenedor, vaya a la sección siguiente.

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Esta configuración no permite realizar conexiones externas al contenedor. SSH solo está disponible en el sitio de Kudu/SCM. El sitio de Kudu/SCM se autentica con la cuenta de Azure.
> root:Docker! no se debe modificar SSH. SCM/KUDU usará las credenciales de Azure Portal. Al cambiar este valor, se producirá un error al usar SSH.

El archivo *Dockerfile* también copia el archivo *sshd_config*  en la carpeta */etc/ssh/* y expone el puerto 2222 en el contenedor:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Se trata de un puerto interno al que solo pueden acceder los contenedores que se encuentren en la red puente de una red privada virtual. 

Por último, el script de entrada, *init.sh*, inicia el servidor SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Apertura de una conexión SSH a un contenedor

1. Vaya a `https://<app-name>.scm.azurewebsites.net/webssh/host` e inicie sesión con su cuenta de Azure. Reemplace `<app-name>` por el nombre de la aplicación web.

1. Una vez que haya iniciado sesión, se le redirigirá a una página de información de la aplicación web. Seleccione **SSH** en la parte superior de la página para abrir el shell y usar los comandos.

    Por ejemplo, puede examinar los procesos que se ejecutan dentro de él mediante el comando `top`.
    
## <a name="clean-up-resources"></a>Limpieza de recursos

Los recursos que creó en este artículo pueden incurrir en costos continuos. Para limpiar los recursos, solo tiene que eliminar el grupo de recursos que los contenga:

```azurecli
az group delete --name myResourceGroup
```

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

¿Qué ha aprendido?

::: zone pivot="container-windows"

> [!div class="checklist"]
> * Implementar una imagen personalizada en un registro de contenedor privado
> * Ejecutar la imagen personalizada en App Service
> * Actualizar y volver a implementar la imagen
> * Acceso a los registros de diagnóstico
> * Conexión al contenedor con SSH

::: zone-end

::: zone pivot="container-linux"

> [!div class="checklist"]
> * Insertar una imagen personalizada de Docker en Azure Container Registry
> * Implementar la imagen personalizada en App Service
> * Configuración de las variables de entorno
> * Extraer la imagen en App Service mediante una identidad administrada
> * Acceso a los registros de diagnóstico
> * Habilitar CI/CD en Azure Container Registry para App Service
> * Conexión al contenedor con SSH

::: zone-end


Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a la aplicación.

> [!div class="nextstepaction"]
> [Tutorial: Asignar un nombre DNS personalizado a la aplicación](app-service-web-tutorial-custom-domain.md)

O bien, eche un vistazo a otros recursos:

> [!div class="nextstepaction"]
> [Configuración de un contenedor personalizado](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Tutorial: Aplicación de WordPress con varios contenedores](tutorial-multi-container-app.md)
::: zone-end
