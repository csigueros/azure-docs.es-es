---
title: Implementación de aplicaciones de ASP.NET Core en Azure Kubernetes Service con Azure DevOps Starter
description: Azure DevOps Starter facilita la introducción a Azure. Con DevOps Starter, puede implementar una aplicación de ASP.NET Core con Azure Kubernetes Service (AKS) en pocos pasos.
ms.author: gwallace
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.openlocfilehash: 302508d1575dada30d2cee8f65381153ef22d55b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053148"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>Implementación de aplicaciones de ASP.NET Core en Azure Kubernetes Service con Azure DevOps Starter

Azure DevOps Starter ofrece una experiencia simplificada en la que puede utilizar su código existente y el repositorio de Git, o elegir una aplicación de ejemplo para crear una canalización de integración continua (CI) y entrega continua (CD) en Azure. 

DevOps Starter también:

* Crea automáticamente recursos de Azure, como Azure Kubernetes Service (AKS).
* Crea y configura una canalización de versión en Azure DevOps que configura una canalización de compilación y de versión de CI/CD.
* Crea un recurso de Azure Application Insights para la supervisión.
* Habilita [Azure Monitor para contenedores](../azure-monitor/containers/container-insights-overview.md) para supervisar el rendimiento de las cargas de trabajo del contenedor en el clúster de AKS

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Usar DevOps Starter para implementar una aplicación de ASP.NET Core en AKS
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Examen del clúster de AKS
> * Examen de la canalización de CI
> * Examen de la canalización de CD
> * Confirmación de los cambios en Git y su implementación automática en Azure
> * Limpieza de recursos

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Puede obtener una gratuita mediante [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>Usar DevOps Starter para implementar una aplicación de ASP.NET Core en AKS

DevOps Starter crea una canalización de CI/CD en Azure Pipelines. Puede crear una organización de Azure DevOps nueva o usar una existente. DevOps Starter también crea recursos de Azure, como un clúster de AKS, en la suscripción de Azure que prefiera.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda, escriba **DevOps Starter** y, después, selecciónelo. Haga clic en **Agregar** para crear un recurso.

    ![Panel de DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Seleccione **.NET** y después **Siguiente**.

1. En **Elegir un marco de trabajo de la aplicación**, seleccione **ASP.NET Core** y, después, seleccione **Siguiente**.

1. Seleccione **Servicio de Kubernetes** y, después, **Siguiente**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configuración de Azure DevOps y una suscripción de Azure

1. Cree una organización de Azure DevOps nueva o seleccione una existente. 

1. Escriba el nombre del proyecto de Azure DevOps. 

1. Seleccione su suscripción a Azure.

1. Para ver más valores de configuración de Azure e identificar el número de nodos del clúster de AKS, seleccione **Cambiar**. Este panel muestra varias opciones para configurar el tipo y ubicación de los servicios de Azure.
 
1. Salga del área de configuración de Azure y seleccione **Listo**. Pocos minutos después el proceso se ha completado. Una aplicación ASP.NET Core de ejemplo se configura en un repositorio de Git de su organización de Azure DevOps, se crea un clúster de AKS, se ejecuta una canalización de CI/CD y la aplicación se implementa en Azure. 

    Cuando todo esto finaliza, se muestra el panel de Azure DevOps Starter en Azure Portal. También puede ir al panel de DevOps Starter directamente desde la opción **Todos los recursos** de Azure Portal. 

    Este panel proporciona visibilidad del repositorio de código de Azure DevOps, la canalización de CI/CD y el clúster de AKS. En la canalización de Azure DevOps se pueden configurar otras opciones de CI/CD. A la derecha, seleccione **Examinar** para ver la aplicación en ejecución.

## <a name="examine-the-aks-cluster"></a>Examen del clúster de AKS

DevOps Starter configura automáticamente un clúster de AKS, que puede explorar y personalizar. Para familiarizarse con el clúster de AKS, siga estos pasos:

1. Vaya al panel de inicio de DevOps Starter.

1. A la derecha, seleccione el servicio AKS. Se abre un panel para el clúster de AKS. Desde esta vista puede realizar varias acciones, como supervisar el mantenimiento del contenedor, buscar registros y abrir el panel de Kubernetes.

1. A la derecha, seleccione **Ver el panel de Kubernetes**. Opcionalmente, siga los pasos para abrir el panel de Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Examen de la canalización de CI

DevOps Starter configura automáticamente una canalización de CI/CD de Azure completa en su organización de Azure DevOps. Puede explorar y personalizar la canalización. Para familiarizarse con ella, siga estos pasos:

1. Vaya al panel de inicio de DevOps Starter.

1. En la parte superior del panel de DevOps Starter, seleccione **Compilar canalizaciones**.  Una pestaña del explorador muestra la canalización de compilación del nuevo proyecto.

1. Elija el campo **Estado** y seleccione los puntos suspensivos (...).  Un menú muestra varias opciones, como poner en cola una nueva compilación, poner en pausa una compilación y editar la canalización de compilación.

1. Seleccione **Editar**.

1. En este panel puede examinar las distintas tareas de la canalización de compilación. La compilación ejecuta varias tareas, como capturar códigos fuente del repositorio Git, restaurar dependencias y publicar las salidas usadas para implementaciones.

1. En la parte superior de la canalización de compilación, seleccione el nombre de esta.

1. Cambie el nombre de la canalización de compilación por otro más descriptivo, seleccione **Guardar y poner en cola** y, luego, **Guardar**.

1. En el nombre de la canalización de compilación, seleccione **Historial**. Este panel muestra un registro de auditoría de los cambios recientes de la compilación. Azure DevOps realiza un seguimiento de los cambios realizados en la canalización de compilación y permite comparar las versiones.

1. Seleccione **Desencadenadores**. DevOps Starter crea automáticamente un desencadenador de integración continua y cada confirmación al repositorio inicia una compilación. Opcionalmente, puede elegir incluir o excluir ramas del proceso de integración continua.

1. Seleccione **Retención**. En función del escenario, puede especificar directivas para conservar o quitar un determinado número de compilaciones.

## <a name="examine-the-cd-release-pipeline"></a>Examen de la canalización de versión de la implementación continua

DevOps Starter crea y configura automáticamente los pasos necesarios para implementar desde la organización de Azure DevOps en la suscripción de Azure. Dichos pasos incluyen la configuración de una conexión de servicio de Azure para realizar la autenticación de Azure DevOps en la suscripción de Azure. La automatización también crea una canalización de versión, lo que proporciona la implementación continua en Azure. Para más información acerca de la canalización de versión, siga estos pasos:

1. Seleccione **Compilación y versión** y, después, **Versiones**.  DevOps Starter crea una canalización de versión para administrar implementaciones en Azure.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a la canalización de versión, y, después, **Editar**. La canalización de versión contiene una *canalización*, que define el proceso de lanzamiento.

1. En **Artefactos**, seleccione **Colocar**. La canalización de compilación que ha examinado en los pasos anteriores genera la salida que se usa para el artefacto. 

1. A la derecha del icono **Colocar**, seleccione **Desencadenador de implementación continua**. Esta canalización de versión tiene un desencadenador de implementación continua habilitado, que ejecuta una implementación cada vez que hay un nuevo artefacto de compilación disponible. Opcionalmente, puede deshabilitar el desencadenador, con lo que las implementaciones van a requerir una ejecución manual. 

1. A la derecha, seleccione **Ver versiones** para mostrar un historial de las versiones.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a una de las versiones y, después, **Abrir**. Puede explorar varios menús, como un resumen de las versiones, elementos de trabajo asociados y las pruebas.

1. Seleccione **Confirmaciones**. Esta vista muestra las confirmaciones de código que están asociadas a esta implementación. Compare las versiones para ver las diferencias de confirmación entre las implementaciones.

1. Seleccione **Registros**. Los registros contienen información útil sobre el proceso de implementación. Se pueden ver tanto durante las implementaciones como después de ellas.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure 

 > [!NOTE]
 > El siguiente procedimiento prueba la canalización de CI/CD. Para ello, realiza un cambio de texto simple.

A partir de ese momento ya puede empezar a colaborar con un equipo en una aplicación mediante el uso de un proceso de CI/CD que implemente automáticamente el trabajo más reciente en su sitio web. Cada cambio que se realiza en el repositorio de Git inicia una compilación en Azure DevOps y una canalización de CD ejecuta una implementación en Azure. Siga el procedimiento descrito en esta sección o utilice otra técnica para confirmar los cambios en el repositorio. Por ejemplo, puede clonar el repositorio de Git en su herramienta favorita o IDE, y luego insertar los cambios en este repositorio.

1. En el menú de Azure DevOps, seleccione **Código** > **Archivos** y, a continuación, vaya al repositorio.

1. Vaya al directorio *Views\Home*, seleccione los puntos suspensivos (...) que hay junto al archivo *Index.cshtml* y seleccione **Editar**.

1. Realice un cambio en el archivo, como agregar texto dentro de una de las etiquetas div. 

1. En la parte superior derecha, seleccione **Confirmar** y, después, seleccione **Confirmar** de nuevo para insertar el cambio. Tras unos instantes, se inicia una compilación se inicia en Azure DevOps y se ejecuta una versión para implementar los cambios. Supervise el estado de la compilación en el panel de DevOps Starter o en el explorador con la organización de Azure DevOps.

1. Una vez que complete la versión, actualice la aplicación para comprobar los cambios.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a realizar pruebas, limpie los recursos para que no se acumulen costos de facturación. Cuando dejen de ser necesarios, puede eliminar el clúster de AKS y los recursos relacionados que ha creado en este tutorial. Para ello, utilice la funcionalidad de **eliminación** del panel de DevOps Starter.

> [!IMPORTANT]
> El siguiente procedimiento elimina permanentemente los recursos. La funcionalidad de *Eliminación* destruye los datos que crea el proyecto en DevOps Starter tanto en Azure como en Azure DevOps y no se podrán recuperar. Utilice este procedimiento cuando haya leído detenidamente las indicaciones.

1. En Azure Portal, vaya al panel de DevOps Starter.
1. En la parte superior derecha, seleccione **Eliminar**. 
1. En el mensaje, seleccione **Sí** para *eliminar permanentemente* los recursos.

## <a name="next-steps"></a>Pasos siguientes

Si lo desea, puede modificar estas canalizaciones de compilación y de versión para satisfacer las necesidades de su equipo. También puede usar este patrón de CI/CD como plantilla para las demás canalizaciones. En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Usar DevOps Starter para implementar una aplicación de ASP.NET Core en AKS
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Examen del clúster de AKS
> * Examen de la canalización de CI
> * Examen de la canalización de CD
> * Confirmación de los cambios en Git y su implementación automática en Azure
> * Limpieza de recursos

Para más información acerca del uso del panel de Kubernetes, consulte:

> [!div class="nextstepaction"]
> [Uso del panel de Kubernetes](../aks/kubernetes-dashboard.md)