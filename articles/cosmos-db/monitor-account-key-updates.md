---
title: Supervisión de la cuenta de Azure Cosmos DB para actualizaciones y regeneración de claves
description: Use la métrica Claves de cuenta actualizadas para supervisar las actualizaciones de claves de la cuenta. Esta métrica muestra el número de veces que se actualizan las claves principal y secundaria para una cuenta y la hora a la que se han cambiado.
ms.service: cosmos-db
ms.topic: how-to
ms.author: sngun
author: SnehaGunda
ms.date: 09/16/2021
ms.openlocfilehash: fe344bdfbd9d4c88fd5cdf4024052ac122cf0e5d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594412"
---
# <a name="monitor-your-azure-cosmos-db-account-for-key-updates-and-key-regeneration"></a>Supervisión de la cuenta de Azure Cosmos DB para actualizaciones y regeneración de claves

Azure Monitor para Azure Cosmos DB proporciona métricas, alertas y registros para supervisar la cuenta. Puede crear paneles y personalizarlos en función de las necesidades. Las métricas de Azure Cosmos DB se recopilan de forma predeterminada, por lo que no es necesario habilitar ni configurar nada de forma explícita. Para supervisar las actualizaciones de claves de la cuenta, use la métrica **Claves de cuenta actualizadas**. Esta métrica muestra el número de veces que se actualizan las claves principal y secundaria para una cuenta y la hora a la que se han cambiado. También puede configurar alertas para obtener notificaciones cuando se actualiza una clave.

## <a name="monitor-key-updates-with-metrics"></a>Supervisión de actualizaciones de claves con métricas

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **Monitor** en la barra de navegación izquierda y, después, seleccione **Métricas**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Panel Métricas en Azure Monitor" border="true":::

1. En el panel **Métricas**, seleccione el ámbito del recurso para el que quiera ver las métricas.

   1. En primer lugar, elija la **suscripción** necesaria y, en el campo **Tipo de recurso**, seleccione **Azure Cosmos DB**. Se muestra una lista de grupos de recursos en los que hay cuentas de Azure Cosmos DB.

   1. Elija un **Grupo de recursos** y seleccione una de las cuentas de Azure Cosmos existentes. Seleccione Aplicar.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="Selección del ámbito de la cuenta para ver las métricas" border="true":::

1. En el campo **Métrica**, elija la métrica **Claves de cuenta actualizadas**. Deje el campo **Agregación** en el valor predeterminado **Recuento**. En esta vista se muestra el número total de veces que se han actualizado la clave principal y la secundaria para la cuenta seleccionada. También puede seleccionar una escala de tiempo en el gráfico y ver la fecha y hora de actualización de la clave.

1. Para ver con más detalles qué clave se ha cambiado, seleccione la opción **Aplicar división**. Seleccione **KeyType** y establezca las propiedades **Limitar** y **Ordenar**. Ahora el gráfico se divide por actualizaciones de clave principal y secundaria, como se muestra en la siguiente imagen:

   :::image type="content" source="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png" alt-text="Gráfico de métricas cuando se actualizan las claves principales y secundarias" border="true" lightbox="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png":::

## <a name="configure-alerts-for-a-key-update"></a>Configuración de alertas para una actualización de claves

Es posible que quiera supervisar las actualizaciones de claves de una cuenta. Cuando se gira o actualiza una clave, es necesario actualizar las aplicaciones cliente dependientes para que puedan seguir funcionando. Mediante la configuración de alertas, puede recibir notificaciones cuando se actualiza una clave.

Siga las instrucciones del artículo [Creación de una alerta](create-alerts.md) con algunos cambios. Al seleccionar la condición de alerta, elija la señal **Claves de cuenta actualizadas**. Seleccione la dimensión **KeyType** y elija la clave **Primary** (Principal) o **Secondary** (Secundaria). En función del tipo de clave que seleccione se desencadena una alerta cuando se actualiza la clave.

En la captura de pantalla siguiente se muestra cómo configurar una alerta de tipo crítico cuando se actualizan las claves de cuenta:

:::image type="content" source="./media/monitor-account-key-updates/configure-key-update-alert.png" alt-text="Configuración de una alerta para recibir una notificación por correo electrónico cuando se actualizan las claves de cuenta":::

## <a name="next-steps"></a>Pasos siguientes

* Supervise los datos de Azure Cosmos DB mediante la [configuración de diagnóstico](cosmosdb-monitor-resource-logs.md) en Azure.
* [Auditoría de las operaciones del plano de control de Azure Cosmos DB](audit-control-plane-logs.md)