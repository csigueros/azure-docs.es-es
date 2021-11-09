---
title: 'Inicio rápido: Creación de una canalización de CI/CD para Ruby on Rails con Azure DevOps Starter'
description: Azure DevOps Starter facilita la introducción a Azure. En pocos y rápidos pasos puede iniciar una aplicación web de Ruby en un servicio de Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: georgewallace
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 4ada4087555504d7bef052c85053e2815b1d77a6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061577"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-starter"></a>Creación de una canalización de CI/CD para Ruby on Rails con Azure DevOps Starter

Configure la integración continua (CI) y la entrega continua (CD) para la aplicación Ruby on Rails mediante Azure DevOps Starter. DevOps Starter simplifica la configuración inicial de una canalización de compilación y de versión de Azure DevOps.

Si no tiene una suscripción de Azure, puede obtener una gratuita mediante [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Azure DevOps Starter crea una canalización de CI/CD en Azure Repos. Puede crear una organización de Azure DevOps nueva o usar una existente. DevOps Starter también crea recursos de Azure en la suscripción de Azure que prefiera.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda, escriba **DevOps Starter** y, después, selecciónelo. Haga clic en **Agregar** para crear un recurso.

    ![Panel de DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-app-and-azure-service"></a>Selección de una aplicación de ejemplo y un servicio de Azure

1. Seleccione la aplicación de ejemplo de **Ruby**.

1. Seleccione el marco de trabajo de la aplicación **Ruby on Rails**. Cuando finalice, seleccione **Siguiente**.

1. **Web App on Linux** es el destino de implementación predeterminado.  Si lo desea, puede seleccionar **Web App for Containers**. El marco de trabajo de la aplicación que ha elegido antes determina el tipo de destino de implementación del servicio de Azure que está disponible aquí. 
    
1. Seleccione el servicio de destino de su elección y, después, seleccione **Siguiente**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configuración de Azure DevOps y una suscripción de Azure 

1. Cree una organización de Azure DevOps gratuita o elija una existente. 

1. Escriba el nombre del proyecto de Azure DevOps. 

1. Seleccione la suscripción de Azure y la ubicación, escriba el nombre de la aplicación y seleccione **Listo**.  
    En unos minutos, el panel de DevOps Starter se muestra en Azure Portal. Una aplicación de ejemplo se configura en un repositorio en la organización de Azure DevOps, se ejecuta una compilación y la aplicación se implementa en Azure. 
    
    El panel proporciona visibilidad del repositorio de código, la canalización de CI/CD y la aplicación de Azure. A la derecha, seleccione **Examinar** para ver la aplicación en ejecución.

    ![Vista de panel](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Confirmación de los cambios en el código y ejecución de CI/CD

Azure DevOps Starter crea un repositorio Git en Azure Pipelines o GitHub. Para ver el repositorio y realizar cambios en el código de la aplicación, siga estos pasos:

1. En el lado izquierdo del panel de DevOps Starter, seleccione el vínculo de la rama principal. El vínculo abre una vista al repositorio Git recién creado.

1. Para ver la dirección URL de clonación del repositorio, seleccione **Clonar** en la parte superior derecha. Puede clonar el repositorio Git en su IDE favorito. En los pasos siguientes, puede usar el explorador web para realizar cambios en el código y confirmarlos directamente en la rama maestra.

1. En el lado izquierdo, vaya al archivo *app/views/pages/home.html.erb* y, luego, seleccione **Editar**.

1. Realice un cambio en el archivo. Por ejemplo, modifique el texto de una de las etiquetas div.

1. Seleccione **Confirmar** y guarde los cambios.

1. En el explorador, vaya al panel de DevOps Starter. Debe haber una compilación en curso. Los cambios que ha realizado se compilan e implementan automáticamente a través de una canalización de CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examen de la canalización de CI/CD de Azure Pipelines

Azure DevOps Starter configura automáticamente una canalización de CI/CD completa en su organización de Azure DevOps. Explore y personalice la canalización según sea necesario. Para familiarizarse con las canalizaciones de compilación y de versión de Azure DevOps, siga estos pasos:

1. Vaya al panel de inicio de DevOps Starter.

1. En la parte superior, seleccione **Canalizaciones de compilación**. Una pestaña del explorador muestra la canalización de compilación del nuevo proyecto.

1. Elija el campo **Estado** y seleccione los puntos suspensivos (...). Un menú muestra varias opciones, como poner en cola una nueva compilación, poner en pausa una compilación y editar la canalización de compilación.

1. Seleccione **Editar**.

1. En este panel puede examinar las distintas tareas de la canalización de compilación. La compilación ejecuta varias tareas, como capturar códigos fuente del repositorio Git, restaurar dependencias y publicar las salidas usadas para implementaciones.

1. En la parte superior de la canalización de compilación, seleccione el nombre de esta.

1. Cambie el nombre de la canalización de compilación por otro más descriptivo, seleccione **Guardar y poner en cola** y, luego, **Guardar**.

1. En el nombre de la canalización de compilación, seleccione **Historial**. Este panel muestra un registro de auditoría de los cambios recientes de la compilación. Azure DevOps realiza un seguimiento de los cambios realizados en la canalización de compilación y permite comparar las versiones.

1. Seleccione **Desencadenadores**.  DevOps Starter crea automáticamente un desencadenador de integración continua y cada confirmación al repositorio inicia una compilación. Opcionalmente, puede elegir incluir o excluir ramas del proceso de integración continua.

1. Seleccione **Retención**. En función del escenario, puede especificar directivas para conservar o quitar un determinado número de compilaciones.

1. Seleccione **Compilación y versión** y, después, **Versiones**.  DevOps Starter crea una canalización de versión para administrar implementaciones en Azure.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a la canalización de versión, y, después, **Editar**. La canalización de versión contiene una *canalización*, que define el proceso de lanzamiento.

1. En **Artefactos**, seleccione **Colocar**. La canalización de compilación que ha examinado genera la salida que se usa para el artefacto. 

1. A la derecha del icono **Colocar**, seleccione **Desencadenador de implementación continua**. Esta canalización de versión tiene un desencadenador de implementación continua habilitado, que ejecuta una implementación cada vez que hay un nuevo artefacto de compilación disponible. Opcionalmente, puede deshabilitar el desencadenador, con lo que las implementaciones van a requerir una ejecución manual. 

1. En el lado izquierdo, seleccione **Tareas**. Las tareas son las actividades que realiza el proceso de implementación. En este ejemplo, se ha creado una tarea que se implementa en Azure App Service.

1. A la derecha, seleccione **Ver versiones** para mostrar un historial de las versiones.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a una de las versiones y, después, **Abrir**. Puede explorar varios menús, como un resumen de las versiones, elementos de trabajo asociados y las pruebas.

1. Seleccione **Confirmaciones**. Esta vista muestra las confirmaciones de código que están asociadas a esta implementación. 

1. Seleccione **Registros**. Los registros contienen información útil sobre el proceso de implementación. Se pueden ver tanto durante las implementaciones como después de ellas.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando dejen de ser necesarios, puede eliminar la instancia de Azure App Service y los recursos relacionados que ha creado en esta guía de inicio rápido. Para ello, utilice la funcionalidad de **eliminación** del panel de DevOps Starter.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo modificar las canalizaciones de compilación y versión para satisfacer las necesidades de su equipo, consulte:

> [!div class="nextstepaction"]
> [Definición de la canalización de implementación continua (CD) en varias fases](/azure/devops/pipelines/release/define-multistage-release-process)