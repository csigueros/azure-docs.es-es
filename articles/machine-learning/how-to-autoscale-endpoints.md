---
title: Escalabilidad automática de puntos de conexión administrados en línea
titleSuffix: Azure Machine Learning
description: Aprenda a escalar verticalmente puntos de conexión administrados. Obtenga más CPU, memoria, espacio en disco y características adicionales.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 11/03/2021
ms.openlocfilehash: f979651909a2484f6bcdf7b0953c91874bea7cc5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520662"
---
# <a name="autoscale-a-managed-online-endpoint-preview"></a>Escalabilidad automática de un punto de conexión administrado en línea (versión preliminar)

La escalabilidad automática ejecuta automáticamente la cantidad adecuada de recursos para controlar la carga en la aplicación. Los [puntos de conexión administrados](concept-endpoints.md) permiten la escalabilidad automática mediante la integración con la característica de escalabilidad automática de Azure Monitor.

La escalabilidad automática de Azure Monitor admite un amplio conjunto de reglas. Puede configurar escalado basado en métricas (por ejemplo, uso de CPU > 70 %), escalado basado en programación (por ejemplo, reglas de escalado para horario comercial punta) o una combinación. Para conocer los detalles, consulte [Información general sobre el escalado automático en Microsoft Azure](/azure-monitor/autoscale/autoscale-overview.md).

:::image type="content" source="media/how-to-autoscale-endpoints/concept-autoscale.png" alt-text="Diagrama de la escalabilidad automática agregando o quitando instancias según necesidad":::

En la actualidad puede administrar la escalabilidad automática mediante la CLI de Azure, REST, ARM o Azure Portal basado en el explorador. Otros SDK de Azure ML, como el de Python, van a agregar compatibilidad con el tiempo.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos 

* Un punto de conexión implementado. [Implementación y puntuación de un modelo de Machine Learning con un punto de conexión en línea (versión preliminar)](how-to-deploy-managed-online-endpoints.md). 

## <a name="define-an-autoscale-profile"></a>Definición de un perfil de escalado automático

Para habilitar la escalabilidad automática en un punto de conexión, primero defina un perfil de escalabilidad automática. Este perfil define la capacidad predeterminada, mínima y máxima del conjunto de escalado. En el ejemplo siguiente se establece la capacidad predeterminada y mínima en dos instancias de máquina virtual, y la capacidad máxima en cinco:

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

El fragmento de código siguiente establece los nombres del punto de conexión y la implementación:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_endpoint_deployment_name" :::

Luego obtenga el identificador de Azure Resource Manager de la implementación y el punto de conexión:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_other_env_variables" :::

El fragmento de código siguiente crea el perfil de escalabilidad automática:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="create_autoscale_profile" :::

> [!NOTE]
> Para obtener más información, vea la [página de referencia de la escalabilidad automática](/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)

# <a name="portal"></a>[Portal](#tab/azure-portal)

En [Estudio de Azure Machine Learning](https://ml.azure.com), seleccione el área de trabajo y luego seleccione __Puntos de conexión__ en el lado izquierdo de la página. Una vez que aparezcan los puntos de conexión, seleccione el que quiere configurar.

:::image type="content" source="media/how-to-autoscale-endpoints/select-endpoint.png" alt-text="Captura de pantalla de una entrada de implementación de punto de conexión en el portal.":::

En la pestaña __Detalles__ del punto de conexión, seleccione __Configurar escalado automático__.

:::image type="content" source="media/how-to-autoscale-endpoints/configure-auto-scaling.png" alt-text="Captura de pantalla del vínculo Configurar escalado automático en los detalles del punto de conexión.":::

En __Elegir cómo escalar los recursos__, seleccione __Escalabilidad automática personalizada__ para comenzar con la configuración. En la condición de escalabilidad predeterminada, use los valores siguientes:

* En __Modo de escala__, seleccione __Escalado basado en una métrica__.
* Establezca __Mínimo__ en __2__.
* Establezca __Máximo__ en __5__.
* Establezca __Predeterminado__ en __2__.

:::image type="content" source="media/how-to-autoscale-endpoints/choose-custom-autoscale.png" alt-text="Captura de pantalla que muestra la elección de escalabilidad automática personalizada.":::

---

## <a name="create-a-rule-to-scale-out-using-metrics"></a>Creación de una regla para escalar horizontalmente mediante métricas

Una regla de escalabilidad horizontal común es aquella que aumenta el número de instancias de máquina virtual cuando la carga media de CPU es alta. En el ejemplo siguiente se asignan dos nodos más (hasta el máximo) si la carga media de CPU es superior al 70 % durante cinco minutos:

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_out_on_cpu_util" :::

La regla forma parte del perfil `my-scale-settings` (`autoscale-name` coincide con el valor `name` del perfil). El valor de su argumento `condition` indica que la regla debe desencadenarse cuando "El consumo medio de CPU entre las instancias de la máquina virtual supere el 70 % durante cinco minutos". Cuando se cumple esa condición, se asignan dos instancias de máquina virtual más. 

> [!NOTE]
> Para obtener más información sobre la sintaxis de la CLI, vea [`az monitor autoscale`](/cli/azure/monitor/autoscale).

# <a name="portal"></a>[Portal](#tab/azure-portal)

En la sección __Reglas__, seleccione __Agregar una regla__. Aparece la página __Regla de escalado__. Use la siguiente información para rellenar los campos de esta página:

* Establezca __Nombre de métrica__ en __Porcentaje de uso de la CPU__.
* Establezca __Operador__ en __Mayor que__ y el __Umbral de métrica__ en __70__.
* Establezca __Duración (minutos)__ en 5. Deje las __Estadísticas de intervalo de agregación__ como __Media__.
* Establezca __Operación__ en __Aumentar recuento en__ y __Recuento de instancias__ en __2__.

Por último, seleccione el botón __Agregar__ para crear la regla.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-out-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-out-rule.png" alt-text="Captura de pantalla que muestra la regla escalar horizontalmente > 70 % de CPU durante 5 minutos.":::

---

## <a name="create-a-rule-to-scale-in-using-metrics"></a>Creación de una regla para reducir horizontalmente mediante métricas

Si la carga es ligera, una regla de reducción horizontal puede reducir el número de instancias de máquina virtual. En el ejemplo siguiente se libera un solo nodo, hasta un mínimo de 2, si la carga de CPU es inferior al 30 % durante 5 minutos:

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_in_on_cpu_util" :::

# <a name="portal"></a>[Portal](#tab/azure-portal)

En la sección __Reglas__, seleccione __Agregar una regla__. Aparece la página __Regla de escalado__. Use la siguiente información para rellenar los campos de esta página:

* Establezca __Nombre de métrica__ en __Porcentaje de uso de la CPU__.
* Establezca __Operador__ en __Menor que__ y el __Umbral de métrica__ en __30__.
* Establezca __Duración (minutos)__ en __5__.
* Establezca __Operación__ en __Reducir el recuento en__ y __Recuento de instancias__ en __1__.

Por último, seleccione el botón __Agregar__ para crear la regla.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-in-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-in-rule.png" alt-text="Captura de pantalla que muestra una regla de reducción horizontal":::

Si tiene reglas de escalado horizontal y de reducción horizontal, van a tener un aspecto similar al de la captura de pantalla siguiente. Ha especificado que si la carga de CPU media supera el 70 % durante 5 minutos, se deben asignar dos nodos más, hasta el límite de 5. Si la carga de CPU es inferior al 30 % durante 5 minutos, se debe liberar un solo nodo, hasta el mínimo de 2. 

:::image type="content" source="media/how-to-autoscale-endpoints/autoscale-rules-final.png" lightbox="media/how-to-autoscale-endpoints/autoscale-rules-final.png" alt-text="Captura de pantalla que muestra la configuración de escalabilidad automática, incluidas las reglas.":::

---

## <a name="create-a-scaling-rule-based-on-endpoint-metrics"></a>Creación de una regla de escalabilidad basada en métricas del punto de conexión

Las reglas anteriores se aplicaban a la implementación. Ahora, agregue una regla que se aplique al punto de conexión. En este ejemplo, si la latencia de la solicitud supera una media de 70 milisegundos durante 5 minutos, asigne otro nodo.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_up_on_request_latency" :::

# <a name="portal"></a>[Portal](#tab/azure-portal)

En la parte inferior de la página, seleccione __+ Agregar una condición de escalado__.

Seleccione __Escalado basado en una métrica__ y luego __Agregar una regla__. Aparece la página __Regla de escalado__. Use la siguiente información para rellenar los campos de esta página:

* Establezca __Origen de métrica__ en __Otro recurso__.
* Establezca __Tipo de recurso__ en __Puntos de conexión línea de Machine Learning__.
* Establezca __Recurso__ en el punto de conexión.
* Establezca __Nombre de la métrica__ en __Latencia de solicitud__.
* Establezca __Operador__ en __Mayor que__ y el __Umbral de métrica__ en __70__.
* Establezca __Duración (minutos)__ en __5__.
* Establezca __Operación__ en __Aumentar recuento en__ y __Recuento de instancias__ en 1.

:::image type="content" source="media/how-to-autoscale-endpoints/endpoint-rule.png" lightbox="media/how-to-autoscale-endpoints/endpoint-rule.png" alt-text="Captura de pantalla que muestra las reglas de métricas del punto de conexión.":::

---

## <a name="create-scaling-rules-based-on-a-schedule"></a>Creación de reglas de escalabilidad basadas en una programación

También puede crear reglas que se apliquen solo en determinados días u horas. En este ejemplo, el recuento de nodos se establece en 2 el fin de semana.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="weekend_profile" :::

# <a name="portal"></a>[Portal](#tab/azure-portal)

En la parte inferior de la página, seleccione __+ Agregar una condición de escalado__. En la nueva condición de escala, use la siguiente información para rellenar los campos:
 
* Seleccione __Escalar a un número específico de instancias__.
* Establezca __Recuento de instancias__ en __2__.
* Establezca __Programación__  en __Repetir en días específicos__.
* Establezca la programación en __Repetir cada__ __Sábado__ y __Domingo__.

:::image type="content" source="media/how-to-autoscale-endpoints/schedule-rules.png" lightbox="media/how-to-autoscale-endpoints/schedule-rules.png" alt-text="Captura de pantalla que muestra reglas basadas en una programación.":::

---

## <a name="delete-resources"></a>Eliminar recursos

Si no va a usar las implementaciones, elimínelas:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la escalabilidad automática con Azure Monitor, vea los siguientes artículos:

- [Información acerca de la configuración de escalado automático](/azure-monitor/autoscale/autoscale-understand-settings)
- [Información general sobre patrones comunes de escalabilidad automática](/azure-monitor/autoscale/autoscale-common-scale-patterns)
- [Procedimientos recomendados de escalado automático](/azure-monitor/autoscale/autoscale-best-practices)
- [Solución de problemas de escalabilidad automática de Azure](/azure-monitor/autoscale/autoscale-troubleshoot)
