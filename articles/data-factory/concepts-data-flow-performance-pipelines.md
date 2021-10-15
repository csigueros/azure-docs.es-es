---
title: Optimización del rendimiento de la canalización en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre la optimización de la ejecución de flujos de datos en canalizaciones de Azure Data Factory y Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 3545d7e8bbf8131c9fe454b39ea57a23cd233264
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293525"
---
# <a name="using-data-flows-in-pipelines"></a>Uso de flujos de datos en canalizaciones 

Al crear canalizaciones complejas con varios flujos de datos, el flujo lógico puede tener un gran impacto en el tiempo y el costo. En esta sección se trata el impacto de las distintas estrategias de arquitectura.

## <a name="executing-data-flows-in-parallel"></a>Ejecución de flujos de datos en paralelo

Si se ejecutan varios flujos de datos en paralelo, el servicio pone en marcha clústeres de Spark independientes para cada actividad. Esto permite que cada trabajo se aísle y se ejecute en paralelo, pero hará que se ejecuten varios clústeres al mismo tiempo.

Si los flujos de datos se ejecutan en paralelo, se recomienda no habilitar la propiedad de período de vida de Azure IR, ya que dará lugar a varios grupos semiactivos no usados.

> [!TIP]
> En lugar de ejecutar el mismo flujo de datos varias veces para cada actividad, almacene provisionalmente los datos en un lago de datos y use rutas de acceso con caracteres comodín para procesar los datos en un único flujo de datos.

## <a name="execute-data-flows-sequentially"></a>Ejecución secuencial de flujos de datos

Si ejecuta las actividades de flujo de datos en secuencia, se recomienda establecer un TTL en la configuración de Azure IR. El servicio vuelve a usar los recursos de proceso, lo que da lugar a un tiempo de actividad del clúster más rápido. Cada actividad seguirá aislada y recibiendo un nuevo contexto de Spark para cada ejecución. Para reducir aún más el tiempo entre actividades secuenciales, active la casilla **Quick re-use** (Reutilización rápida) en Azure IR para indicar al servicio que vuelva a usar el clúster existente.

## <a name="overloading-a-single-data-flow"></a>Sobrecarga de un único flujo de datos

Si coloca toda la lógica dentro de un único flujo de datos, el servicio va a ejecutar todo el trabajo en una sola instancia de Spark. Aunque esto puede parecer una manera de reducir los costos, combina distintos flujos lógicos y puede ser difícil de supervisar y depurar. Si se produce un error en un componente, también se producirá un error en todas las demás partes del trabajo. Se recomienda organizar los flujos de datos por flujos independientes de lógica de negocios. Si el flujo de datos es demasiado grande, la división en componentes independientes facilitará la supervisión y la depuración. Aunque no hay ningún límite en el número de transformaciones de un flujo de datos, tener demasiados hará que el trabajo sea complejo.

## <a name="execute-sinks-in-parallel"></a>Ejecución de receptores en paralelo

El comportamiento predeterminado de los receptores de flujo de datos es ejecutar cada receptor de forma secuencial, en serie, y producir un error en el flujo de datos cuando se encuentra un error en el receptor. Además, todos los receptores se establecen de forma predeterminada en el mismo grupo, a menos que vaya a las propiedades del flujo de datos y establezca otras prioridades para los receptores.

Los flujos de datos permiten agrupar los receptores en grupos en la pestaña de propiedades del flujo de datos en el diseñador de la interfaz de usuario. Puede establecer el orden de ejecución de los receptores y agruparlos con el mismo número de grupo. Para facilitar la administración de los grupos, puede pedir al servicio que ejecute los receptores del mismo grupo en paralelo.

En la actividad Ejecutar flujo de datos de la canalización, en la sección "Propiedades del receptor" hay una opción para activar la carga del receptor en paralelo. Cuando se habilita "Ejecutar en paralelo", se indica a los flujos de datos que escriban en los receptores conectados al mismo tiempo en lugar de hacerlo de forma secuencial. Para usar la opción de ejecución en paralelo, los receptores deben agruparse y conectarse al mismo flujo mediante una nueva rama o la división condicional.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al rendimiento del flujo de datos](concepts-data-flow-performance.md)
- [Optimización de orígenes](concepts-data-flow-performance-sources.md)
- [Optimización de receptores](concepts-data-flow-performance-sinks.md)
- [Optimización de transformaciones](concepts-data-flow-performance-transformations.md)

Vea el resto de artículos sobre Data Flow relacionados con el rendimiento:

- [Actividad Data Flow](control-flow-execute-data-flow-activity.md)
- [Supervisión del rendimiento de flujo de datos](concepts-data-flow-monitoring.md)
- [Rendimiento de Microsoft Integration Runtime](concepts-integration-runtime-performance.md)
