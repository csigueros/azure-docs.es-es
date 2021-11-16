---
title: Uso de puntos de conexión en línea administrados (versión preliminar) en Estudio
titleSuffix: Azure Machine Learning
description: Aprenda a crear y usar puntos de conexión en línea administrados (versión preliminar) mediante Estudio de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2, studio
ms.author: ssambare
author: shivanissambare
ms.reviewer: laobri
ms.date: 10/21/2021
ms.openlocfilehash: fee2a8211b90c2b7dbc06a1e64f047e28031eaa6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560431"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>Creación y uso de puntos de conexión en línea administrados (versión preliminar) en Estudio

Aprenda a usar Estudio para crear y administrar puntos de conexión en línea administrados (versión preliminar) en Azure Machine Learning. Use puntos de conexión en línea administrados para simplificar las implementaciones en producción. Para más información sobre los puntos de conexión en línea administrados, consulte [¿Qué son los puntos de conexión?](concept-endpoints.md)

En este artículo aprenderá a:

> [!div class="checklist"]
> * Creación de un punto de conexión en línea administrado
> * Visualizar puntos de conexión en línea administrados
> * Incorporación de una implementación a un punto de conexión en línea administrado
> * Actualizar puntos de conexión en línea administrados
> * Eliminar implementaciones y puntos de conexión en línea administrados

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos
- Un área de trabajo de Azure Machine Learning. Para más información, consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).
- El repositorio de ejemplos: clone el [repositorio de ejemplo de AzureML](https://github.com/Azure/azureml-examples). En este artículo se usan los recursos de `/cli/endpoints/online`.

## <a name="create-a-managed-online-endpoint-preview"></a>Creación de un punto de conexión en línea administrado (versión preliminar)

Use Estudio para crear un punto de conexión en línea administrado (versión preliminar) directamente en el explorador. Al crear un punto de conexión en línea administrado en Estudio, debe definir una implementación inicial. No puede crear un punto de conexión en línea administrado vacío.

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
1. En la barra de navegación izquierda, seleccione la página **Endpoints** (Puntos de conexión).
1. Seleccione **+ Create (preview)** (+ Crear [versión preliminar]).

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="Captura de pantalla para crear un punto de conexión en línea administrado en la pestaña Puntos de conexión.":::

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" lightbox="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" alt-text="Captura de pantalla del asistente para crear un punto de conexión en línea administrado.":::

### <a name="follow-the-setup-wizard-to-configure-your-managed-online-endpoint"></a>Siga el asistente para configurar el punto de conexión en línea administrado.

1. Puede usar nuestro [modelo](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1/model) de ejemplo y el [script de puntuación](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py) en [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1).
1. En el paso **Entorno** del asistente, puede seleccionar el entorno mantenido **AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference**.

También puede crear un punto de conexión en línea administrado desde la página **Models** (Modelos) de Estudio. Esta es una manera sencilla de agregar un modelo a una implementación en línea administrada existente.

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
1. En la barra de navegación izquierda, seleccione la página **Models** (Modelos).
1. Seleccione el círculo situado junto al nombre del modelo para elegirlo.
1. Seleccione **Implementar** > **Implementación de un punto de conexión en tiempo real (versión preliminar)** .

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" alt-text="Captura de pantalla de la creación de un punto de conexión en línea administrado desde la interfaz de usuario de modelos.":::

## <a name="view-managed-online-endpoints-preview"></a>Visualización de puntos de conexión en línea administrados (versión preliminar)

Puede ver los puntos de conexión en línea administrados (versión preliminar) en la página **Endpoints** (Puntos de conexión). Use la página de detalles del punto de conexión para encontrar información crítica, incluidos el URI del punto de conexión, el estado, las herramientas de prueba, los monitores de actividad, los registros de implementación y el código de consumo de ejemplo:

1. En la barra de navegación de la izquierda, seleccione **Puntos de conexión**.
1. (Opcional) En **Filter** (Filtro) cree un filtro por **Compute type** (Tipo de proceso) para mostrar solo los tipos de proceso **Managed** (Administrado).
1. Seleccione el nombre de un punto de conexión para ver su página de detalles.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" alt-text="Captura de pantalla de la vista de detalles del punto de conexión administrado.":::

### <a name="test"></a>Prueba

Use la pestaña **Test** (Prueba) de la página de detalles del punto de conexión para probar la implementación en línea administrada. Escriba la entrada de ejemplo y vea los resultados.

1. Seleccione la pestaña **Test** (Prueba) en la página de detalles del punto de conexión.
1. Use la lista desplegable para seleccionar la implementación que desea probar.
1. Escriba la entrada de ejemplo.
1. Seleccione **Probar**.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" lightbox="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="Captura de pantalla de una prueba de implementación en la que se proporcionan los datos de ejemplo directamente en el explorador.":::

### <a name="monitoring"></a>Supervisión

Use la pestaña **Monitoring** (Supervisión) para ver gráficos de supervisión general de la actividad del punto de conexión en línea administrado.

Para usar la pestaña de supervisión, debe seleccionar "**Enable Application Insight diagnostic and data collection**" (Habilitar la recopilación de datos y el diagnóstico de Application Insights) al crear el punto de conexión.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="Captura de pantalla de la supervisión de métricas en el nivel de punto de conexión en el estudio.":::

Para más información sobre cómo ver monitores y alertas adicionales, consulte [Supervisión de puntos de conexión en línea administrados](how-to-monitor-online-endpoints.md).

## <a name="add-a-deployment-to-a-managed-online-endpoint"></a>Incorporación de una implementación a un punto de conexión en línea administrado

Puede agregar una implementación al punto de conexión en línea administrado existente.

En la **página de detalles del punto de conexión**:

1. Seleccione el botón **+ Add Deployment** (+ Agregar implementación) en la [página de detalles del punto de conexión](#view-managed-online-endpoints-preview).
2. Siga las instrucciones para completar la implementación.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" alt-text="Captura de pantalla de la opción Agregar implementación de la página de detalles del punto de conexión.":::

Como alternativa, puede usar la página **Models** (Modelos) para agregar una implementación:

1. En la barra de navegación izquierda, seleccione la página **Models** (Modelos).
1. Seleccione el círculo situado junto al nombre del modelo para elegirlo.
1. Seleccione **Implementar** > **Implementación de un punto de conexión en tiempo real (versión preliminar)** .
1. Elija implementar en un punto de conexión en línea administrado existente.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" lightbox="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" alt-text="Captura de pantalla de la opción Agregar implementación de la página Modelos.":::

> [!NOTE]
> Puede agregar una nueva implementación para ajustar el equilibrio del tráfico entre las implementaciones de un punto de conexión.
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" lightbox="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="Captura de pantalla del uso de controles deslizantes para controlar la distribución del tráfico entre varias implementaciones.":::

## <a name="update-managed-online-endpoints-preview"></a>Actualización de puntos de conexión en línea administrados (versión preliminar)

Puede actualizar el porcentaje de tráfico de la implementación y el recuento de instancias en el Estudio de Azure Machine Learning.

### <a name="update-deployment-traffic-allocation"></a>Actualización de la asignación del tráfico a las implementaciones

Utilice la **asignación del tráfico a las implementaciones** para controlar el porcentaje de solicitudes entrantes que van a cada implementación en un punto de conexión.

1. En la página de detalles del punto de conexión, seleccione **Update traffic** (Actualizar tráfico).
2. Ajuste el tráfico y seleccione **Update** (Actualizar).

> [!TIP]
> El valor de **Total traffic percentage** (Porcentaje de tráfico total) debe ser 0 % (para deshabilitar el tráfico) o 100 % (para habilitar el tráfico).

### <a name="update-deployment-instance-count"></a>Actualización del número de instancias de implementación

Use las instrucciones siguientes para ajustar el número de instancias para ampliar o reducir una implementación individual:

1. En la página de detalles del punto de conexión: Busque la tarjeta de la implementación que desea actualizar.
1. Seleccione el **icono de edición** en la tarjeta de detalles de implementación.
1. Actualice el número de instancias.
1. Seleccione **Actualizar**.

## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>Eliminación de implementaciones y puntos de conexión en línea administrados (versión preliminar)

Obtenga información sobre cómo eliminar un punto de conexión en línea administrado completo (versión preliminar) y sus implementaciones asociadas (versión preliminar). O bien, elimine una implementación individual de un punto de conexión en línea administrado.

### <a name="delete-a-managed-online-endpoint"></a>Eliminación de un punto de conexión en línea administrado

Al eliminar un punto de conexión en línea administrado también se eliminan las implementaciones asociadas a él.

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
1. En la barra de navegación izquierda, seleccione la página **Endpoints** (Puntos de conexión).
1. Seleccione el círculo situado junto al punto de conexión para elegirlo.
1. Seleccione **Eliminar**.

También puede eliminar un punto de conexión en línea administrado directamente en la [página de detalles del punto de conexión](#view-managed-online-endpoints-preview). 

### <a name="delete-an-individual-deployment"></a>Eliminación de una implementación individual

Siga estos pasos para eliminar una implementación individual de un punto de conexión en línea administrado. Esto afecta a las demás implementaciones del punto de conexión en línea administrado:

> [!NOTE]
> No se puede eliminar una implementación que tenga tráfico asignado. Para poder eliminarla, primero debe [establecer la asignación de tráfico](#update-deployment-traffic-allocation) de la implementación en 0 %.

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
1. En la barra de navegación izquierda, seleccione la página **Endpoints** (Puntos de conexión).
1. Seleccione el punto de conexión en línea administrado.
1. En la página de detalles del punto de conexión, busque la implementación que desea eliminar.
1. Seleccione el **icono de eliminación**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar los puntos de conexión en línea administrados de Azure Machine Learning. Vea los siguientes pasos:

- [¿Qué son los puntos de conexión?](concept-endpoints.md)
- [Implementación de puntos de conexión en línea administrados con la CLI de Azure](how-to-deploy-managed-online-endpoints.md)
- [Implementación de modelos con REST (versión preliminar)](how-to-deploy-with-rest.md)
- [Supervisión de los puntos de conexión en línea administrados](how-to-monitor-online-endpoints.md)
- [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](./how-to-troubleshoot-online-endpoints.md)
- [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md)
- [Administración y aumento de las cuotas de los recursos con Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)