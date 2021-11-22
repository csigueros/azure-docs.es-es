---
title: 'Inicio rápido: Implementación de la primera aplicación de contenedor mediante el Azure Portal'
description: Implemente la primera aplicación en la versión preliminar de Azure Container Apps mediante Azure Portal.
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: v-bcatherine
ms.custom: ''
ms.openlocfilehash: e2f2bf2cafc60bca637bee3309f4b721965f30fd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710287"
---
# <a name="quickstart-deploy-your-first-container-app-using-the-azure-portal"></a>Inicio rápido: Implementación de la primera aplicación de contenedor mediante el Azure Portal

La versión preliminar de Azure Container Apps permite ejecutar microservicios y aplicaciones contenedorizadas en una plataforma sin servidor. Con Container Apps, puede disfrutar de las ventajas de ejecutar contenedores y, al mismo tiempo, dejar atrás las preocupaciones de configurar manualmente la infraestructura en la nube y orquestadores de contenedores complejos.

En este inicio rápido, cree un entorno seguro de Container Apps e implemente su primera aplicación de contenedor mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Se requiere una cuenta de Azure con una suscripción activa. Si no la tiene, puede [crear una cuenta gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="setup"></a>Configurar

Para empezar, inicie sesión en [Azure Portal](https://portal.azure.com).
<!--
Do we need to include steps to login?  Probably not..
-->

## <a name="create-a-container-app"></a>Crear una aplicación de contenedor

Para crear una aplicación contenedora, comience en la página principal de Azure Portal.

1. Busque **Aplicaciones de contenedor** en la barra de búsqueda superior.
1. Busque **Aplicaciones de contenedor** en los resultados de la búsqueda.
1. Seleccione el botón **Crear**.

### <a name="basics-tab"></a>Pestaña Aspectos básicos

En la pestaña *Aspectos básicos*, realice las acciones siguientes.

#### <a name="enter-project-details"></a>Especificación de los detalles del proyecto

| Configuración | Acción |
|---|---|
| Subscription | Seleccione su suscripción a Azure. |
| Resource group | Seleccione **Crear nuevo** y escriba **my-container-apps**. |
| Nombre de la aplicación de contenedor |  Escriba **my-container-app**. |

#### <a name="create-an-environment"></a>Creación de un entorno
 
1. En el campo *Create Container App environment* (Crear entorno de aplicación de contenedor), seleccione **Create new** (Crear nuevo).
1. En la página *Create Container App environment* (Crear entorno de aplicación de contenedor), en la pestaña *Basics* (Aspectos básicos), escriba los siguientes valores:

    | Configuración | Value |
    |---|---|
    | Nombre del entorno | Escriba **my-environment**. | 
    | Region | Seleccione **Centro de Canadá**. |

1. Seleccione la pestaña **Supervisión** para crear un área de trabajo de Log Analytics.
1. Seleccione **Crear nuevo** en el campo *Área de trabajo de Log Analytics*.
1. Escriba **my-container-apps-logs** en el campo *Nombre* del cuadro de diálogo *Create new Log Analytics Workspace* (Crear área de trabajo de Log Analytics).
  
    El campo *Ubicación* se ha rellenado previamente con *Centro de Canadá* de forma automática.
1. Seleccione **Aceptar**.
1. Seleccione el botón **Crear** en la parte inferior de la página *Create Container App environment* (Crear entorno de aplicación de contenedor).
1. Haga clic en el botón **Siguiente: Configuración de aplicaciones** en la parte inferior de la página.

### <a name="app-settings-tab"></a>Pestaña Configuración de aplicaciones

En la pestaña *Configuración de aplicaciones*, realice las siguientes acciones:

| Configuración | Acción |
|---|---|
| Use quickstart image (Uso de una imagen de inicio rápido) | **Anule la activación** de la casilla. |
| Name | Escriba **my-app**. <!-- I don't know what name to use --> |
| Origen de la imagen | Seleccione **Docker Hub u otros registros**. |
| Tipo de imagen | Seleccione **Público**. |
| Registry login server (Servidor de inicio de sesión de registro) | Escriba `mcr.microsoft.com`. |  
| Image and tag (Imagen y etiqueta) | Escriba **/azuredocs/containerapps-helloworld:latest**. |

#### <a name="application-ingress-settings"></a>Configuración de entrada de la aplicación

| Configuración | Acción |
|---|---|
| Entrada | Seleccione **Habilitado**. |
| Visibilidad de la entrada | Seleccione **Externo**. |
| Puerto de destino | Escriba **80**. |

### <a name="deploying-the-container-app"></a>Implementación de la aplicación de contenedor

1. Seleccione el botón **Revisar y crear** de la parte inferior de la página.  

    A continuación, se comprueba la configuración de la aplicación de contenedor. Si no se encuentran errores, se habilita el botón *Crear*.  

    Si hay errores, todas las pestañas que contengan errores se marcan con un punto rojo.  Vaya a la pestaña adecuada. Los campos que contienen un error se resaltarán en rojo.  Una vez corregidos todos los errores, vuelva a seleccionar **Revisar y crear**.

1. Seleccione **Crear**.

    Se muestra una página con el mensaje *Implementación en curso*.  Una vez completada correctamente la implementación, verá el mensaje *Se completó la implementación*.

### <a name="view-your-deployed-application"></a>Visualización de la aplicación implementada

Seleccione **Ir al recurso** para ver la aplicación de contenedor.  Seleccione el vínculo que hay junto a *URL de aplicación* para ver la aplicación. Verá el siguiente mensaje en el explorador.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Primera implementación de Azure Container Apps.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminar la instancia de Azure Container Apps y todos los servicios asociados quitando el grupo de recursos.

1. Seleccione el grupo de recursos **my-container-apps** en la sección *Información general*.
1. Seleccione el botón **Eliminar grupo de recursos** en la parte superior de la página del grupo de recursos *Información general*.
1. Escriba el nombre del grupo de recursos **my-container-apps** en el cuadro de diálogo de confirmación *¿Está seguro de que desea eliminar "my-container-apps"?* .
1. Seleccione **Eliminar**.  
    El proceso para eliminar el grupo de recursos puede tardar unos minutos en completarse.


> [!TIP]
> ¿Tiene problemas? Háganoslo saber en GitHub abriendo una incidencia en el [repositorio de Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Entornos en Azure Container Apps](environment.md)
