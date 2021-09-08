---
title: Administración del contenido público en el registro de contenedor privado
description: Procedimientos y flujos de trabajo en Azure Container Registry para administrar las dependencias de las imágenes públicas de Docker Hub y otro contenido público
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 06/17/2021
ms.openlocfilehash: 806cb6b49824db65744bc653c6c467c7a816a21e
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983705"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Administración del contenido público con Azure Container Registry

Este artículo es una introducción a los procedimientos y flujos de trabajo para usar un registro local, como un [registro de contenedor de Azure](container-registry-intro.md) para mantener copias del contenido público, como las imágenes de contenedor de Docker Hub. 


## <a name="risks-with-public-content"></a>Riesgos con el contenido público

El entorno puede tener dependencias del contenido público, como imágenes de contenedor público, [gráficos Helm](https://helm.sh/), directivas de [agente de directiva abierta](https://www.openpolicyagent.org/) (OPA) u otros artefactos. Por ejemplo, podría ejecutar [nginx](https://hub.docker.com/_/nginx) para el enrutamiento de servicio o `docker build FROM alpine` mediante la extracción de imágenes directamente de Docker Hub u otro registro público. 

Sin los controles adecuados, el hecho de tener dependencias del contenido del registro público puede introducir riesgos en los flujos de trabajo de desarrollo e implementación de imágenes. Para mitigar estos riesgos, mantenga copias locales del contenido público siempre que sea posible. Para más información, consulte el [blog de Open Container Iniciative](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Autenticación con Docker Hub

Como primer paso, si actualmente extrae imágenes públicas de Docker Hub como parte de un flujo de trabajo de compilación o de implementación, se recomienda [autenticarse con una cuenta de Docker Hub](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) en lugar de realizar una solicitud de incorporación de cambios anónima.

Al realizar solicitudes de incorporación de cambios anónimas frecuentes, es posible que vea errores de Docker similares a `ERROR: toomanyrequests: Too Many Requests.` o `You have reached your pull rate limit.`. Autentíquese en Docker Hub para evitar estos errores.

> [!NOTE]
> Desde el 2 de noviembre de 2020, se aplican [límites de frecuencia de descarga](https://docs.docker.com/docker-hub/download-rate-limit) a las solicitudes anónimas y autenticadas a Docker Hub desde cuentas del plan gratuito de Docker y se aplican por dirección IP e identificador de Docker, respectivamente. 
>
> Al estimar el número de solicitudes de incorporación de cambios, tenga en cuenta que, si se usan servicios de proveedores de nube o se trabaja detrás de un proceso NAT corporativo, se presentarán varios usuarios a Docker Hub de forma agregada como un subconjunto de direcciones IP. La adición de autenticación de cuentas de pago de Docker a las solicitudes realizadas a Docker Hub evitará posibles interrupciones del servicio debido a la limitación de velocidad.
>
> Para más información, consulte [Precios y suscripciones de Docker](https://www.docker.com/pricing) y [Términos de servicio de Docker](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Token de acceso de Docker Hub

Al autenticarse en Docker Hub, este admite [tokens de acceso personal](https://docs.docker.com/docker-hub/access-tokens/) como alternativa a las contraseñas de Docker. Se recomienda el uso de tokens para los servicios automatizados que extraen imágenes de Docker Hub. Puede generar varios tokens para distintos usuarios o servicios, así como revocar los tokens cuando ya no los necesite.

Para autenticarse con `docker login` mediante un token, omita la contraseña en la línea de comandos. Cuando se le solicite una contraseña, escriba el token en su lugar. Si habilitó la autenticación en dos fases para la cuenta de Docker Hub, debe usar un token de acceso personal al iniciar sesión desde la CLI de Docker.

### <a name="authenticate-from-azure-services"></a>Autenticación desde servicios de Azure

Varios servicios de Azure, como App Service y Azure Container Instances, admiten la extracción de imágenes de registros públicos, como es el caso de Docker Hub para implementaciones de contenedores. Si necesita implementar una imagen de Docker Hub, se recomienda que configure las opciones para autenticarse con una cuenta de Docker Hub. Ejemplos:

**App Service**

* **Origen de imagen:** Docker Hub
* **Acceso al repositorio**: Privada
* **Inicio de sesión**: \<Docker Hub username>
* **Contraseña**: \<Docker Hub token>

Para más información, consulte [Extracciones autenticadas de Docker Hub en App Service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Origen de imagen:** Docker Hub u otro registro
* **Tipo de imagen**: Privada
* **Servidor de inicio de sesión del registro de imágenes**: docker.io
* **Nombre de usuario del registro de imágenes**: \<Docker Hub username>
* **Contraseña del registro de imágenes**: \<Docker Hub token>
* **Imagen**: docker.io/\<repo name\>:\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importación de imágenes en una instancia de Azure Container Registry
 
Para empezar a administrar copias de imágenes públicas, puede crear un registro de contenedor de Azure si aún no tiene uno. Cree un registro con la [CLI de Azure](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) u otras herramientas. 

Como paso único recomendado, [importe](container-registry-import-images.md) imágenes base y otro contenido público en la instancia de Azure Container Registry. El comando [az acr import](/cli/azure/acr#az_acr_import) de la CLI de Azure admite la importación de imágenes de registros públicos, como Docker Hub y Microsoft Container Registry, y desde otros registros de contenedores privados. 

`az acr import` no requiere una instalación local de Docker. Puede ejecutarlo con una instalación local de la CLI de Azure o directamente en Azure Cloud Shell. Admite imágenes de cualquier tipo de sistema operativo, imágenes de varias arquitecturas o artefactos OCI, como los gráficos Helm.

Ejemplo:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

En función de las necesidades de su organización, puede realizar la importación en un registro dedicado o en un repositorio de un registro compartido.

## <a name="update-image-references"></a>Actualización de referencias de imágenes

Los desarrolladores de imágenes de aplicación deben asegurarse de que el código haga referencia al contenido local bajo su control.

* Actualice las referencias de las imágenes para que usen el registro privado. Por ejemplo, actualice una instrucción `FROM baseimage:v1` en un Dockerfile a `FROM myregistry.azurecr.io/mybaseimage:v1`
* Configure las credenciales o un mecanismo de autenticación para usar el registro privado. El mecanismo exacto depende de las herramientas que use para acceder al registro y de cómo administre el acceso de los usuarios.
    * Si usa un clúster de Kubernetes o Azure Kubernetes Service para acceder al registro, consulte los [escenarios de autenticación](authenticate-kubernetes-options.md).
    * Más información sobre las [opciones para autenticarse](container-registry-authentication.md) con Azure Container Registry.

## <a name="automate-application-image-updates"></a>Automatización de las actualizaciones de imágenes de aplicación

Para ampliar la importación de imágenes, configure una [tarea de Azure Container Registry](container-registry-tasks-overview.md) para automatizar las compilaciones de imágenes de aplicación cuando se actualizan las imágenes base. Una tarea de compilación automatizada puede realizar un seguimiento de las [actualizaciones de la imagen base](container-registry-tasks-base-images.md) y las [actualizaciones del código fuente](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Para ver un ejemplo detallado, consulte [Consumo y mantenimiento de contenido público con Azure Container Registry Tasks](tasks-consume-public-content.md). 

> [!NOTE]
> Una sola tarea preconfigurada puede recompilar automáticamente cada imagen de aplicación que haga referencia a una imagen base dependiente. 
 
## <a name="next-steps"></a>Pasos siguientes
* Más información sobre [ACR Tasks](container-registry-tasks-overview.md) para compilar, ejecutar, insertar y revisar imágenes de contenedor en Azure.
* Consulte [Consumo y mantenimiento de contenido público con Azure Container Registry Tasks](tasks-consume-public-content.md) para ver un flujo de trabajo de acceso automatizado para actualizar las imágenes de su entorno. 
* Consulte los [tutoriales de ACR Tasks](container-registry-tutorial-quick-task.md) para ver más ejemplos de automatización de compilaciones y actualizaciones de imágenes.
