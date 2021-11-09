---
title: 'Tutorial: Creación de una canalización de CI/CD para el código existente mediante Azure DevOps Starter'
description: Azure DevOps Starter facilita la introducción a Azure. DevOps Projects le ayuda, con pocos pasos rápidos, a usar su propio código y repositorio de GitHub para iniciar una aplicación en un servicio de Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: gwallace
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.custom: mvc
ms.openlocfilehash: 9600dcc2c9cbd150783ed1a14afb2b785ba5b054
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056347"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Creación de una canalización de CI/CD para el repositorio de GitHub con Azure DevOps Starter

Azure DevOps Starter presenta un proceso simplificado para crear una canalización de integración continua (CI) y entrega continua (CD) en Azure. Puede traer el código y el repositorio de Git existentes, o bien seleccionar una aplicación de ejemplo.

Podrá:

> [!div class="checklist"]
> * Usar DevOps Starter para crear una canalización de CI/CD
> * Configurar el acceso a un repositorio de GitHub y elegir un marco
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Confirmar cambios en GitHub e implementarlos automáticamente en Azure
> * Examen de la canalización de CI/CD de Azure Pipelines
> * Limpieza de recursos

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Puede obtener una gratuita mediante [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acceda a un repositorio de GitHub o a un repositorio de Git externo que contenga código .NET, Java, PHP, Node.js, Python o código web estático.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Azure DevOps Starter crea una canalización de CI/CD en Azure Pipelines. Puede crear una organización de Azure DevOps nueva o usar una existente. Azure DevOps Starter también crea recursos de Azure en la suscripción de Azure que prefiera.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda, escriba **DevOps Starter** y, después, selecciónelo. Haga clic en **Agregar** para crear un recurso.

    ![Panel de DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Seleccione **Traiga su propio código** y, después, **Siguiente**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Configuración del acceso al repositorio de GitHub y selección de un marco de trabajo

1. Seleccione **GitHub** o un repositorio de código de **Git** externo. Para este tutorial, seleccione **GitHub**. Es posible que se le pida que se autentique en GitHub la primera vez, para que Azure pueda acceder al repositorio de GitHub.

1. Seleccione un **Repositorio** y una **Rama** y, a continuación, seleccione **Siguiente**.

1. Si usa contenedores de Docker, cambie **¿Está dockerizada la aplicación?** a **Sí**. En este tutorial, deje **No** seleccionado y, a continuación, seleccione **Siguiente**. Para más información sobre el uso de contenedores de Docker, mantenga el puntero sobre el icono **i**.

   ![Selección del marco de trabajo de la aplicación en el menú desplegable](_img/azure-devops-project-github/appframework.png)

1. En los menús desplegables, seleccione un **entorno de ejecución de la aplicación** y un **marco de trabajo de la aplicación** y, a continuación, seleccione **Siguiente**. El marco de trabajo de la aplicación determina el tipo de destino de implementación en un servicio de Azure disponible.

1. Seleccione un **servicio de Azure** para implementar la aplicación y, después, seleccione **Siguiente**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configuración de Azure DevOps y una suscripción de Azure

1. Escriba un valor en **Nombre del proyecto**.

1. Cree una nueva organización gratuita en **Organización de Azure DevOps** o seleccione una organización existente en el menú desplegable.

1. Seleccione la suscripción en **Suscripción a Azure** y, después, escriba un nombre en **Aplicación web** o use el valor predeterminado. Seleccione un valor en **Ubicación** y, después, **Listo**. En unos minutos, se muestra la información general de la implementación de DevOps Starter en Azure Portal.

1. Seleccione **Ir al recurso** para ver el panel de DevOps Starter. En la esquina superior derecha, ancle el **proyecto** al panel para un acceso rápido. Azure DevOps Starter configura automáticamente una compilación de integración continua y un desencadenador de versión. El código permanece en el repositorio de GitHub o en otro repositorio externo; además, se configura una aplicación de ejemplo en un repositorio de la organización definida en **Organización de Azure DevOps**. Azure DevOps Starter ejecuta la compilación e implementa la aplicación en Azure.

   ![Vista del panel de Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. El panel muestra el repositorio de código, la canalización de CI/CD y la aplicación en Azure. A la derecha, en Recursos de Azure, seleccione **Examinar** para ver la aplicación en ejecución.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Confirmar cambios en GitHub e implementarlos automáticamente en Azure

Ahora está listo para colaborar en la aplicación con un equipo. El proceso de CI/CD implementa automáticamente el trabajo más reciente en el sitio web. Cada cambio que se realiza en el repositorio de GitHub inicia una compilación en Azure DevOps; además, una canalización de CD ejecuta una implementación en Azure.

1. En el panel de DevOps Starter, seleccione **Repositorios**. El repositorio de GitHub se abre en una nueva pestaña del explorador. Realice un cambio en la aplicación y seleccione **Confirmar cambios**.

1. Poco después se inicia una compilación en Azure Pipelines. Puede supervisar el estado de la compilación en el panel de DevOps Starter. También puede supervisarlo desde la organización de Azure DevOps si selecciona la pestaña **Canalizaciones de compilación** en el panel de DevOps Starter.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examen de la canalización de CI/CD de Azure Pipelines

Azure DevOps Starter configura automáticamente una canalización de CI/CD en Azure Pipelines. Explore y personalice la canalización según sea necesario. Para familiarizarse con las canalizaciones de compilación y de versión, siga estos pasos:

1. En el panel de DevOps Starter, seleccione **Canalizaciones de compilación**.

1. Una vez abierta la página de **Azure Pipelines**, verá un historial de las compilaciones más recientes y el estado de cada una de ellas.

   ![Página Compilaciones de Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. En la esquina superior derecha de la página **Compilaciones**, puede seleccionar **Editar**, para cambiar la compilación actual, **Cola**, para agregar una nueva compilación, o el botón de puntos suspensivos verticales (**&#8942;**), para abrir un menú con más opciones. Seleccione **Editar**.

1. La compilación lleva a cabo varias tareas, como capturar código fuente del repositorio, restaurar dependencias y publicar salidas para implementaciones. A la derecha, debajo de **Nombre**, cambie el nombre de la canalización de compilación por uno más descriptivo. Seleccione **Guardar y poner en cola** y, después, seleccione **Guardar**. Escriba un comentario y, a continuación, seleccione **Guardar** de nuevo.

   ![Página Compilaciones de Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Seleccione el panel **Historial** para ver un registro de auditoría de los cambios recientes en la compilación.  Azure DevOps realiza el seguimiento de los cambios realizados en la canalización de compilación y permite comparar las versiones.

1. Seleccione la pestaña **Desencadenadores**. Azure DevOps Projects crea automáticamente un desencadenador de CI con alguna configuración predeterminada. Puede establecer desencadenadores, como **Habilitar la integración continua** para ejecutar una compilación cada vez que confirme un cambio de código. También puede establecer desencadenadores para programar la ejecución de compilaciones en momentos concretos.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite la instancia de Azure App Service y los recursos relacionados que creó en este tutorial, puede eliminarlos. Use la funcionalidad de **eliminación** del panel de DevOps Projects.

## <a name="next-steps"></a>Pasos siguientes

Cuando configuró el proceso de CI/CD en este tutorial, creó automáticamente una canalización de compilación y una canalización de versión en Azure DevOps Projects. Puede modificar estas canalizaciones de compilación y de versión para satisfacer las necesidades de su equipo.

Para más información acerca de la canalización de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definición de la canalización de implementación continua (CD) en varias fases](/azure/devops/pipelines/release/define-multistage-release-process)

Para más información sobre la supervisión de aplicaciones, consulte:
  
 > [!div class="nextstepaction"]
 > [¿Qué es Azure Monitor?](../azure-monitor/overview.md)