---
title: 'Tutorial: Implementación de una aplicación de ASP.NET en Azure Virtual Machines mediante Azure DevOps Starter'
description: DevOps Starter facilita tanto que se empiece a usar Azure como que se implemente una aplicación de ASP.NET en máquinas virtuales de Azure en pocos pasos.
ms.author: gwallace
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.openlocfilehash: 9bf6b19722ac78d15c3a862b43976294dc75747c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055745"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Tutorial: Implementación de una aplicación de ASP.NET en Azure Virtual Machines mediante Azure DevOps Starter

Azure DevOps Starter ofrece una experiencia simplificada en la que puede utilizar su código existente y el repositorio de Git, o elegir una aplicación de ejemplo para crear una canalización de integración continua (CI) y entrega continua (CD) en Azure. 

DevOps Starter también:
* Crea automáticamente recursos de Azure, como una máquina virtual (VM) de Azure.
* Crea y configura una canalización de versión en Azure DevOps que incluye una canalización de compilación para integración continua.
* Configura una canalización de versión para implementación continua. 
* Crea un recurso de Azure Application Insights para la supervisión.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Usar DevOps Starter para implementar una aplicación de ASP.NET
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Examen de la canalización de CI
> * Examen de la canalización de CD
> * Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure
> * Configurar la supervisión de Azure Application Insights
> * Limpieza de recursos

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Puede obtener una gratuita mediante [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Usar DevOps Starter para implementar una aplicación de ASP.NET

DevOps Starter crea una canalización de CI/CD en Azure Pipelines. Puede crear una organización de Azure DevOps nueva o usar una existente. DevOps Projects también crea recursos de Azure, como máquinas virtuales, en la suscripción de Azure que se elija.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda, escriba **DevOps Starter** y, después, selecciónelo. Haga clic en **Agregar** para crear un recurso.

    ![Panel de DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Seleccione **.NET** y después **Siguiente**.

1. En **Elegir un marco de trabajo de la aplicación**, seleccione **ASP.NET** y, después, seleccione **Siguiente**. El marco de trabajo de la aplicación que eligió en un paso anterior, determina el tipo de destino de implementación del servicio de Azure que está disponible aquí. 

1. Seleccione la máquina virtual y, después, **Siguiente**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configuración de Azure DevOps y una suscripción de Azure

1. Cree una organización de Azure DevOps o elija una existente. 

1. Escriba el nombre del proyecto de Azure DevOps. 

1. Seleccione los servicios de la suscripción a Azure. Si lo desea, puede seleccionar **Cambiar** y, luego, especificar más detalles de la configuración, como la ubicación de los recursos de Azure.
 
1. Escriba el nombre de máquina virtual, el nombre de usuario y la contraseña de el nuevo recurso de máquina virtual de Azure y, después, seleccione **Listo**. Al cabo de unos minutos, la máquina virtual de Azure estará lista. Se configura una aplicación ASP.NET de ejemplo en un repositorio de la organización de Azure DevOps, se ejecutan la compilación y la versión, y la aplicación se implementa en la máquina virtual de Azure recién creada. 

   Al finalizar, el panel de DevOps Starter se muestra en Azure Portal. También puede ir al panel directamente desde **Todos los recursos** en Azure Portal. 

   Dicho panel proporciona visibilidad del repositorio de código de Azure DevOps, la canalización de CI/CD y la aplicación que se ejecuta en Azure.   

   ![Vista de panel](_img/azure-devops-project-vms/vm-starter-dashboard.png)

DevOps Starter configura automáticamente una compilación de integración continua y un desencadenador de versión que implementa en el repositorio los cambios realizados en el código. Puede seguir configurando opciones adicionales en Azure DevOps. Para ver la aplicación en ejecución, seleccione **Examinar**.
    
## <a name="examine-the-ci-pipeline"></a>Examen de la canalización de CI
 
DevOps Starter configuró automáticamente una canalización de CI/CD en Azure Pipelines. Puede explorar y personalizar la canalización. Para familiarizarse con la canalización de compilación, siga estos pasos:

1. En la parte superior del panel de DevOps Starter, seleccione **Compilar canalizaciones**. Una pestaña del explorador muestra la canalización de compilación del nuevo proyecto.

1. Elija el campo **Estado** y seleccione los puntos suspensivos (...). Un menú muestra varias opciones, como poner en cola una nueva compilación, poner en pausa una compilación y editar la canalización de compilación.

1. Seleccione **Editar**.

1. En este panel puede examinar las distintas tareas de la canalización de compilación. La compilación ejecuta varias tareas, como capturar códigos fuente del repositorio Git, restaurar dependencias y publicar las salidas usadas para implementaciones.

1. En la parte superior de la canalización de compilación, seleccione el nombre de esta.

1. Cambie el nombre de la canalización de compilación por otro más descriptivo, seleccione **Guardar y poner en cola** y, luego, **Guardar**.

1. En el nombre de la canalización de compilación, seleccione **Historial**. Este panel muestra un registro de auditoría de los cambios recientes de la compilación. Azure DevOps realiza un seguimiento de los cambios realizados en la canalización de compilación y permite comparar las versiones.

1. Seleccione **Desencadenadores**. DevOps Starter crea automáticamente un desencadenador de integración continua y cada confirmación al repositorio inicia una compilación. Opcionalmente, puede elegir incluir o excluir ramas del proceso de integración continua.

1. Seleccione **Retención**. En función del escenario, puede especificar directivas para conservar o quitar un determinado número de compilaciones.

## <a name="examine-the-cd-pipeline"></a>Examen de la canalización de CD

DevOps Starter crea y configura automáticamente los pasos necesarios para implementar desde la organización de Azure DevOps en la suscripción a Azure. Dichos pasos incluyen la configuración de una conexión de servicio de Azure para realizar la autenticación de Azure DevOps en la suscripción de Azure. La automatización también crea una canalización de implementación continua, lo que proporciona la implementación continua a la máquina virtual de Azure. Para más información acerca de la canalización de la implementación continua de Azure DevOps, siga estos pasos:

1. Seleccione **Compilación y versión** y, después, **Versiones**.  DevOps Starter crea una canalización de versión para administrar implementaciones en Azure.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a la canalización de versión, y, después, **Editar**. La canalización de versión contiene una *canalización*, que define el proceso de lanzamiento.

1. En **Artefactos**, seleccione **Colocar**. La canalización de compilación que ha examinado en los pasos anteriores genera la salida que se usa para el artefacto. 

1. Al lado del icono **Colocar**, seleccione **Desencadenador de implementación continua**. Esta canalización de versión tiene un desencadenador de implementación continua habilitado que ejecuta una implementación cada vez que hay un nuevo artefacto de compilación disponible. Opcionalmente, puede deshabilitar el desencadenador, con lo que las implementaciones van a requerir una ejecución manual. 

1. En el lado izquierdo, seleccione **Tareas** y, después, seleccione un entorno. Las tareas son las actividades que ejecuta el proceso de implementación y se agrupan en fases. Esta canalización de versión se produce en dos fases:

    - La primera contiene una tarea de implementación de un grupo de recursos de Azure que realiza dos acciones:
     
        - Configura la máquina virtual para la implementación
        -   Agrega la nueva máquina virtual a un grupo de implementación de Azure DevOps. El grupo de implementación de máquina virtual en Azure DevOps administra los grupos lógicos de las máquinas de destino de la implementación.
     
    - En la segunda fase, una tarea de administración de aplicación web de IIS crea un sitio web de IIS en la máquina virtual. Se crea una segunda tarea de implementación de una aplicación web de IIS para implementar el sitio.

1. A la derecha, seleccione **Ver versiones** para mostrar un historial de las versiones.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a una de las versiones y, después, **Abrir**. Puede explorar varios menús, como un resumen de las versiones, elementos de trabajo asociados y las pruebas.

1. Seleccione **Confirmaciones**. Esta vista muestra las confirmaciones de código que están asociadas a esta implementación. Compare las versiones para ver las diferencias de confirmación entre las implementaciones.

1. Seleccione **Registros**. Los registros contienen información útil sobre el proceso de implementación. Se pueden ver tanto durante las implementaciones como después de ellas.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure 

A partir de ese momento ya puede empezar a colaborar con un equipo en una aplicación mediante el uso de un proceso de CI/CD que implemente automáticamente el trabajo más reciente en su sitio web. Cada cambio que se realiza en el repositorio de Git inicia una compilación en Azure DevOps y una canalización de CD ejecuta una implementación en Azure. Siga el procedimiento descrito en esta sección o utilice otra técnica para confirmar los cambios en el repositorio. Los cambios de código inician el proceso de CI/CD e implementan automáticamente sus cambios en el sitio web de IIS en la máquina virtual de Azure.

1. En el panel izquierdo, seleccione **Código** y, después, vaya al repositorio.

1. Vaya al directorio *Views\Home*, seleccione los puntos suspensivos (...) que hay junto al archivo *Index.cshtml* y seleccione **Editar**.

1. Realice un cambio en el archivo, como agregar texto dentro de una de las etiquetas div. 

1. En la parte superior derecha, seleccione **Confirmar** y, después, seleccione **Confirmar** de nuevo para insertar el cambio. Tras unos instantes, se inicia una compilación se inicia en Azure DevOps y se ejecuta una versión para implementar los cambios. Supervise el estado de la compilación en el panel de DevOps Starter o en el explorador con la organización de Azure DevOps.

1. Una vez que complete la versión, actualice la aplicación para comprobar los cambios.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar la supervisión de Azure Application Insights

Con Azure Application Insights puede supervisar fácilmente el rendimiento y la utilización de la aplicación. DevOps Starter configura automáticamente un recurso de Application Insights para la aplicación. Puede seguir configurando diversas alertas y supervisando las funcionalidades según sea necesario.

1. En Azure Portal, vaya al panel de DevOps Starter. 

1. En la parte inferior derecha, seleccione el vínculo de **Application Insights** para la aplicación. Se abre el panel **Application Insights**. Esta vista contiene información sobre la supervisión de la disponibilidad, el rendimiento y el uso de la aplicación.

   ![El panel Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Seleccione **Intervalo de tiempo** y, después, elija **Última hora**. Para filtrar los resultados, seleccione **Actualizar**. Ya puede ver toda la actividad de los últimos 60 minutos. 
    
1. Para salir del intervalo de tiempo, seleccione **x**.

1. Seleccione **Alertas** y, después, **Agregar alerta de métrica**. 

1. Escriba el nombre de la alerta.

1. En la lista desplegable **Métrica**, examine las distintas métricas de alertas. La alerta predeterminada es para un **tiempo de respuesta de servidor mayor de 1 segundo**. Puede configurar fácilmente diversas alertas para mejorar las funcionalidades de supervisión de la aplicación.

1. Seleccione la casilla **Notify via Email owners, contributors, and readers** (Notificar a través de correo electrónico a lectores, colaboradores y propietarios). Si lo desea, puede realizar acciones adicionales cuando se muestre una alerta mediante la ejecución de una aplicación de lógica de Azure.

1. Seleccione **Aceptar** para crear la alerta. Poco después la alerta aparece como activa en el panel. 

1. Salga del área **Alertas** y vuelva al panel **Application Insights**.

1. Seleccione **Disponibilidad** y, después, **Agregar prueba**. 

1. Escriba el nombre de una prueba y seleccione **Crear**. Así se crea la prueba de ping simple para comprobar la disponibilidad de la aplicación. Después de unos minutos, los resultados de la prueba están disponibles y el panel de Application Insights muestra un estado de disponibilidad.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a realizar pruebas, limpie los recursos para que no se acumulen costos de facturación. Cuando dejen de ser necesarios, puede eliminar la máquina virtual de Azure y los recursos relacionados que ha creado en este tutorial. Para ello, use la funcionalidad **Eliminación** del panel de DevOps Starter. 

> [!IMPORTANT]
> El siguiente procedimiento elimina permanentemente los recursos. La funcionalidad de *Eliminación* destruye los datos que crea el proyecto en DevOps Starter tanto en Azure como en Azure DevOps y no se podrán recuperar. Utilice este procedimiento cuando haya leído detenidamente las indicaciones.

1. En Azure Portal, vaya al panel de DevOps Starter.
1. En la parte superior derecha, seleccione **Eliminar**. 
1. En el mensaje, seleccione **Sí** para *eliminar permanentemente* los recursos.

Si lo desea, puede modificar estas canalizaciones de compilación y de versión para satisfacer las necesidades de su equipo. También puede usar este patrón de CI/CD como plantilla para las demás canalizaciones. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Usar DevOps Starter para implementar una aplicación de ASP.NET
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Examen de la canalización de CI
> * Examen de la canalización de CD
> * Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure
> * Configurar la supervisión de Azure Application Insights
> * Limpieza de recursos

Para más información acerca de la canalización de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definición de la canalización de implementación continua (CD) en varias fases](/azure/devops/pipelines/release/define-multistage-release-process)