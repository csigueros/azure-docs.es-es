---
title: Ejecución de tareas en segundo plano con WebJobs
description: Aprenda a usar WebJobs para ejecutar tareas en segundo plano en Azure App Service. Elija entre diversos formatos de script y ejecútelos con expresiones CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 6/25/2021
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 3ecb31f6d008fda51d03c3e0d2d44b881397b466
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "122653451"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Ejecución de tareas en segundo plano con WebJobs en Azure App Service

Implemente WebJobs mediante [Azure Portal](https://portal.azure.com) para cargar un archivo ejecutable o script. Puede ejecutar tareas en segundo plano en Azure App Service.

Si en lugar de Azure App Service usa Visual Studio 2019 para desarrollar e implementar WebJobs, consulte [Implementación de WebJobs mediante Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Información general
WebJobs es una característica de [Azure App Service](index.yml) que le permite ejecutar un programa o script en la misma instancia que una aplicación web, una aplicación de API o una aplicación móvil. No hay ningún coste adicional en el uso de trabajos web.

Puede usar el SDK de Azure WebJobs con WebJobs para simplificar muchas tareas de programación. WebJobs todavía no es compatible con App Service en Linux. Para obtener más información, consulte [¿Qué es el SDK de Webjobs?](https://github.com/Azure/azure-webjobs-sdk/wiki)

Azure Functions proporciona otra manera de ejecutar programas y scripts. Para una comparación entre WebJobs y Functions, consulte [Elección entre Flow, Logic Apps, Functions y WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos de WebJob

En la tabla siguiente se describen las diferencias entre los WebJobs *continuo* y *desencadenado*.


|Continuo  |Desencadenado  |
|---------|---------|
| Se inicia inmediatamente cuando se crea el WebJob. Para evitar que el trabajo finalice, el programa o script habitualmente funciona dentro de un bucle sin fin. Si el trabajo finaliza, es posible reiniciarlo. Normalmente se usa con el SDK de WebJobs. | Se inicia solo cuando se desencadena manualmente o de acuerdo con una programación. |
| Se ejecuta en todas las instancias en que se ejecuta la aplicación web. También puede restringir el WebJob a una sola instancia. |Se ejecuta en una sola instancia que Azure selecciona para el equilibrio de carga.|
| Admite la depuración remota. | No admite la depuración remota.|
| El código se implementa en `\site\wwwroot\app_data\Jobs\Continuous`. | El código se implementa en `\site\wwwroot\app_data\Jobs\Triggered`. |

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipos de archivo admitidos para scripts o programas

Se admiten los tipos de archivo siguientes:

* .cmd, .bat, .exe (con cmd de Windows)
* .ps1 (con PowerShell)
* .sh (con Bash)
* .php (con PHP)
* .py (con Python)
* .js (con Node.js)
* .jar (con Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Creación de un WebJob continuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Si tiene el control de código fuente configurado para la aplicación, WebJobs se deben implementar como parte de la integración del control de código fuente. Una vez configurado el control de código fuente con la aplicación, no se puede agregar una instancia de WebJobs desde Azure Portal.

1. En [Azure Portal](https://portal.azure.com), vaya a la página **App Service** de la aplicación web, la aplicación de API o la aplicación móvil de App Service.

1. En el panel izquierdo de la página **App Service** de la aplicación, busque y seleccione **WebJobs**.

   ![Selección de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

1. En la página **WebJobs**, seleccione **Agregar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

1. Rellene la configuración de **Agregar trabajo web** como se especifica en la tabla.

   ![Captura de pantalla que muestra la configuración de Agregar WebJob que necesita establecer.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Configuración      | Valor de ejemplo   | Descripción  |
   | ------------ | ----------------- | ------------ |
   | **Nombre** | myContinuousWebJob | Nombre único dentro de una aplicación de App Service. Debe comenzar con una letra o un número y no puede contener caracteres especiales salvo "-" y "_". |
   | **Carga de archivos** | ConsoleApp.zip | Archivo *ZIP* que contiene el archivo de script o el archivo ejecutable así como cualquier archivo complementario necesario para ejecutar el programa o script. Los tipos de archivo de script o de archivo ejecutable compatibles aparecen en la sección [Tipos de archivo admitidos](#acceptablefiles). |
   | **Tipo** | Continuo | Los [tipos de WebJob](#webjob-types) aparecen descritos anteriormente en este artículo. |
   | **Escala** | Instancias múltiples | Solo disponible para WebJobs continuos. Determina si el programa o el script se ejecuta en todas las instancias o solo en una. La opción para que se ejecute en varias instancias no se aplica a los [planes de tarifa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Gratis o Compartido. | 

1. Seleccione **Aceptar**. 

   El WebJob nuevo aparece en la página **WebJobs**. Si ve un mensaje que indica que se ha agregado el WebJob, pero no lo ve, seleccione **Actualizar**. 

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/list-continuous-webjob.png)

1. Para detener o reiniciar un WebJob continuo, haga clic con el botón derecho en el WebJob en la lista y seleccione **Detener** o **Iniciar**.

    ![Detención de un WebJob continuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Creación de un WebJob desencadenado de manera manual

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **App Services**. 

1. Seleccione la aplicación web, la aplicación de API o la aplicación móvil de la lista. 

1. En el panel izquierdo de la página **App Service** de la aplicación, seleccione **WebJobs**.

   ![Selección de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. En la página **WebJobs**, seleccione **Agregar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

1. Rellene la configuración de **Agregar trabajo web** como se especifica en la tabla. 

   ![Captura de pantalla que muestra la configuración que debe establecerse para crear un WebJob desencadenado manualmente.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Configuración      | Valor de ejemplo   | Descripción  |
   | ------------ | ----------------- | ------------ |
   | **Nombre** | myTriggeredWebJob | Nombre único dentro de una aplicación de App Service. Debe comenzar con una letra o un número y no puede contener caracteres especiales salvo "-" y "_".|
   | **Carga de archivos** | ConsoleApp.zip | Archivo *ZIP* que contiene el archivo de script o el archivo ejecutable así como cualquier archivo complementario necesario para ejecutar el programa o script. Los tipos de archivo de script o de archivo ejecutable compatibles aparecen en la sección [Tipos de archivo admitidos](#acceptablefiles). |
   | **Tipo** | Desencadenado | Los [tipos de WebJob](#webjob-types) se han descrito anteriormente en este artículo. |
   | **Desencadenadores** | Manual | |

4. Seleccione **Aceptar**.

   El WebJob nuevo aparece en la página **WebJobs**. Si ve un mensaje que indica que se ha agregado el WebJob, pero no lo ve, seleccione **Actualizar**.  

   ![Lista de WebJobs desencadenados](./media/web-sites-create-web-jobs/list-triggered-webjob.png)

7. Para ejecutar el WebJob, haga clic con el botón derecho en su nombre en la lista y seleccione **Ejecutar**.
   
    ![Ejecución de WebJobs](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Creación de un WebJob programado

También se desencadena un WebJob programado. Puede programar que el desencadenador se active automáticamente según la programación que especifique.
 
<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **App Services**. 

1. Seleccione la aplicación web, la aplicación de API o la aplicación móvil de la lista. 

1. En el panel izquierdo de la página **App Service** de la aplicación, seleccione **WebJobs**.

   ![Selección de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

1. En la página **WebJobs**, seleccione **Agregar**.

   ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Rellene la configuración de **Agregar trabajo web** como se especifica en la tabla.

   ![Página Agregar WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Configuración      | Valor de ejemplo   | Descripción  |
   | ------------ | ----------------- | ------------ |
   | **Nombre** | myScheduledWebJob | Nombre único dentro de una aplicación de App Service. Debe comenzar con una letra o un número y no puede contener caracteres especiales salvo "-" y "_". |
   | **Carga de archivos** | ConsoleApp.zip | Archivo *ZIP* que contiene el archivo de script o el archivo ejecutable así como cualquier archivo complementario necesario para ejecutar el programa o script. Los tipos de archivo de script o de archivo ejecutable compatibles aparecen en la sección [Tipos de archivo admitidos](#acceptablefiles). |
   | **Tipo** | Desencadenado | Los [tipos de WebJob](#webjob-types) aparecen descritos anteriormente en este artículo. |
   | **Desencadenadores** | Programado | Para que la programación funcione de manera confiable, habilite la característica AlwaysOn. AlwaysOn solo está disponible en los planes de tarifa Básico, Estándar y Premium.|
   | **Expresión CRON** | 0 0/20 * * * * | Las [expresiones CRON](#ncrontab-expressions) se describen en la sección siguiente. |

4. Seleccione **Aceptar**.

   El WebJob nuevo aparece en la página **WebJobs**. Si ve un mensaje que indica que se ha agregado el WebJob, pero no lo ve, seleccione **Actualizar**.  

   ![Lista de WebJobs programados](./media/web-sites-create-web-jobs/list-scheduled-webjob.png)

## <a name="ncrontab-expressions"></a>Expresiones NCRONTAB

Puede especificar una [expresión NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) en el portal o incluir un archivo `settings.job` en la raíz del archivo *.zip* de WebJob, como en el ejemplo siguiente:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Para más información, consulte [Programar un trabajo Web desencadenado](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="manage-webjobs"></a>Administración de WebJobs

Puede administrar el estado de ejecución de WebJobs individuales que se ejecutan en el sitio en [Azure Portal](https://portal.azure.com). Simplemente vaya a **Configuración** > **WebJobs**, elija el WebJob y, a continuación, puede iniciar y detener el WebJob. También puede ver y modificar la contraseña del webhook que ejecuta el WebJob.  

También puede [agregar una configuración de la aplicación](configure-common.md#configure-app-settings) denominada `WEBJOB_STOPPED` con un valor de `1` para detener todos los WebJobs que se ejecutan en el sitio. Esto puede ser práctico como una manera de evitar que los WebJobs en conflicto se ejecuten en espacios de ensayo y de producción. De forma similar, puede usar un valor de `1` para la opción de configuración `WEBJOBS_DISABLE_SCHEDULE` para deshabilitar los WebJobs desencadenados en el sitio o en un espacio de ensayo. En el caso de las ranuras, recuerde habilitar la opción **Configuración de ranura de implementación** para que no se intercambie la propia configuración.    

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Visualización del historial de trabajos

1. Seleccione el WebJob y, a continuación, para ver el historial, seleccione **Registros**.
   
   ![Botón Registros](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. En la página **Detalles del WebJob**, seleccione una hora para ver los detalles de una ejecución.
   
   ![Detalles del WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. En la página **Detalles de ejecución del WebJob**, seleccione **Alternar salida** para ver el texto del contenido del registro.
   
    ![Detalles de ejecución del trabajo web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver el texto de salida en otra ventana del explorador, seleccione **Descargar**. Para descargar el texto propiamente tal, haga clic con el botón derecho en **Descargar** y use las opciones del explorador para guardar el contenido del archivo.
   
5. Seleccione el vínculo de la ruta de navegación de los **WebJobs** que se encuentra en la parte superior de la página para ir a una lista de los WebJobs.

    ![Ruta de navegación de WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de WebJobs el panel del historial](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Pasos siguientes

El SDK de Azure WebJobs se puede usar con WebJobs para simplificar muchas tareas de programación. Para obtener más información, consulte [¿Qué es el SDK de Webjobs?](https://github.com/Azure/azure-webjobs-sdk/wiki)
