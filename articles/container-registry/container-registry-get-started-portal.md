---
title: 'Inicio rápido: Creación de un registro en el portal'
description: Aprenda rápidamente a crear un registro de contenedor privado de Azure con Azure Portal.
ms.date: 06/23/2021
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
- contperf-fy21q4
ms.openlocfilehash: 51531b6af5babe256ec89079c7705cfea71ef52a
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895947"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Inicio rápido: Creación de un instancia de Azure Container Registry mediante Azure Portal

Azure Container Registry es un servicio de registro privado para compilar, almacenar y proporcionar imágenes de contenedor y artefactos relacionados. En este inicio rápido, creará una instancia de Azure Container Registry con Azure Portal. A continuación, utilice los comandos de Docker para insertar una imagen de contenedor en el registro y, finalmente, extraiga y ejecute la imagen desde el registro.

Para iniciar sesión en el registro y trabajar con imágenes de contenedor, este inicio rápido requiere que se ejecute la CLI de Azure (se recomienda la versión 2.0.55 o posterior). Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli].

También debe tener instalado Docker localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

Seleccione **Crear un recurso** > **Contenedores** > **Registro de contenedor**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Ir al registro de contenedor en el portal":::

En la pestaña **Aspectos básicos**, escriba valores para **Grupo de recursos** y **Nombre del Registro**. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Para esta guía de inicio rápido, cree un grupo de recursos en la ubicación `West US` denominado `myResourceGroup`y en **SKU**, seleccione "Básico".

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Creación de un registro de contenedor en el portal":::

Acepte los valores predeterminados para la configuración restante. Después, seleccione **Revisar y crear**. Después de revisar la configuración, seleccione **Crear**.

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

Cuando aparezca el mensaje **Implementación correcta**, seleccione el registro de contenedor en el portal. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Información general del registro de contenedor en el portal":::

Anote el nombre del registro y el valor del **Servidor de inicio de sesión**, que es un nombre completo que termina en `azurecr.io` en la nube de Azure. Estos valores se usan en los pasos siguientes cuando inserta y extrae imágenes con Docker.

## <a name="log-in-to-registry"></a>Iniciar sesión en el registro

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia del registro. [Inicie sesión en la CLI de Azure][get-started-with-azure-cli] en la máquina local y, a continuación, ejecute el comando [az acr login][az-acr-login]. Especifique solo el nombre del recurso de registro al iniciar sesión con la CLI de Azure. No use el nombre completo del servidor de inicio de sesión.

```azurecli
az acr login --name <registry-name>
```

Ejemplo:

```azurecli
az acr login --name mycontainerregistry
```

El comando devolverá `Login Succeeded` una vez completado. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Lista de imágenes de contenedor

Para mostrar las imágenes en el registro, vaya al registro en el portal, seleccione **Repositorios** y después seleccione el repositorio **hello-world** que creó con `docker push`.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Lista de imágenes de contenedor en el portal":::

Al seleccionar el repositorio **hello-world**, verá la imagen `v1`-tagged en **Etiquetas**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos, vaya al grupo de recursos **myResourceGroup** en el portal. Una vez cargado el grupo de recursos, haga clic en **Eliminar grupo de recursos** para eliminar el grupo de recursos, el registro de contenedor y las imágenes de contenedor almacenadas allí.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Eliminación de un grupo de recursos en el portal":::


## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado una instancia de Azure Container Registry con Azure Portal, ha insertado una imagen de contenedor y ha extraído y ejecutado la imagen del registro. Siga los tutoriales de Azure Container Registry para información más detallada de ACR.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Registry Tasks][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
