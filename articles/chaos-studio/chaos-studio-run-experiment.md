---
title: Ejecución y administración de un experimento de caos en Azure Chaos Studio
description: Aprenda a iniciar y detener un experimento de caos, así como a consultar sus detalles y su historial, en Azure Chaos Studio
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06abbe99467af52cb7867d31f31d4aa2d727d0ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090981"
---
# <a name="run-and-manage-an-experiment-in-azure-chaos-studio"></a>Ejecución y administración de un experimento en Azure Chaos Studio

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En este artículo se facilita información general sobre cómo usar un experimento de caos que haya creado anteriormente.

## <a name="start-an-experiment"></a>Inicio de un experimento

1. Abra [Azure Portal](https://portal.azure.com).

2. Busque **Chaos Studio (versión preliminar)** en la barra de búsqueda.

3. Haga clic en **Experimentos**. Es la vista de lista de experimentos que puede iniciar, detener o eliminar de forma masiva. También crear uno.

    ![Lista de experimentos del portal](images/run-experiment-list.png)

4. Haga clic en el experimento. En la página de información general puede iniciar, detener y editar el experimento, así como consultar detalles esenciales sobre el recurso y acceder al historial. Haga clic en el botón **Iniciar** y, a continuación, en **Aceptar** para iniciar el experimento.

    ![Inicio del experimento](images/run-experiment-start.png)

5. En el estado del experimento se muestra *PreProcessingQueued*, luego *WaitingToStart* y, por último, *En ejecución*.

## <a name="view-experiment-history-and-details"></a>Consulta del historial y los detalles del experimento

1. Una vez que se esté ejecutando el experimento, haga clic en **Detalles** en la ejecución actual, debajo de **Historial**, para consultar los detalles sobre el estado y los errores.

    ![Historial de ejecuciones](images/run-experiment-history.png)

2. En la vista de detalles del experimento se muestra el estado de ejecución de cada paso, rama y error. Haga clic en un error.

    ![Detalles del experimento](images/run-experiment-details.png)

3. En los detalles del error se muestra más información sobre la ejecución del error, como los destinos que han tenido errores o se han ejecutado correctamente y por qué. Si se produce un error al ejecutar el experimento, la información de depuración aparece aquí.

    ![Detalles del error](images/run-experiment-fault.png)

## <a name="edit-experiment"></a>Edición del experimento

1. Vuelva a la información general del experimento y haga clic en el botón **Editar**.

    ![Edición del experimento](images/run-edit.png)

2. Se trata del mismo diseñador de experimentos que se usó para crear el experimento. Puede agregar o quitar pasos, ramas y errores, así como editar parámetros de error y destinos. Haga clic en **…** junto al error para editarlo.

    ![Edición de un error](images/run-edit-ellipses.png)

3. Cuando haya terminado de editarlo, haga clic en **Guardar**. Si desea descartar los cambios sin guardarlos, haga clic en el botón **Cerrar (X)** de la parte superior derecha.
  ![Guardado del experimento](images/run-edit-save.png)

> [!WARNING]
> Si agregó destinos al experimento, recuerde agregar también una asignación de roles en el recurso de destino para la identidad del experimento.

## <a name="delete-experiment"></a>Eliminación del experimento
1. Vuelva a la lista de experimentos y marque aquellos que desee eliminar. Haga clic en **Eliminar** en la barra de herramientas encima de la lista de experimentos. Es posible que tenga que hacer clic en los puntos suspensivos (…) para ver la opción de eliminación en función de la resolución de su pantalla.

    ![Eliminación del experimento](images/run-delete.png)

2. Haga clic en **Sí** para confirmar que desea eliminar el recuerdo.

3. Como alternativa, puede abrir un experimento y hacer clic en el botón **Eliminar** de la barra de herramientas.
