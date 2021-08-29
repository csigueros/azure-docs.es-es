---
title: 'Tutorial: Introducción a la integración con las canalizaciones'
description: En este tutorial, aprenderá a integrar las canalizaciones y las actividades mediante Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 71ca2dd856b18424d79a20db8a4c5552b2e98c53
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822943"
---
# <a name="integrate-with-pipelines"></a>Integración con las canalizaciones

En este tutorial, aprenderá a integrar las canalizaciones y las actividades mediante Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Creación de una canalización y adición de una actividad de cuaderno

1. En Synapse Studio, vaya al centro **Integrate** (Integración).
1. Seleccione **+**  > **Canalización** para crear una canalización. Haga clic en el nuevo objeto de canalización para abrir el diseñador de canalizaciones.
1. En **Actividades**, expanda la carpeta **Synapse** y arrastre un objeto **Cuaderno** al diseñador.
1. Seleccione la pestaña **Configuración** de las propiedades de la actividad del cuaderno. Use la lista desplegable para seleccionar un cuaderno del área de trabajo actual de Synapse.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Programación de la canalización para que se ejecute cada hora

1. En la canalización, seleccione **Agregar desencadenador** > **Nuevo/editar**.
1. En **Choose trigger** (Elegir desencadenador), seleccione **New** (Nuevo) y establezca el valor de **Recurrence** (Periodicidad) en "cada 1 hora".
1. Seleccione **Aceptar**. 
1. Seleccione **Publish All** (Publicar todo). 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Forzado de la ejecución inmediata de una canalización

Una vez publicada la canalización, puede que quiera ejecutarla inmediatamente sin esperar a que pase una hora.

1. Abra la canalización.
1. Haga clic en **Agregar desencadenador** > **Trigger now** (Desencadenar ahora).
1. Seleccione **Aceptar**. 

## <a name="monitor-pipeline-execution"></a>Supervisión de la ejecución de la canalización

1. Vaya al centro de conectividad **Supervisión**.
1. Seleccione **Ejecuciones de canalizaciones** para supervisar el progreso de la ejecución de la canalización.
1. En esta vista puede cambiar entre una **lista** tabular y mostrar un gráfico de **Gantt**. 
1. Haga clic en un nombre de canalización para ver el estado de las actividades de esa canalización.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Visualización de datos con Power BI](get-started-visualize-power-bi.md)
