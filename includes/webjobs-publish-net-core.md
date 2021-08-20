---
title: Archivo de inclusión
description: archivo de inclusión
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9013095a974a86d34399ab566bcc3fea440f2dc7
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125980"
---
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

1. En el cuadro de diálogo **Publicar**, seleccione **Azure** en **Destino** y, luego, **Siguiente**. 

1. Seleccione **Azure WebJobs** en **Specific target** (Destino específico) y, luego, elija **Siguiente**.

1. Por encima de **Instancias de App Service** seleccione el botón más ( **+** ) para **Create a new Azure WebJob** (Crear una instancia de Azure WebJob).

1. En el cuadro de diálogo **App Service (Windows)** , use la configuración de hospedaje de la tabla siguiente.

    | Configuración      | Valor sugerido  | Descripción                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nombre** | Nombre único globalmente | Nombre que identifica de forma única la nueva aplicación de función. |
    | **Suscripción** | Elija una suscripción | La suscripción de Azure que se va a usar. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nombre del grupo de recursos en el que se va a crear la instancia de Function App. Elija **Nuevo** para crear un grupo de recursos nuevo.|
    | **[Plan de hospedaje](../articles/app-service/overview-hosting-plans.md)** | Plan de App Service | Un [plan de App Service](../articles/app-service/overview-hosting-plans.md) especifica la ubicación, el tamaño y las características de la granja de servidores web que hospeda la aplicación. Para ahorrar dinero cuando hospede varias aplicaciones, configure las aplicaciones web para que compartan un único plan de App Service. Los planes de App Service definen la región, el tamaño de la instancia, el recuento de escala y la SKU (gratis, compartida, básica, estándar o premium). Seleccione **Nuevo** para crear un plan de App Service. Los niveles Gratis y Básico no admiten la opción Siempre disponible para mantener el sitio en ejecución continua. |

    ![Cuadro de diálogo Crear servicio de aplicaciones](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Seleccione **Crear** para crear un WebJobs y los recursos relacionados en Azure con esta configuración e implementar el código del proyecto.

1. Seleccione **Finalizar** para volver a la página **Publicar**.  